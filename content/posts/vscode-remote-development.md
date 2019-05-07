---
title: "Vscode Remote Development"
date: 2019-05-07T22:45:04+09:00
draft: true
---

[Overview](https://code.visualstudio.com/docs/remote/remote-overview)

まだStableには入っていないので、Visual Studio Code Insidersが必要。既存のVSCodeと共存できるので助かる。

## Visual Studio Code Insiders

[Visual Studio Code Insiders](https://code.visualstudio.com/insiders/)

```text
sudo apt install ./code-insiders_1.34.0-1557206682_amd64.deb
```

## Remote Development Extension

[Remote Development](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.vscode-remote-extensionpack)

## Dockerコンテナで試してみる

コマンドパレットから`Remote-Containers: Open Folder in Container...`を実行するとフォルダ選択となるのでどこから選ぶ。

次に環境を選択する。とりあえず今回はPython3にしてみる（選択するとコンテナをビルドしたりし始める。）。

`.devcontainer`ディレクトリが作成されて、その中にRemote Development環境を作成するためのDockerfile等（雛形）が作成される。

```text
$ ls .devcontainer/
Dockerfile  devcontainer.json  requirements.txt.temp  settings.vscode.json
```

次にこのディレクトリ（ワークスペース）を開くと、勝手に`.devcontainer`内の定義に従ってコンテナが立ち上がりアタッチされる。

確かに動いている

```
$ docker ps
CONTAINER ID        IMAGE                                                     COMMAND                  CREATED             STATUS              PORTS               NAMES
e4935d4bafff        vsc-remote-development-18cf7d4b9a461adea94c2817af0e9fb0   "/bin/sh -c 'echo Co…"   16 seconds ago      Up 14 seconds                           hardcore_chatelet
```

### なにか書いてみる

コンテナ側に（VSCodeの）Pythonの拡張機能がインストールされているようなので、

```
$ cat devcontainer.json 
// See https://aka.ms/vscode-remote/containers for the
// documentation about the devcontainer.json format
{
	"name": "Python 3",
	"context": "..",
	"dockerFile": "Dockerfile",
	"workspaceFolder": "/workspace",
	"extensions": [
		"ms-python.python"
	]
}
```

とりあえずPythonファイルを作成する。

![image](images/vscode-remote-development/2019-05-07_23-25-43.png)

ちゃんと補完が効く！

![image](images/vscode-remote-development/2019-05-07_23-31-56.png)

ブレイクポイントつけて`F5`でデバッグもできるー！

![image](images/vscode-remote-development/2019-05-07_23-35-08.png)

ローカルには何も入れなくて良くてラク。開発環境がWindowsだったりすると、上手くインストールできなかったり、インストールにひと手間必要だったりするライブラリが入った環境が手軽に作成できそうで良さそう。

### pip?

VSCodeのTerminal開くとコンテナのbashなので、ここで`pip install`してみることにする。

![image](images/vscode-remote-development/2019-05-07_23-44-01.png)

Dockerfileを見ると、プロジェクトルートにあるrequiements.txtを`pip install`してくれるようなので、カレントにrequirements.txtを作成する。

```
$ pip install numpy
$ pip freeze > requirements.txt
$ cat requirements.txt
astroid==2.2.5
isort==4.3.18
lazy-object-proxy==1.3.1
mccabe==0.6.1
numpy==1.16.3
pylint==2.3.1
six==1.12.0
typed-ast==1.3.5
wrapt==1.11.1
```

コマンドパレットから`Remote Containers: Rebuild Container`すればいいと思いきやエラーとなった。ひとまずVSCodeを落として、コンテナ削除して、イメージ削除してVSCode再起動すると再度ビルドが走ってちゃんとrequirements.txtのモジュールがインストールされていた！この辺は流石にめんどいので要調査。

![image](images/vscode-remote-development/2019-05-08_00-20-47.png)
