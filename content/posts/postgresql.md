---
title: "Postgresql"
date: 2019-05-30T22:19:51+09:00
draft: false
tags: [ "postgresql" ]
---

```text
# cat /etc/lsb-release
DISTRIB_ID=Ubuntu
DISTRIB_RELEASE=18.04
DISTRIB_CODENAME=bionic
DISTRIB_DESCRIPTION="Ubuntu 18.04.2 LTS"
```

## Preparation

```text
# apt-get install -y vim wget
# vim /etc/login.defs  <-- add "CREATE_HOME yes"
# useradd postgres
# passwd postgres
```

## Install from Source

```text
# apt-get install -y make gcc libreadline6-dev zlib1g-dev
# cd /tmp
# wget https://ftp.postgresql.org/pub/source/v9.6.13/postgresql-9.6.13.tar.gz
# tar xzvf postgresql-9.6.13.tar.gz
# cd postgresql-9.6.13
# ./configure
# make
 .
 .
 .
All of PostgreSQL successfully made. Ready to install.
# make install
 .
 .
 .
PostgreSQL installation complete.
```

Default install directory is `/usr/local/pgsql`. Change install directory by adding `--prefix=/your/path` option.

## Environment Settings

```text
# su - postgres
$ bash
$ vim ~/.bash_profile
$ cat ~/.bash_profile
export PATH=/usr/local/pgsql/bin:$PATH
export LD_LIBRARY_PATH=/usr/local/pgsql/lib:LD_LIBRARY_PATH
```

## Create a Database Cluster

```text
$ initdb -D /home/postgres/data --no-locale
 .
 .
 .
Success. You can now start the database server using:

    pg_ctl -D /home/postgres/data -l logfile start
```

## Start and Stop PostgreSQL

Start

```text
$ pg_ctl start -D /home/postgres/data -w
 .
 .
 .
LOG:  database system is ready to accept connections
LOG:  autovacuum launcher started
 done
server started
```

Test

```text
$ psql
psql (9.6.13)
Type "help" for help.

postgres=# select now();
              now
-------------------------------
 2019-05-30 13:53:12.132528+00
(1 row)

postgres=# \q
```

Stop

```text
$ pg_ctl stop -D /home/postgres/data
 .
 .
 .
 done
server stopped
```
