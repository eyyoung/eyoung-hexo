---
title: 智能家居：Home Assistant
---
## 安装

https://bbs.hassbian.com/thread-4520-1-1.html

国内制作的一键安装脚本可以大幅度降低安装难度。
基于Docker的安装模式基本可以体验到完整的系统，并且大幅度提高了树莓派的利用率。即使在HAOS进程崩溃的情况下也能隔离HAOS与系统其他应用的进程，让其他应用正常运行。

## 目录

安装后Docker会映射宿主与容器目录。

```
# 配置目录
/usr/share/hassio/homeassistant:/config
# media目录
/usr/share/hassio/media:/media
```

可以使用
```
docker inspect homeassistant
```
检查容器启动相关信息
也可以通过
```
docker exec -it homeassistant bash
```
进入容器内部

## 基本概念

以下简称Home Assistant为HA. 

### 平台(Platform)

设备被抽象成平台，如开关对应switch, 空调对应climate, 风扇对应fan，每个平台有特定的接口需要实现，如switch需要实现turn_on, turn_off. 不同的设备接入HA只需要实现各个平台需要的接口。

HA内置了部分平台，如switch, climate, lights等，也可以通过安装其他集成扩展更多平台。

https://developers.home-assistant.io/docs/creating_platform_index/

### 集成

HA内置了大量的内部集成，如Xiaomi Aqara，BMW Connected，DAAPD, Logitect，这些集成可以帮我们快速集成局域网中已经存在的服务，如果有额外的需求，可以通过安装新的custom_components达到扩展的目的。

custom_components位于容器的/config/custom_components中，宿主的/usr/share/hassio/homeassistant/custom_components

components的加载会优先使用custom_components，如果存在同名目录，会覆盖内置的components。

### 插件

插件与集成的目的不同，插件是为了扩展docker内部功能的不足，homeass集成有特定的格式，观察容器内/usr/src/homeassistant/homeassistant/components的子目录，可以看到各个集成是为了实现设备扩展，需要定义不同的device_tracker, sensor, switch来达到拓展设备的目的。

插件则不局限于添加设备，如File Editor可以给HA添加文件编辑能力。Node RED可以给平台添加流程化变成能力。

## 配置

通过一键安装脚本安装并初始化配置后，系统便会进入Dashboard，没有接入设备的Home Assistant只有基础的几个配置

## 集成与插件

## HACS

HACS类似于一个集成管理中心，安装后我们可以更便捷的安装开源的集成。

HACS依赖于从github上下载components，国内的github环境导致网络不稳定，建议使用搜索方式跳转Repository然后通过git clone方式下载代码并转移到custom_components中

## Homekit

Homekit集成提供了HA接入Homekit的能力。HA上的设备通过该组件接入Homekit后便可以通过iOS或者Mac中的Home应用进行控制。
当然，也可以使用Siri与快捷指令进行控制。

## Homekit Controller

如果要使用HA的自动化能力，就得将原本接入Homekit的设备先接入HA中，而因为Homekit集成的原因，接入HA的设备都可以被Homekit识别，所以原本device->homekit，转成device->HA->homekit，就达成了Homekit与HA都可以控制的局面。

## Logitech

通过Logitech集成，可以接将Homepod接入HA被识别成media_player，而HA的tts服务可以使用media_player来播放声音。

## BMW

多数车辆都具备接入HA的能力，只要车辆具备车载互联系统，便可以通过HA提供的device_tracker, sensor, switch等平台能力接入HA中。

BMW Connected是HA官方提供的接入BMW车辆的能力。

## Xiaomi MIoT Auto(HACS)

米家具备大量的Wifi协议设备，米家将他的Wifi协议定义为MIot，MIoT Auto内置了对于大量支持MIoT协议设备的适配。

最新版本中，MIoT已经省去了手动获取Token的步骤，通过账号密码便可以快速集成MIoT协议的设备，大大简化了操作。

## Xiaomi BLE(HACS)

同上，米家的Wifi设备可以通过MIoT接入，蓝牙设备则可以通过Xiaomi BLE接入。

BLE设备由于对外不断广播的特性，树莓派只要在设备周围便可以检测到相关的传感器信息。