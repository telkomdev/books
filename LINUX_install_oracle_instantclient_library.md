# How to install Oracle instantclient library
## Debian based OS
### Install prerequisites package
```
$ sudo apt-get update -y \
  && sudo apt-get install -y wget unzip
```
### Download latest Oracle instantclient
```shell
$ wget https://download.oracle.com/otn_software/linux/instantclient/216000/instantclient-basic-linux.x64-21.6.0.0.0dbru.zip -O instantclient.zip
```
### Install & configure instantclient
```shell
$ unzip instantclient.zip \
  && mkdir -p /opt/oracle/instantclient \
  && mv instantclient_*/* /opt/oracle/instantclient/ \
  && echo "/opt/oracle/instantclient" > /etc/ld.so.conf.d/oracle-instantclient.conf \
  && sudo ldconfig
```
