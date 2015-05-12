---
layout: post
title: Quick Start for virtualenv and virtualenvwrapper
---

**virtualenv** is defined as a Virtual Python Environment builder. Actually it is a tool used to create isolated python environments. It creates an environment that has its own installation directories, that doesn't share libraries with other virtualenv environments.

**virtualenvwrapper** is a set of extensions to Ian Bicking’s virtualenv tool. The extensions include wrappers for creating and deleting virtual environments and otherwise managing your development workflow, making it easier to work on more than one project at a time without introducing conflicts in their dependencies.

To use virtualenvwrapper, *python* and *pip* should definitely be installed in your OS (currently my linux distribution is debian with kernel 3.16).

For the installation, [here](https://virtualenvwrapper.readthedocs.org/en/latest/index.html) and [here](https://pypi.python.org/pypi/virtualenv) are all both reference. 

When everything is installed, then we should know how to use it, specifically, we need to answer the following questions:

- How to create a new virtual environment
- How to work in it
- How to exit
- How remove a virtual environment

Here are the answers:

```
$ mkvirtualenv env // create a new one
$ workon // list all virtual environments 
$ workon  env // get into environment env
$ deactivate // exit 
$ mkvirtualenv env // remove
```

> **Note**

>- The virtual environment is not created in some specific location, i.e, if your current directory is /Desktop/, then you type $mkvirtualenv env, it does not mean your env is created on desktop.

>- Actually, no matter what directory you are in, you could always list all working virtual environments by “workon” command, and then get into it. You could also switch into another environment, even you are already in one. 












