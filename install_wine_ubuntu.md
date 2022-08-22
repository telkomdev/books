## Install Wine Ubuntu

## Setup PPA, and enable 32-bit architecture

```shell
$ sudo dpkg --add-architecture i386
$ wget -qO - https://dl.winehq.org/wine-builds/winehq.key | sudo apt-key add -
```

## enable the Wine apt repository

###  Ubuntu 19.10 
```shell
$ sudo apt-add-repository 'deb https://dl.winehq.org/wine-builds/ubuntu/ eoan main'
```

###  Ubuntu 18.04 
```shell
$ sudo apt-add-repository 'deb https://dl.winehq.org/wine-builds/ubuntu/ bionic main'
$ sudo add-apt-repository ppa:cybermax-dexter/sdl2-backport
```

###  Ubuntu 16.04 
```shell
$ sudo apt-add-repository 'deb https://dl.winehq.org/wine-builds/ubuntu/ xenial main'
```
Install

```shell
$ sudo apt install aptitude
$ sudo aptitude install winehq-stable
```

Test
```shell
$ wine --version
```