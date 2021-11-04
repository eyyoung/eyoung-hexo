---
title: 内网穿透
---
由于HA搭建在家中的内部局域网，我们无法在外网达到控制局域网的目的。为了达到内网穿透的目的，我们有

* ngrok
* frp

等开源协议可以选择。

相对于ngrok，frp的配置会更为简单。但是无论是哪种协议，都需要

* 一个公网域名
* 一个具备暴露在公网有独立IP的虚拟主机

## FRP

https://github.com/fatedier/frp

* 下载（版本自改，https://github.com/fatedier/frp/releases）
```
wget https://github.com/fatedier/frp/releases/download/v0.36.2/frp_0.36.2_linux_amd64.tar.gz
```
* 解压
```
tar -zxvf frp_0.36.2_linux_amd64.tar.gz
```
* 配置

服务端配置
```
[common]
bind_port = 7000
dashboard_port = 7500
privilege_token = xxxx
dashboard_user = xxxxx
dashboard_pwd = xxxxxx
vhost_http_port = 10080
vhost_https_port = 10443
```

服务端运行
```
./frps -c frps.ini
```

客户端配置
```
[common]
server_addr = xxx
server_port = 7000
privilege_token = xxxx
login_fail_exit = false

[homeassistant]
type = tcp
local_port = 8123
remote_port = 38123

[router]
type = tcp
local_ip = 10.48.1.1
local_port = 80
remote_port = 38128
```
 
客户端运行
```
./frpc -c frpc.ini
```

## Supervisor

通过supervisor控制应用在后台常驻运行。

* 通过pip安装supervisor
```
pip install supervisor
```
* 配置
```
cd /etc/supervisor
```
可以看到include config.d/*中的所有配置文件
* 新建新的supervisor配置文件
* 新增配置
```ini
[program:frpcloud]  ;程序的名称
command =  /home/pi/frp/frp_0.35.1_linux_arm/frpc -c /home/pi/frp/frp_0.35.1_linux_arm/frpc.ini ;执行的命令
directory = /home/pi/ ;命令执行的目录
user = pi  ;执行进程的用户
stopsignal = INT
autostart = true  ;是否自动启动
autorestart = true  ;是否自动重启
startsecs = 1  ;自动重启间隔
stderr_logfile = /var/log/frpcloud.err.log  ;标准错误日志
stdout_logfile = /var/log/frpcloud.out.log  ;标准输出日志
```
* 启动supervisor
```ruby
supervisorctl start frpcloud
```
* 其他命令
```bash
supervisorctl status # 查看状态
supervisorctl restart frpcloud # 重启服务
supervisorctl reread # 更新配置
supervisorctl update # 配置生效
```