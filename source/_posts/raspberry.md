---
title: 树莓派 Cheatsheet
---
# 关于树莓派

如需要在家中设立数字中心（智能家居，文件共享，内网穿透等），内网服务器是必然选择，无论是使用Mac Mini或是PC作为内网服务中枢都大材小用，能耗过高，效率过低。
树莓派必然是最佳选择。

## 树莓派的选择

树莓派4具有8GB与4GB版本，目前看来，家中所需要使用到的服务多数并没有高内存需求，4GB版本足矣应付多个服务共存的需求

## 刷机

需要一张存储卡

* https://www.raspberrypi.org/software/
* 下载并使用官方提供的工具将Raspberry刷入存储卡

## 开启ssh

* Raspberry默认开启ssh
* https://magpi.raspberrypi.org/articles/ssh-remote-control-raspberry-pi

## 使用SSH Key授权ssh

* https://www.ssh.com/ssh/copy-id
* 使用ssh-copy-id命令快捷将本机的ssh key设置到Raspberry OS中

## 存储卡转移

由于刚开始赠送的32GB在安装了多个服务后会存在不够用的情况，如果需要将整盘转移到新的SD卡中，可以使用官方提供的备份文档。

* https://www.raspberrypi.org/documentation/linux/filesystem/backup.md
* dd命令
```bash
sudo dd bs=4M if=/dev/sdb | gzip > PiOS.img.gz
```
```bash
gunzip --stdout PiOS.img.gz | sudo dd bs=4M of=/dev/sdb
```

## dd命令进度显示

```text
watch -n 1 pkill -USR1 -x dd
```

## dd命令后存储容量缩水

```
sudo raspi-config 
->Advanced Options 
->Expand Filesystem
```

## 搭建SMB服务

https://hub.docker.com/r/trnape/rpi-samba/

##  搭建 Aria2

https://hub.docker.com/r/wahyd4/aria2-ui/

## 搭建Logitech Media Server

Homepod 接入 HA

https://hub.docker.com/r/lmscommunity/logitechmediaserver

### Airplay Bridge

* http://downloads.sourceforge.net/project/lms-plugins-philippe44/dev/repo-sf.xml