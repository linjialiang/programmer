# WAMP 开发环境

> 这是为了帮助初学者快速搭建 PHP 开发环境而专门设立的一个章节

```text
- 当前最新版本：wamp-v4.3.11
- 百度网盘下载地址： https://pan.baidu.com/s/1Tk0Aa4BVdjc5YkP-FEDTeA 密码： r1yd
```

## 环境包列表

> 以下是 WAMP 开发环境所涉及到的软件包

| 序号 | 包名                                   | 描述                 |
| ---- | -------------------------------------- | -------------------- |
| 1    | `httpd-2.4.39-win64-VC15.zip`          | web 服务器包         |
| 2    | `mariadb-10.4.6-winx64.zip`            | 数据库管理包         |
| 3    | `php-7.3.6-Win32-VC15-x64.zip`         | php 解释器（线程版） |
| 4    | `phpMyAdmin-4.9.0.1-all-languages.zip` | mysql 网页版管理平台 |
| 5    | `adminer-4.7.1.7z`                     | mysql 网页版管理平台 |
| 6    | `php_xdebug.7z`                        | php_xdebug 包        |
| 7    | `composer-1.8.6.7z`                    | composer 包          |

> 注意：WAMP 开发环境需要 `VC15_x64` 运行库支持

## 目录结构

> WAMP 开发环境的目录结构如下：

```text
## =============================================================================
##                              【wamp开发环境目录】
## =============================================================================
├─ base                                     wamp核心目录
|   ├─apache24                              Web服务器
|   |  ├─bin                                阿帕奇可执行程序目录
|   |  ├─conf                               配置
|   |  |  ├─httpd.conf                      阿帕奇主配置文件
|   |  |  └─ ...
|   |  └─ ...
|   |
|   ├─mariadb                               mariadb 10.4版本
|   |  ├─bin                                mariadb可执行程序目录
|   |  ├─my.ini                             mariadb配置文件
|   |  └─ ...
|   |
|   ├─php                                   php 7.3版本
|   |  ├─ext                                模块目录
|   |  ├─php.ini                            php主配置文件
|   |  └─ ...
|   |
|   ├─phpmyadmin                            数据库管理平台，内附：adminer.php
|   |
|   ├─conf                                  公用配置文件目录
|   |   ├─apache24.conf                  apache24的公用配置文件
|   |
|   ├─default                               apache24站点缺省位置
|   |
├─web                                       web根目录
|   ├─sites                                 站点配置文件目录
|   |
|   ├─www                                   Web根目录
|   |
|   ├─data                                  mariadb数据库存放目录
|   |
|   ├─logs                                  服务器相关日志文件目录
|   |  ├─apache24                           apache24日志目录
|   |  ├─xdebug                             xdebug日志目录
|   |
|
├─install.bat                               安装wamp相关服务到系统服务
|
├─uninstall.bat                             从系统服务卸载wamp相关服务
|
├─便捷指令.bat                              日常经常用到的wamp相关指令
|
└─强制删除.bat                              使用net指令来删除wamp相关服务
```

> 内容导航：

| 序号 | 导航链接                         |
| ---- | -------------------------------- |
| 01   | [配置 httpd](./配置httpd.md)     |
| 02   | [配置 mariadb](./配置mariadb.md) |
| 03   | [配置 php](./配置php.md)         |
| 04   | [维护 wamp](./维护wamp.md)       |
| 05   | [更新日志](./更新日志.md)        |

| 源码导航                                   |
| ------------------------------------------ |
| [httpd 主配置文件](./source/httpd.conf)    |
| [httpd 子配置文件](./source/apache24.conf) |
| [php 配置文件](./source/php.ini)           |
| [mariadb 配置文件](./source/my.ini)        |

## 版本说明

> 从 v3 版本开始确立的版本号的发布为： `wamp-v` + `A-B-C`

| 写法 | 说明                                               |
| ---- | -------------------------------------------------- |
| `A`  | 主版本号，技术层面更改，发布新版本                 |
| `B`  | 次版本号，新版本与旧版出现兼容性问题时，发布新版本 |
| `C`  | 小版本号，wamp 内容出现变化，发布新版本            |

## 更新日志

> 见 [更新日志](./更新日志.md)
