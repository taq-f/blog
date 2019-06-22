---
title: "Apache Beam"
date: 2019-06-22T19:26:12+09:00
draft: false
---

Walkthrough of Apache Beam GET STARTED documentation.

## Python version

Python 2.7.x is required.

```
$ python --version
Python 2.7.16
$ pip --version
pip 18.1 from /usr/lib/python2.7/dist-packages/pip (python 2.7)
```

Do `pip install --upgrade pip` if pip is not installed. Make sure virtualenv is installed too (`pip install --upgrade virtualenv` if it is not).

## Create a virtual environment

Create a virtual environment by virtualenv command, and activate it. virtualenv might not be on PATH.

```
mkdir beam
cd beam
virtualenv .
. bin/activate
```

## Install Apache Beam

This must be done after activating the virtual envuronment.

```
(beam)$ pip install apache-beam
```

## Run examples

`apache-beam` contains examples. Let's try word count example, which takes a file path as an input and writes the result.

Prepare a text file (input/sample.txt) which contains sentences, then run the following example.

```
(beam)$ python -m apache_beam.examples.wordcount --input input/sample.txt --output out.txt
(beam)$ cat out.txt-00000-of-00001
be: 1
All: 1
required: 1
run: 1
in: 1
can: 1
many: 1
locally: 1
the: 2
Beam: 1
script: 1
example: 1
directory: 1
described: 1
Apache: 1
passing: 1
The: 1
has: 1
by: 1
arguments: 1
examples: 3
```
