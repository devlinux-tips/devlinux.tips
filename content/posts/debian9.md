---
title: "Debian 9: VPS post install"
date: 2019-05-11T15:41:42+02:00
draft: true
toc: false
images:
tags:
  - debian
  - vps
---

# Debian 9

## Update system

After booting into fresh Debian 9 VPS, first do upate of the system.


> Default Debian 9 user is *root*, and as long terminal commands are not starting with *sudo* this means that are run natively by *root* user but if commands are starting with *sudo*, they are running by elavated user.
{.is-info}


```sh
apt-get update
apt-get upgrade
```


Install *vim* and *sudo* and completition for bash (if using bash).


```sh
apt-get install -y vim sudo bash-completion
```


> On Debian 9 VPS's I have used *vim* is not installed by default, but *nano* is.
{.is-info}


Run `visudo` and make sure that line *%sudo   ALL=(ALL:ALL) ALL* is not commentted out.


> On Debian 9 VPS's I have used, default sudo editor is *nano*, it can be changed but there is no need to.
{.is-info}

## Create elavated user

Create elavated user so *root* will be used as minimal as possible. Newly created user will be able to use most *root* can do via *sudo* command.

* Create *group* for use with same name as user
* Create user
* Make newly created group as its default group, add it to *sudo* group
* Set *bash* as default shell for user

> Chose a hard to crack password, with more than 8 characters, having uppercase character(s), symbol(s) and number(s)
{.is-warning}


```sh
# change 'x' to wanted name of user/group
groupadd x
useradd -m -g x -G sudo -s /bin/bash x
passwd x
```


> Other shells can be used *zsh*, *fish*, *csh*, etc. Make sure wanted shell is installed.
{.is-info}

* Print user and its group ids.
* Switch to newly created user.
* Change directory to users home
* Print current directory

```sh
# change 'x' name of created user
id x
su x
cd
pwd
```

## Shell alias

Add for both users *root* and elavated user in `.bashrc` mostly used commands as alias to simplify typing

```sh
cd
vim .bashrc
```

At the end of file or if there are some aliases commentted or not add your own aliases

* list all files (with hidden), with permissions as details list
* opens vim
* prints RAM usage in readable format
* print filesystem usage in readable format
* size of folder or file, depending on what is used

```
alias la='ls -al'
alias v='vim'
alias free='free -m'
alias df='dg -h'
alias du='du -sh'
```


## SSH keys

Adding to your elavated user private and public key used by secure entering VPS via SSH.

> TODO: link on how to create SSH keys
{.is-warning}

* Create `.ssh` folder if not exists
* Create private and public key file

> `$HOME` is environment variable that points to home directory of currently logged in user.
{.is-info}

```sh
mkdir $HOME/.ssh
touch $HOME/.ssh/id_rsa
touch $HOME/.ssh/id_rsa.pub
```

Change permissions of folder and private/public key files

```sh
chmod 700 $HOME/.ssh
chmod 600 $HOME/.ssh/id_rsa
chmod 644 $HOME/.ssh/id_rsa.pub
```

Copy and paste private and public key from your local machine used for SSH to VPS.

> In Unix/Linux machines keys can be found in `/home/<user>/.ssh` and for Windows 7 and newer in `C:\Users\<user>\.ssh`. And usually named *id_rsa* (private key) and *id_rsa.pub* (public key).
{.is-info}

```sh
# paste private key
vim $HOME/.ssh/id_rsa
# paste public key
vim $HOME/.ssh/id_rsa.pub
```

Create and add your public key to authorized keys file and make it more secure.

```sh
cat $HOME/.ssh/id_rsa.pub > $HOME/.ssh/authorized_keys
chmod 600 $HOME/.ssh/authorized_keys
```

> Getting keys can be accomplished by using `rsync`, by copying from your own machine to VPS
{.is-info}

## SSH config

Usually entering VPS SSH by default is with user *root* and some set password by VPS provider. It is usually wise to change SSH port (default is *22*) and use public key authentication instead of password.
