---
tags: 
    - Tool
title: VSCode：远程连接
created: '2021-11-24T09:23:17.619Z'
modified: '2021-11-25T03:52:12.423Z'
---

## 1、安装Remote SSH插件

在vscode扩展中搜索Remote SSH插件并安装。

### 1.1、服务器安装ssh

+ 安装ssh服务端：apt-get install openssh-server  
+ 确认ssh-server是否启动：ps -e | grep ssh  
+ 如果ssh没有启动，则需要启动ssh-server：/etc/init.d/ssh start  
+ 如果ssh启动了，重启一下：/etc/init.d/ssh restart

### 1.2、配置SSH连接

![](https://cdn.jsdelivr.net/gh/cuijian2b/Notable@master/notes/assert/1d77b2ca2bb141b6b3d7b47c647190e5.jpg)
Host 连接名称(自定义）  
HostName IP地址  
User 用户名(我使用root)

### 1.3、配置免密登录

+ 将自己的ssh公钥放到服务器上的/root/.ssh/authorized_keys中  
+ 检查ssh的配置文件，路径：/etc/ssh/sshd_config，确保：  
PublickeyAuthentication yes  
AuthorizedKeysFile ./ssh/authorized_keys
第一次需要输入密码登录，以后就可以免密直接登录了，网络正常的情况下，打开新窗口就可以连上了。

## 2、离线安装Linux环境

我的Linux服务器并不能连接外网，所以启动远程连接服务器后，观察vscode终端会一直卡在：  
Installing to /root/.vscode-server/bin/ea3859d4ba2f3e577a159bc91e3074c5d85c0523 …  Downloading with wget  
是由于下载不到环境相关文件，此时就需要离线安装vscode环境。

### 2.1、Help->About查看版本

![](https://cdn.jsdelivr.net/gh/cuijian2b/Notable@master/notes/assert/8d126f2571fe4e588a78ac5b3ba08dfc.png)

### 2.2、下载环境的压缩包

打开本地浏览器，输入连接：<https://update.code.visualstudio.com/commit:/server-linux-x64/stable>  
将替换成自己对应版本的即可，例如我的：<https://update.code.visualstudio.com/commit:ea3859d4ba2f3e577a159bc91e3074c5d85c0523/server-linux-x64/stable>，会下载到一个vscode-server-linux-x64.tar.gz压缩文件。

### 2.3、将环境文件放到服务器上

**vscode-server-linux-x64.tar.gz文件**：

![](https://cdn.jsdelivr.net/gh/cuijian2b/Notable@master/notes/assert/cfb4f35ba3594a2194477cf4e3bb3d36.png)

将vscode-server-linux-x64.tar.gz在服务器上解压，然后进入/root/.vscode-server/bin/目录，该目录下会有一个以自己vscode的版本hash-id命名的目录，将解压后的vscode-server-linux-x64.tar.gz中的所有文件拷贝至该目录，再使用vscode配置好的config文件再登录远程服务器。
