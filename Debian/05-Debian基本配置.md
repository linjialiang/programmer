# debian 基本配置

> debian 刚刚安装完成，还需要进行一系列的配置，才能更好的使用

## 设置静态 ip

> 网络地址设置成静态 ip，方便 ssh 远程管理

```shell
$ cp /etc/network/interfaces{,.bak}
$ vi /etc/network/interfaces
```

> 动态 ip 设置

```conf
source /etc/network/interfaces.d/*

# The loopback network interface
auto lo
iface lo inet loopback

# The primary nerwork interfaces
allow-hotplug enp0s3
iface enp0s3 inet dhcp
```

> 静态 ip 设置

```conf
source /etc/network/interfaces.d/*

# The loopback network interface
auto lo
iface lo inet loopback

# The primary nerwork interfaces
allow-hotplug enp0s3
iface enp0s3 inet static
        address 192.168.10.252
        netmask 255.255.255.0
        gateway 192.168.10.1
```

> 关闭/开启网卡

```shell
$ ifdown enp0s3
$ ifup enp0s3
```

> 重启网络

```shell
$ service networking restart
$ systemctl restart networking
$ /etc/init.d/networking restart
```

## 修改 DNS

> DNS 对网络速度和网络安全有很大的作用

```shell
$ cp /etc/resolv.conf{,.bak}
$ vi /etc/resolv.conf
```

> 提示：虚拟机桥接安装后的首选 dns 为 192.168.10.1，如无必要，不需要修改

## ssh 允许 root 连接远程

> 开发环境下，推荐使用 root 账户远程连接，这样非常便捷

```shell
$ cp /etc/ssh/sshd_config{,.bak}
$ vi /etc/ssh/sshd_config
```

> 找到 `PermitRootLogin` 去掉备注，并将属性值改为 yes

```shell
PermitRootLogin yes
```

> 重启 ssh

```shell
$ /etc/init.d/ssh restart
```

## 配置镜像源

> 安装好 debian 后，我们需要按照自身的需求配置好镜像源文件

```shell
$ cp /etc/apt/sources.list{,.bak}
$ vi /etc/apt/sources.list
```

> `sources.list` 文件内容：

```conf
deb http://mirrors.ustc.edu.cn/debian/ buster main contrib non-free
deb-src http://mirrors.ustc.edu.cn/debian/ buster main contrib non-free

deb http://mirrors.ustc.edu.cn/debian/ buster-updates main contrib non-free
deb-src http://mirrors.ustc.edu.cn/debian/ buster-updates main contrib non-free

deb http://mirrors.ustc.edu.cn/debian/ buster-backports main contrib non-free
deb-src http://mirrors.ustc.edu.cn/debian/ buster-backports main contrib non-free

deb http://mirrors.ustc.edu.cn/debian-security/ buster/updates main contrib non-free
deb-src http://mirrors.ustc.edu.cn/debian-security/ buster/updates main contrib non-free
```

> 更新源索引/更新包

```shell
$ apt update
$ apt dist-upgrade
```

## 美化 bash 终端

> 用户根目录下的 `.bashrc` 可以让 bash 控制台的界面更加清晰

```shell
$ cp ~/.bashrc{,.bak}
$ vi ~/.bashrc
```

```shell
PS1='[${debian_chroot:+($debian_chroot)}\u@Debian10 \W]\$ '
export LS_OPTIONS='--color=auto'
eval "`dircolors`"
alias ls='ls $LS_OPTIONS -F'
alias ll='ls $LS_OPTIONS -lF'
alias lla='ls $LS_OPTIONS -laF'
```

> 使用 source 更新终端界面

```shell
$ source ~/.bashrc
```

## 安装必备工具

> 有些系统工具非常有用，而我们的极简安装可能都没有安装上

```shell
$ apt install lrzsz tar bzip2 gzip curl wget
```

1. lrzsz

   > lrzsz 是一款在 linux 里可代替 ftp 上传和下载的程序

2. tar/tar.gz/tar.bz2

   > linux 下最常使用的压缩工具

3. curl

   > curl 是一种命令行工具，作用是发出网络请求，然后得到和提取数据，显示在"标准输出"（stdout）上面。

   ```text
   具体知识点请查看阮一峰的博客：
        - http://www.ruanyifeng.com/blog/2011/09/curl.html
   ```

## 包的安装守则

> 推荐使用 debian 官方源自带的 apt 指令安装，如无必要，尽可能不要使用编译的方式来安装包！
