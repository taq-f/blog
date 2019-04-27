---
title: "Git Submodule"
date: 2018-09-24T12:47:28+09:00
tags: [ "git" ]
---

## Add

`git submodule add -b master https://github.com/taq-f/taq-f.github.io submoduledir`

```
$ ls -la
合計 16
drwxrwxr-x 3 taq-f taq-f 4096  9月 24 12:50 .
drwxrwxr-x 3 taq-f taq-f 4096  9月 24 12:50 ..
drwxrwxr-x 8 taq-f taq-f 4096  9月 24 12:50 .git
-rw-rw-r-- 1 taq-f taq-f    9  9月 24 12:50 testfile.txt
$ git status
ブランチ master
nothing to commit, working tree clean
$ git submodule add -b master https://github.com/taq-f/taq-f.github.io submoduledir
Cloning into '/home/taq-f/development/work/test/submoduledir'...
remote: Counting objects: 428, done.
remote: Compressing objects: 100% (240/240), done.
remote: Total 428 (delta 185), reused 383 (delta 143), pack-reused 0
Receiving objects: 100% (428/428), 5.21 MiB | 5.66 MiB/s, done.
Resolving deltas: 100% (185/185), done.
$ ls -la
合計 24
drwxrwxr-x  4 taq-f taq-f 4096  9月 24 12:53 .
drwxrwxr-x  3 taq-f taq-f 4096  9月 24 12:50 ..
drwxrwxr-x  9 taq-f taq-f 4096  9月 24 12:53 .git
-rw-rw-r--  1 taq-f taq-f  113  9月 24 12:53 .gitmodules
drwxrwxr-x 12 taq-f taq-f 4096  9月 24 12:53 submoduledir
-rw-rw-r--  1 taq-f taq-f    9  9月 24 12:50 testfile.txt
```

.gitmodules

```text
[submodule "submoduledir"]
	path = submoduledir
	url = https://github.com/taq-f/taq-f.github.io
	branch = master
```

## Delete

```
$ git submodule deinit themes/my-hugo-theme
Cleared directory 'themes/my-hugo-theme'
Submodule 'themes/my-hugo-theme' (https://github.com/taq-f/my-hugo-theme) unregistered for path 'themes/my-hugo-theme'
$ git rm themes/my-hugo-theme
rm 'themes/my-hugo-theme'
```
