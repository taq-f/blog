---
title: "Github SSH"
date: 2019-06-16T12:46:52+09:00
draft: false
---

## Generate key pair

```
$ cd ~/.ssh
$ ssh-keygen -t rsa
$ ls
id_rsa  id_rsa.pub
```

## Upload public key

visit `https://github.com/settings/ssh` and click `New SSH key`.

Fill Title and Key and click `Add SSH Key`. `Title` can be anything to identify from where you connect to Github and `Key` is the content of `id_rsa.pub`

## Test

```
$ ssh -T git@github.com
The authenticity of host 'github.com (13.114.40.48)' can't be established.
RSA key fingerprint is SHA256:nThbg6kXUpJWGl7E1IGOCspRomTxdCARLviKw6E5SY8.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added 'github.com,13.114.40.48' (RSA) to the list of known hosts.
Hi taq-f! You've successfully authenticated, but GitHub does not provide shell access.
```

The command above looks for key files in default location; `~/.ssh/id_rsa`, `~/.ssh/id_dsa` or `~/.ssh/identity`.

For custom location, create `~/.ssh/config` file with content like

```
Host github github.com
  HostName github.com
  IdentityFile ~/.ssh/id_git_rsa
  User git
```

## Github!

See the output of `git config remote.origin.url` of a repository. If it begins with `https`, for example `https://github.com/taq-f/blog.git`, the repository uses https instead of ssh. Update remote url by `git remote set-url origin git@github.com:taq-f/blog.git`
