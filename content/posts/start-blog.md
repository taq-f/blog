---
title: "Start a Blog"
date: 2018-09-22T23:34:39+09:00
draft: false
categories: [ "blog" ]
tags: [ "blog", "hugo", "github" ]
---

いまさら感があるけど、github pages + Hugoでブログを始めるメモ（ブログじゃなくてもHugoで生成する静的サイトなら別になんでも）。このブログを作ったときのメモ。

## Hugoのインストール

Ubuntu環境なので[公式のインストール手順](https://gohugo.io/getting-started/installing/#linux)を参考にsnapでインストール。

```
$ snap install hugo --channel=extended
$ hugo version
Hugo Static Site Generator v0.47.1/extended linux/amd64 BuildDate: 2018-08-24T17:21:56Z
```

## サイト作成（練習）

Githubで公開する前にローカルでサイト作成の練習（チュートリアル）。[公式の「Create a New Site」](https://gohugo.io/getting-started/quick-start/#step-2-create-a-new-site)以降の手順をやってみておくとなんとなくHugoでのサイトの作り方がわかる。

## Githubで公開

やり方はまんまこれ。ユーザーページ前提で進める。

https://gohugo.io/hosting-and-deployment/hosting-on-github/

1. `taq-f.github.io`という名前のリポジトリを作る。ここにHugoでビルドしたファイルを置く（-> `https://taq-f.github.io`に公開される）。
1. `blog`リポジトリを別途作る（名前は自由）。ここにブログ原稿（とHugoがビルドするための諸々のファイル）を置く。原則自分で編集するのはこっちのリポジトリ。
1. `git clone git@github.com:taq-f/blog.git`
1. `hugo new site blog && cd blog`
1. `hugo new posts/my-first-post.md`
1. `hugo server -D`して初版公開OKのところまで持っていく。
1. `rm -rf public`してpublicディレクトリを消しておく
    * ここにビルドしたファイル達が生成されるけど、そのファイルは別リポジトリ（最初に作った`taq-f.github.io`で管理したいので、その前準備）
1. `git submodule add -b master git@github.com:taq-f/taq-f.github.io.git public`。`public`ディレクトリに`taq-f.github.io`リポジトリをサブモジュールで追加する。こうすることで、`hugo`コマンドで生成されるファイルは`public`に置かれ、別originを持てる。
1. これでgit周りの設定は終わり。

ビルド -> `public`ディレクトリ側で変更をcommit&push をスクリプト化しておくと便利、とのことで以下のようなスクリプトを`blog`直下に置いておく。

deploy.sh

```bash
#!/bin/bash

echo -e "\033[0;32mDeploying updates to GitHub...\033[0m"

# Build the project.
hugo # if using a theme, replace with `hugo -t <YOURTHEME>`

# Go To Public folder
cd public
# Add changes to git.
git add .

# Commit changes.
msg="rebuilding site `date`"
if [ $# -eq 1 ]
  then msg="$1"
fi
git commit -m "$msg"

# Push source and build repos.
git push origin master

# Come Back up to the Project Root
cd ..
```

あとは実行できるようにしておけばOK。

```
$ chmod u+x deploy.sh
$ ./deploy.sh
```

多分`blog`リポジトリのpushフックで実行すればまずはいい感じ？いけた！

```
$ cp -a .git/hooks/pre-push.sample .git/hooks/pre-push
$ cat .git/hooks/pre-push
#!/bin/sh

# An example hook script to verify what is about to be pushed.  Called by "git
# push" after it has checked the remote status, but before anything has been
# pushed.  If this script exits with a non-zero status nothing will be pushed.
#
# This hook is called with the following parameters:
#
# $1 -- Name of the remote to which the push is being done
# $2 -- URL to which the push is being done
#
# If pushing without using a named remote those arguments will be equal.
#
# Information about the commits which are being pushed is supplied as lines to
# the standard input in the form:
#
#   <local ref> <local sha1> <remote ref> <remote sha1>
#
# This sample shows how to prevent push of commits where the log message starts
# with "WIP" (work in progress).

./deploy.sh

exit 0
$
$
$ git push
Deploying updates to GitHub...
Building sites … DEPRECATION WARNING on line 40, column 36 of stdin:
The value "#ffffff80" is currently parsed as a string, but it will be parsed as a color in
future versions of Sass. Use "unquote('#ffffff80')" to continue parsing it as a string.


                   | EN
+------------------+----+
  Pages            | 19
  Paginator pages  |  0
  Non-page files   |  0
  Static files     | 73
  Processed images |  0
  Aliases          |  6
  Sitemaps         |  1
  Cleaned          |  0

Total in 75 ms
[master 2c9bb58] rebuilding site Sun Sep 23 00:59:38 JST 2018
 13 files changed, 112 insertions(+), 28 deletions(-)
Counting objects: 23, done.
Delta compression using up to 8 threads.
Compressing objects: 100% (22/22), done.
Writing objects: 100% (23/23), 2.86 KiB | 1.43 MiB/s, done.
Total 23 (delta 15), reused 0 (delta 0)
remote: Resolving deltas: 100% (15/15), completed with 12 local objects.
To github.com:taq-f/taq-f.github.io.git
   845bfc4..2c9bb58  master -> master
Counting objects: 9, done.
Delta compression using up to 8 threads.
Compressing objects: 100% (7/7), done.
Writing objects: 100% (9/9), 1.33 KiB | 1.33 MiB/s, done.
Total 9 (delta 5), reused 0 (delta 0)
remote: Resolving deltas: 100% (5/5), completed with 3 local objects.
To github.com:taq-f/blog.git
   e889790..9af48fb  master -> master
```
