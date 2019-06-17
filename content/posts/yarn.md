---
title: "Yarn"
date: 2019-06-17T22:13:51+09:00
draft: false
tags: [ "yarn" ]
---

[Yarn](https://yarnpkg.com/en/)

## Install

```
curl -sS https://dl.yarnpkg.com/debian/pubkey.gpg | sudo apt-key add -
echo "deb https://dl.yarnpkg.com/debian/ stable main" | sudo tee /etc/apt/sources.list.d/yarn.list
sudo apt-get update && sudo apt-get install --no-install-recommends yarn
```

`sudo apt-get install yarn` instead of `sudo apt-get install --no-install-recommends yarn` if Node is installed already.
