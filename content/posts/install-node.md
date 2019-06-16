---
title: "Install Node"
date: 2019-06-16T11:44:37+09:00
draft: false
---

Install NodeJS as usual.

```
$ sudo apt install -y nodejs npm
$ node -v
v10.15.2
$ npm -v
5.8.0
```

Install `n` if you want the latest version, then use `n` to install latest (stable) version of Node.

```
$ sudo npm install n -g
$ sudo n stable
```

Remove NodeJS and npm installed by apt.

```
$ sudo apt purge -y nodejs npm
$ exec $SHELL -l
$ node -v
v10.16.0
$ npm -v
6.9.0
```

Use `n` to change versions.

```
$ sudo n 9.11.2

     install : node-v9.11.2
       mkdir : /usr/local/n/versions/node/9.11.2
       fetch : https://nodejs.org/dist/v9.11.2/node-v9.11.2-linux-x64.tar.gz
   installed : v9.11.2
$ node -v
v9.11.2
```

### Aliases

* latest
* stable
* lts
