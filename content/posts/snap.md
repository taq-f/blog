---
title: "Snap"
date: 2019-05-26T14:12:29+09:00
draft: false
tags: [ "snap" ]
---

## List installed packages

```text
$ snap list
Name                  Version                     Rev   Tracking  Publisher     Notes
code                  a622c65b                    7     stable    vscode✓       classic
core                  16-2.39                     6964  stable    canonical✓    core
core18                20190508                    970   stable    canonical✓    base
gitkraken             5.0.4                       138   stable    gitkraken✓    -
```

## Install package

```
sudo snap install gitkraken
```

## Update package

```text
sudo snap refresh gitkraken
```