---
title: test
date: 2023-06-08 09:32:32
tags:
---


#### **安装过程**
 
> 设置系统时间，系统语言，开启ssh，安装go、node、php7.4、whistle、clash
------
 

### 1. 系统设置

```go

nano /etc/apt/source.list 国内镜像源

useradd cj

visudo 拷贝复制

//开机自启动
systemctl enable ssh 

systemctl start ssh

timedatectl set-timezone Asia/Shanghai

dpkg-reconfigure locales

选中下面四项（光标移上去，空格选中）：

zh_CN GB2312
zh_CN.GBK GBK
zh_CN.UTF-8 UTF-8
en_US.UTF-8 UTF-8

按tab键，切换到下面的确定按钮（确定按钮也可能是乱码，左边那个是），回车。

此时，让你选择默认的编码，选择en_US.UTF-8，别选中文

reboot

//配置固定ip
sudo nano /etc/netplan/50-cloud-init.yaml
network:
  renderer: networkd
  ethernets:
    eth0:
      addresses:
        - 192.168.1.107/24
      nameservers:
        addresses: [4.2.2.2, 8.8.8.8]
      routes:
        - to: default
          via: 192.168.1.1
  version: 2
  sudo netplan apply
sudo systemctl restart systemd-networkd
```

- [[国内镜像源]]
- 设置新用户追加root权限
- 设置时区->[[Debian10 Crontab不生效]]
- [终端乱码，设置中文](https://yangyq.net/2022/06/debian-terminal-encode.html)
- 开启ssh
- [开启固定ip](https://blog.csdn.net/xiaochong0302/article/details/127976781)
- [固定ip](https://juejin.cn/post/7237540467427475511)
 
### 2. 安装php，开启v2free cron定时任务
 
```go
ubuntu版本：

apt install software-properties-common
add-apt-repository ppa:ondrej/php

debian版本:

apt install php7.4

apt install php7.4-curl

mkdir -p /home/cj/code

scp -r cj@192.168.1.102:/Users/cj/Desktop/v2free ./

crontab -e

0 0 * * * php /home/cj/code/v2free/checkin.php

```

### 3. 安装go
 
```go

wget https://golang.google.cn/dl/go1.20.4.linux-arm64.tar.gz

tar -C /usr/local -xzf go1.20.4.linux-arm64.tar.gz

sudo nano /etc/profile

export PATH=$PATH:/usr/local/go/bin

source /etc/profile

```

- [教程](https://www.runoob.com/go/go-environment.html)

### 4. 安装node
 
```go

wget https://nodejs.org/dist/v18.16.0/node-v18.16.0-linux-arm64.tar.xz

xz -d node-v18.16.0-linux-arm64.tar.xz 

tar -xvf node-v18.16.0-linux-arm64.tar 

mv node-v18.16.0-linux-arm64 node

mv node /usr/local/

sudo nano /etc/profile

export PATH=$PATH:/usr/local/node/bin

source /etc/profile

//安装cnpm
npm install cnpm -g --registry=https://registry.npmmirror.com

```

- [教程](https://www.runoob.com/nodejs/nodejs-install-setup.html)

### 5. 安装clash

之前存在教程，大差不差的，linux arm64版本的即可，看对应的文章[[clash连接vpn上网]]，移动端配置代理是7890

```go


mkdir -p ~/.config/clash/

cd .config/clash/

wget -U "Mozilla/6.0" -O ~/.config/clash/config.yaml https://cdn.v2free.top/link/gYH6h6wGEq3wEJLH?clash=1

wget -O Country.mmdb https://v2free.net/ssr-download/Country.mmdb

clash

nano /lib/systemd/system/clash@.service

systemctl daemon-reload  

systemctl start clash@root

systemctl enable clash@root

```



### 6. 安装whistle

 移动端配置代理是8899，ios端记得将关于本机->证书信任->whistle开启
 
```go

mkdir -p /home/cj/obsidian

//sshfs挂载
sshfs cj@192.168.1.102:/Users/cj/Documents/code/Markdown文档/ /home/cj/obsidian

//如果需要使用fs.stat识别挂载的文件夹，需要在挂载时加上-o allow_other选项
sshfs cj@192.168.1.102:/Users/cj/Documents/code/Markdown文档/ /home/cj/obsidian -o allow_other

cnpm install -g whistle

//后面改成git获取
scp cj@192.168.1.102:/Users/cj/Documents/code/whistle/whistle.xiaohongshu.zip ./

unzip whistle.xiaohongshu.zip 

npm -g i ./whistle.xiaohongshu

//配置插件
//rules
小红书
https://edith.xiaohongshu.com
https://edith.xiaohongshu.com/api/sns/v2/note/feed

//小红书插件配置
保存文档目录:/home/cj/obsidian/小红书同步
过滤条件:edith.xiaohongshu.com/api/sns/v2/note/feed
修改过滤条件测试测试

```

![](https://weimgpub.oss-cn-hangzhou.aliyuncs.com/img/202305310642234.png)

- [[debian10挂载sshfsmac文件夹]]
- [whistle的安装教程](https://wproxy.org/whistle/install.html)
- [插件配置教程](https://wproxy.org/whistle/plugins.html)
 
---

 