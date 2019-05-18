---
title: "Vscode Remote Dev Java Web"
date: 2019-05-18T09:47:40+09:00
draft: false
---

## Lombok

Add `GabrielBB.vscode-lombok` to `.devcontainer/devcontainer.json`

```json
{
	"name": "Java 11",
	"dockerFile": "Dockerfile",
	"extensions": [
		"vscjava.vscode-java-pack",
		"GabrielBB.vscode-lombok"
	]
}
```

Restarting VSCode or recreating the container might be required.

## Debug

Start debug server

```
gradlew appRunDebug
```

Create launch setting: `Java: Attach to Remote Program`. Set `locaohost` to hostName and `5005` to port.

```json
{
    "version": "0.2.0",
    "configurations": [
        {
            "type": "java",
            "name": "Debug (Attach) - Remote",
            "request": "attach",
            "hostName": "localhost",
            "port": 5005
        },
        {
            "type": "java",
            "name": "Debug (Launch) - Current File",
            "request": "launch",
            "mainClass": "${file}"
        }
    ]
}
```

Then, debug!!
