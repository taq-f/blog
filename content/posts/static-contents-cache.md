---
title: "Static Contents Cache"
date: 2018-09-23T20:54:28+09:00
draft: false
---

Webアプリケーションでは、画面動作制御が複雑になるとJavaScrpitファイルなどのサイズが大きくなりがち。なのでスクリプトやスタイルシートなどの静的リソースは可能な限りブラウザのキャッシュを活用したい。でも変更したらブラウザ側で特別なことをしなくても再取得して欲しい。「直ってなくない？」「あれ？ctrl + F5してみてください」「直った」をしたくないし、ユーザーにも意識させたくない。

そんな時のベストプラクティスの模索。

## Metaタグに指定

```html
<meta content="no-cache" http-equiv="Pragma" />
<meta content="no-cache" http-equiv="Cache-Control" />
<meta content="0" http-equiv="Expires" />
```

HTML4までは上のようにキャッシュに関する指定（特にキャッシュさせない時ではあるが）を書いていたが、HTML5からは使用してはならない。"ユーザーエージェントに依ってHTTPレベルでの挙動の違いが出てしまうので" とのこと。

https://www.w3.org/TR/html5/document-metadata.html#pragma-directives

実際に指定があっても、モダンなブラウザは無視してキャッシュしているようだった。

**なので、この制御方法は今後使用しない。**

## Etag, Last-Modified

コンテンツのレスポンスヘッダ。これが指定されていると、次回のリクエスト時にこの情報を送信し、サーバー側は当該のコンテンツの更新日付や識別子(Etag)が変化していたらファイルを送信する(Status Code 200)。変更がない場合、ファイルは返さず304(Not Modified)を返し、ブラウザ側は自身のキャッシュを利用する。

**ファイルの更新確認のために一度httpリクエストは発行される。**

Web、アプリケーションサーバーに依ってはファイルを更新しても上手く反映されなかったりするようだった。また、Metaタグ指定とのカラミか分からないが、ブラウザ側から、キャッシュしているファイルのタイムスタンプを送っていないリクエストもあった。そうすると、再取得の判断が狙った通りにならない懸念があるので、**この方法はあまり使いたくない**。

## Expires

これもコンテンツのレスポンスヘッダ。"Expires" を設定することで、ブラウザは指定日付までは当該ファイルをサーバーに取得しに行かない。これはかなり強い指定で、いくらサーバー側でそのファイルを更新していたとしても同じURLでExpire前ならばリクエストを飛ばさない。リクエスト自体が発行されないので、Etag等より効果的（EtagやLast-Modifiedより優先される）。

同じ意味の`Cache-Control: max-age`があるが、広くサポートされている`Expires`の方が推奨されている。ので`Expires`を使う。1週間以上、最大で1年先を設定するのが推奨されているらしい。1年以上先はRFCのガイドラインに違反するのでやめたほうがよさそう。

では、静的コンテンツに変更があり再度リクエストして欲しい時はどうする？

1. 静的コンテンツのURLにクエリパラメータを付加する
1. 静的コンテンツのファイル名を一意にする

### クエリパラメータ

```
http://localhost:5000/static/main.css?q=1234567890
```

ファイル内容が更新されたら`q=`の値が変わるようにする。URLが変わることでExpiresが効かなくなり取得し直してくれる。正常に動作するが、プロキシサーバーに依ってはクエリパラメータが付いているものはそもそもキャッシュしないことが多いよう。狙った動作とならない環境があり、良く見かける対処であるが**推奨されていない**。

### ファイル名

```
http://localhost:5000/static/main-1234567890.css
```

ファイル名自体にフィンガープリントを追加する。クエリパラメータと同様にURLが変わる（そもそもリクエストするファイルが変わる）ことで取得しに来てもらえる。

フィンガープリントは、ファイルコンテンツのハッシュが望ましい。例え更新日付が変わったとしても中身が同じならハッシュが同じになり、キャッシュしていて良いので。

若しくは、静的コンテンツをまとめてフィンガープリントとなるフォルダに格納する。ファイル更新の際にはフォルダ名を変更し、再取得を促す。ただし、更新されていないファイルまで対象になるのであまり良い方法ではない。

参考：https://developers.google.com/speed/docs/insights/LeverageBrowserCaching

[GitHub](https://github.com/)を見てみたら、確かにcssファイルなどのExpiresが1年後に設定されており、ファイル名にもハッシュ（らしき）値が埋め込まれていた。

**現状この方式が一番良さそう。**

## 実装（サーバーサイド）

### Flask, Jinja2 (Python)

レスポンスヘッダ

```python
@app.after_request
def static_cache(res):
    """静的コンテンツのレスポンスヘッダにExpiresを設定する。

    この例ではリクエストの度に1年後を計算しているが、実際にはどこかに保存しておいた方が
    良いと思う。GitHubではしばらくは同じ(約1年後の)Expires日付が返ってきた。
    """
    if request.endpoint == 'static':
        expires = datetime.now() + timedelta(days=365)
        res.headers['Expires'] = expires.isoformat()
    return res
```

静的コンテンツの動的取得

```python
@app.route('/', methods=['GET'])
@skip_session_check
def index():
    common_css = os.listdir(os.path.join(app.root_path, 'static'))
    common_css = [url_for('static', filename=f) for f in common_css]

    return render_template('index.html', name='World', common_css=common_css)
```

```html
<head>
  <meta charset="UTF-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge" />
  <title>Flask Sample App</title>
  <meta name="viewport" content="width=device-width, initial-scale=1">
  {% for file in common_css %}
  <link rel="stylesheet" type="text/css" href="{{ file }}">
  {% endfor %}
</head>
```

これはもっといい方法がないものか。html自体もビルドして作る場合はlinkタグもその場で生成しておけばいいかもしれないが、、、

### Jersey, Thymeleaf (Java)

```java
// TODO 特定のフォルダ配下のファイル一式を取得して、そのリストをViewに渡す処理
```

```html
<link rel="stylesheet" type="text/css"
    th:each="file : ${commonCss}" th:object="${file}" th:href="@{'/css/' + ${file}}">
```

## 実装（クライアント）

### Angular

Angularではビルドを行うと、`--output-hashing=none`を指定しない限り各アセットのファイル名にはそれぞれのハッシュ値が付加される。合わせて生成されるhtmlページにもこのハッシュ値を考慮してタグが生成される（-> htmlタグをサーバーサイドで動的に生成する必要はなくなる）。

※Angularでデフォルトでこの挙動ということは、この方式はイケているはず

### 自分で書いている（ビルドしない）スクリプト

TODO
