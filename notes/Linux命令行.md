---
tags:
  - Linux
title: Linux命令行
created: 2021-10-28T09:14:55.575Z
modified: 2022-09-07T10:00:15.966Z
---

Linux命令行

## 一、目录操作

### 1、移动目录

```bash
pwd             查看当前工作目录
clear           清除屏幕
cd ~            当前用户目录
cd /            根目录
cd -            上一次访问的目录
cd ..           上一级目录
```

### 2、查看目录内信息

```bash
ll             查看当前目录下内容（LL的小写）
```

### 3、创建目录

```bash
mkdir aaa       在当前目录下创建aaa目录，相对路径；
mkdir ./bbb     在当前目录下创建bbb目录，相对路径；
mkdir /ccc      在根目录下创建ccc目录，绝对路径；
```

**递归创建目录（会创建里面没有的目录文件夹）**

```bash
mkdir -p temp/nginx 
```

### 4、搜索命令

```bash
find / -name 'b'        查询根目录下（包括子目录），名以b的目录和文件；
find / -name 'b*'       查询根目录下（包括子目录），名以b开头的目录和文件； 
```

### 5、剪切命令

**重命名**

```bash
mv 原先目录 文件的名称   mv tomcat001 tomcat 
```

**剪切命令(有目录剪切到制定目录下，没有的话剪切为指定目录）**

```bash
mv   /aaa /bbb          将根目录下的aaa目录，移动到bbb目录下，在bbb，麚也叫aaa目录；
mv  bbb usr/bbb         将当前目录下的bbbb目录，移动到usr目录下，并且修改名称为bbb；
```

### 6、复制目录

```bash
cp -r /aaa /bbb         将/目录下的aaa目录复制到/bbb目录下，在/bbb目录下的名称为aaa
cp -r /aaa /bbb/aaa     将/目录下的aa目录复制到/bbb目录下，且修改名为aaa;
```

### 7、删除目录

**强制式删除指定目录**

```bash
rm -rf /bbb          强制删除/目录下的bbb目录。如果bbb目录中还有子目录，也会被强制删除，不会提示；
```

**删除目录**

```bash
rm -r /bbb           普通删除。会询问你是否删除每一个文件
```

## 二、文件操作

### 1、删除文件

**删除**

```bash
rm -r a.java     删除当前目录下的a.java文件（每次回询问是否删除y：同意）
```

**强制删除**

```bash
rm -rf a.java        强制删除当前目录下的a.java文件
rm -rf ./a*          强制删除当前目录下以a开头的所有文件；
rm -rf ./*           强制删除当前目录下所有文件（慎用）；
```

### 2、创建文件

```bash
touch testFile
```

### 3、查找文件

递归删除.pyc格式的文件

```bash
find . -name '*.pyc' -exec rm -rf {} \;
```

打印当前文件夹下指定大小的文件

```bash
find . -name "*" -size 145800c -print
```

递归删除指定大小的文件(145800)

```bash
find . -name "*" -size 145800c -exec rm -rf {} \;
```

递归删除指定大小的文件，并打印出来

```bash
find . -name "*" -size 145800c -print -exec rm -rf {} \;
```

- `"."` 表示从当前目录开始递归查找
- `“ -name '*.exe' "`根据名称来查找，要查找所有以.exe结尾的文件夹或者文件
- `" -type f "`查找的类型为文件
- `"-print"` 输出查找的文件目录名
- `-size 145800c` 指定文件的大小
- `-exec rm -rf {} \;` 递归删除（前面查询出来的结果）

### 4、压缩和解压缩

**tar**

```bash
tar -zcvf start.tar.gz a.java b.java   将当前目录下a.java、b.java打包
tar -zcvf start.tar.gz ./*             将当前目录下的所欲文件打包压缩成haha.tar.gz文件
```

```bash
tar -xvf start.tar.gz                解压start.tar.gz压缩包，到当前文件夹下；
tar -xvf start.tar.gz -C usr/local（C为大写，中间无空格）
                                    解压start.tar.gz压缩包，到/usr/local目录下；
unzip start.tar.gz -d usr/loal
```

解压缩`tar.xz`文件

```bash
tar xf node-v12.18.1-linux-x64.tar.xz
```

**unzip**

```bash
unzip file1.zip                   解压一个zip格式压缩包
zip lib.zip tomcat.jar            将单个文件压缩(lib.zip)
zip -r lib.zip lib/               将目录进行压缩(lib.zip)
zip -r lib.zip tomcat-embed.jar xml-aps.jar        将多个文件压缩为zip文件(lib.zip) 
```

将`english.zip`包，解压到指定目录下`/usr/app/`

```bash
unzip -d /usr/app/com.lydms.english.zip
```

### 5、下载上传文件

```bash
yum install -y lrzsz

rz          上传文件；
sz          下载文件；
```

## 三、文件内容操作（查看日志，更改配置文件）

### 1、修改文件内容

```bash
vim a.java       进入一般模式
i                进入插入模式(编辑模式)
ESC              退出
:wq              保存退出（shift+：调起输入框）
:q！             不保存退出（shift+：调起输入框）（内容更改）
:q               不保存退出（shift+：调起输入框）（没有内容更改）
```

### 2、文件内容的查看

```bash
cat a.java       查看a.java文件的最后一页内容；
more a.java      从第一页开始查看a.java文件内容，按回车键一行一行进行查看，
                 按空格键一页一页进行查看，q退出；
less a.java      从第一页开始查看a.java文件内容，按回车键一行一行的看，
                 按空格键一页一页的看，支持使用PageDown和PageUp翻页，q退出；
```

**总结下more 和 less的区别:**

1. less可以按键盘上下方向键显示上下内容,more不能通过上下方向键控制显示
2. less不必读整个文件，加载速度会比more更快
3. less退出后shell不会留下刚显示的内容,而more退出后会在shell上留下刚显示的内容.
4. 由于more不能后退.

**实时查看文件后几行(实时查看日志)**

```bash
tail -f a.java          查看a.java文件的后10行内容；
```

**前后几行查看**

```bash
head a.java             查看a.java文件的前10行内容；
tail -f a.java          查看a.java文件的后10行内容；
head -n 7 a.java        查看a.java文件的前7行内容；
tail -n 7 a.java        查看a.java文件的后7行内容；
```

### 3、文件内部搜索指定的内容

```bash
grep under 123.txt           在123.txt文件中搜索under字符串，大小写敏感，显示行；
grep -n under 123.txt        在123.txt文件中搜索under字符串，大小写敏感，显示行及行号；
grep -v under 123.txt        在123.txt文件中搜索under字符串，大小写敏感，显示没搜索到的行；
grep -i under 123.txt        在123.txt文件中搜索under字符串，大小写敏感，显示行；
grep -ni under 123.txt       在123.txt文件中搜索under字符串，大小写敏感，显示行及行号；
```

### 4、查看库文件内容

```bash
nm -D optionApi.so | grep GetMsgInfo    在库中查找函数
nm -D optionApi.so | c++filt| grep GetMsgInfo    在库中查找函数
```

**终止当前操作**

`Ctrl+c`和`Ctrl+z`都是中断命令，但是作用却不一样。

```bash
ctrl+z
ctrl+c
```

**Ctrl+Z**就扮演了类似的角色，将任务中断，但是任务并没有结束，在进程中只是维持挂起的状态，用户可以使用fg/bg操作前台或后台的任务，fg命令重新启动前台被中断的任务，bg命令把被中断的任务放在后台执行。\
**Ctrl+C**也扮演类似的角色，强制中断程序的执行。

**重定向功能**\
可以使用 \> 或 \<
将命令的输出的命令重定向到test.txt文件中（没有则创建一个）

```bash
echo 'Hello World' > /root/test.txt
```

## 四、系统日志位置

```bash
cat /etc/redhat-release        查看操作系统版本
/var/log/message               系统启动后的信息和错误日志，是Red Hat Linux中最常用的日志之一
/var/log/message               系统启动后的信息和错误日志，是Red Hat Linux中最常用的日志之一 
/var/log/secure                与安全相关的日志信息 
/var/log/maillog               与邮件相关的日志信息 
/var/log/cron                  与定时任务相关的日志信息 
/var/log/spooler               与UUCP和news设备相关的日志信息 
/var/log/boot.log              守护进程启动和停止相关的日志消息 
```

**查看某文件下的用户操作日志**\
到达操作的目录下，执行下面的程序：

```bash
cat .bash_history
```

## 五、创建与删除软连接

**1、创建软连接**

```bash
ln -s /usr/local/app /data
```

注意：创建软连接时，data目录后不加 / (加上后是查找其下一级目录)；

![在这里插入图片描述](https://cdn.jsdelivr.net/gh/cuijian2b/Notable@master/notes/assert/20191104170912257.png)\
**2、删除软连接**

```bash
rm -rf /data
```

注意：取消软连接最后没有/，rm -rf 软连接。加上/是删除文件夹；

![在这里插入图片描述](https://cdn.jsdelivr.net/gh/cuijian2b/Notable@master/notes/assert/20191104172803658.png)

## 六、Linux下文件的详细信息

```bash
 R:Read  w:write  x: execute执行
-rw-r--r-- 1 root root  34942 Jan 19  2018 bootstrap.jar
前三位代表当前用户对文件权限：可以读/可以写/不能执行
中间三位代表当前组的其他用户对当前文件的操作权限：可以读/不能写/不能执行
后三位其他用户对当前文件权限：可以读/不能写/不能执行
```

![文件](https://cdn.jsdelivr.net/gh/cuijian2b/Notable@master/notes/assert/20190925153418897.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDYyNDExNw==,size_16,color_FFFFFF,t_70)

**更改文件的权限**

```bash
chmod u+x web.xml （---x------）     为文件拥有者（user）添加执行权限；
chmod g+x web.xml （------x---）     为文件拥有者所在组（group）添加执行权限；
chmod 111 web.xml  （---x--x--x）    为所有用户分类，添加可执行权限；
chmod 222 web.xml （--w--w--w-）     为所有用户分类，添加可写入权限；    
chmod 444 web.xml （-r--r--r--）     为所有用户分类，添加可读取权限；
```

## 七、常用的docker容器的命令

**1、下载镜像**\
Linux服务器下载安装包镜像命令

```bash
wget https://mirrors.huaweicloud.com/elasticsearch/7.8.0/elasticsearch-7.8.0-windows-x86_64.zip
```

[华为开源镜像站](https://mirrors.huaweicloud.com/)

```bash
https://mirrors.huaweicloud.com/
```

**2、常用命令**

```bash
#1、查看docker中下载好的镜像：
docker images
 #2、查询需要的容器名称：
docker search mysql
#3、将需要的docker容器下载运行到本地(名称、端口号、msyql密码、ID)：
docker run -di --name=first -p 3306:3306 -e MYSQL_ROOT_PASSWORD=root 26d26dsfsd31a
#4、查看运行的docker容器：
docker ps
#5、查看所有的docker容器（包括未运行的）：
docker ps -a
#6、停止当前运行的docker容器：
docker stop first
#7、启动docker容器：
docker start first
#8、重启docker容器：
docker restart first
#9、删除docker容器：
docker rm first
```

## 八、运维常用命令

### 1、查看服务器端口号是否可用

**查看服务器是否可用**

```bash
ping 49.32.587.164
```

对 www.lydms.com 发送 4 个 ping 包, 检查与其是否联通

```bash
ping -c 4 www.lydms.com
```

**查看服务器指定端口是否可用**

```bash
telnet 49.32.587.164 8093
```

[Telnet安装](https://blog.csdn.net/lydms/article/details/113698856)

这是我写过的一个Linux安装Telnet的文章。

```bash
https://blog.csdn.net/lydms/article/details/113698856
```

### 2、查看当前端口号占用情况

`netstat 命令用于显示各种网络相关信息，如网络连接, 路由表, 接口状态等等;`\
列出所有处于监听状态的tcp端口:

```bash
netstat -lt
```

查看所有的端口信息, 包括 PID 和进程名称

```bash
netstat -tulpn
```

1.用于查看某一端口的占用情况

```bash
lsof -i:8080
```

2.显示tcp，udp的端口和进程等相关情况

```bash
netstat -tunlp
```

3.指定端口号的进程情况

```bash
netstat -tunlp|grep 8080
```

### 3、获取进程启动目录

```bash
ps -ef | grep python

cd /proc/4431/fd
ls -l

lsof -p $PID
```

**1、shutdown(关闭计算机)**

shutdown是最常用也是最安全的关机和重启命令，它会在关机之前调用fsck检查磁盘，其中-h和-r是最常用的参数：

```bash
-h：停止系统服务并关机  
-r： 停止系统服务后重启  
```

案例：

```bash
shutdown -h now    --立即关机  
shutdown -h 10:53  --到10:53关机，如果该时间小于当前时间，则到隔天  
shutdown -h +10    --10分钟后自动关机  
shutdown -r now    --立即重启  
shutdown -r +30 'The System Will Reboot in 30 Mins'   --30分钟后重启并并发送通知给其它在线用户  
```

**2、查看处于各种连接状态数量(ESTABLISHED、CLOSE_WAIT、TIME_WAIT)**

```bash
netstat -n | awk '/^tcp/ {++S[$NF]} END {for(a in S) print a, S[a]}'
```

![在这里插入图片描述](https://cdn.jsdelivr.net/gh/cuijian2b/Notable@master/notes/assert/20200710160201853.png)\
查看处于`ESTABLISHED`状态连接

```bash
netstat -nt | awk '{if($NF=="ESTABLISHED"){wait[$5]++}}END{for(i in wait) print i,wait[i]}'
```

查看处于`CLOSE_WAIT`状态连接

```bash
netstat -nt | awk '{if($NF=="CLOSE_WAIT"){wait[$5]++}}END{for(i in wait) print i,wait[i]}'
```

查看处于`TIME_WAIT`状态连接

```bash
netstat -nt | awk '{if($NF=="TIME_WAIT"){wait[$5]++}}END{for(i in wait) print i,wait[i]}'
```

**4.查看PID进程信息**

```bash
ps -aux |grep 28990
```

根据PID，查看JVM中各线程信息(\'0x9eb'为nid值)

```bash
jstack 2246|grep '0x9eb' -A 50
```

**6、ps 命令**\
过滤得到当前系统中的 ssh 进程信息

```bash
ps aux | grep 'ssh'
```

**7、管道命令**\
`简单来说, Linux 中管道的作用是将上一个命令的输出作为下一个命令的输入, 像 pipe 一样将各个命令串联起来执行, 管道的操作符是 |`\
管道命令查看当前运行的程序中，名称为java的程序

```bash
ps -ef|grep java
```

查看/etc/passwd文件中的root内容

```bash
cat /etc/passwd | grep 'root'
```

查看当前系统的ip连接（Windows和Linux通用）

```bash
netstat -an
```

将sh
test.sh任务放到后台，并将打印的日志输出到nohup.out文件中，**终端不再能够接收任何输入（标准输入）**

```bash
nohup sh test.sh  &
```

将sh
test.sh任务放到后台，并将打印的日志输出到nohup.out文件中，**终端能够接收任何输入**

```bash
nohup sh test.sh  &
```

8、添加Host地址\
打开配置文件

```bash
vim /etc/hosts
```

在打开的文件中添加

```bash
49.235.32.164 www.lydms.com
```

保存文件后，重启网络

```bash
/etc/init.d/network restart
```

重新加载成功：\
![在这里插入图片描述](https://cdn.jsdelivr.net/gh/cuijian2b/Notable@master/notes/assert/20200921141541487.jpg#pic_center)

## 九、yum常用命令

```bash
yum install iptables-services      下载并安装iptables
yum list                           列出当前系统中安装的所有包
yum search package_name            在rpm仓库中搜寻软件包
yum update package_name.rpm        更新当前系统中所有安装的rpm包
yum update package_name            更新一个rpm包
yum remove package_name            删除一个rpm包
yum clean all                      删除所有缓存的包和头文件
```

## 十、其他命令

**查看占用资源**

```bash
ps -au      占用的资源是从进程启动开始，计算的平均占用资源，比如cpu等
top         实时占用的资源；
```

**查看当前目录所占存储**

```bash
du -lh          查看当前文件下各文件夹占用存储空间
du -sh          查看当前文件夹所占存储空间
du --max-depth=<目录层数>     超过指定层数的目录后，予以忽略。
du --max-depth=1             只查看当前目录下文件占用的存储空间
```

**管道命令：**\
根据项目查看进程，更加PID查看项目，以及项目路径

```bash
ps -ef                      查看所有的进程
ps -ef | grep mysql         查看mysql相关的进程
```

通过进程PID查看所占用的端口号

```bash
netstat -nap |grep 进程ID(PID)
```

**查看Linux下系统存储使用率**

```bash
df -h          查看系统硬盘使用情况
```

**杀死进程(根据PID)**

```bash
kill -9 2630       进程pid
```

**关闭防火墙**

```bash
service iptables stop      临时关闭防火墙
chkconfig iptables off     防火墙开启不启动
service iptables status    查看防火墙状态
```

**开机启动选项**

```bash
msconfig                    查看开机启动选项
chkconfig                   查看开机启动服务列表
```

**查看MySQL服务的程序的状态**

```bash
service mysql start        开启MySQL    
service mysql status       查看MySQL的状态    
service mysql stop         关闭MySQL    
```

## 十一、Linux内核优化

打开配置文件

```bash
vim /etc/sysctl.conf
```

加载新的配置(需开启防火墙iptables，否则会报错)

```bash
sysctl -p
```

[收藏的详情地址](https://www.cnblogs.com/lldsn/p/10489593.html)

```bash
https://www.cnblogs.com/lldsn/p/10489593.html
```

## 十二、用户权限操作

### 1、添加用户

添加用户`sum`:

```bash
useradd –d /usr/sum -m sum
```

关于useradd的某些参数：

**-u：** 指定 UID，这个 UID 必须是大于等于500，并没有其他用户占用的 UID

**-g：** 指定默认组，可以是 GID 或者 GROUPNAME，同样也必须真实存在

**-G：** 指定额外组

**-c：** 指定用户的注释信息

**-d：** 指定用户的家目录

已创建的用户`sum`设置密码

```bash
passwd sum
```

新建的用户在面显示

```bash
cat /etc/passwd
```

![反反复复](https://cdn.jsdelivr.net/gh/cuijian2b/Notable@master/notes/assert/20200827143920513.png#pic_center)

删除用户`sum`

```bash
userdel sum
```

删除用户文件夹

```bash
rm -rf /usr/sum
```

切换下刚才添加的用户

```bash
su sum
```

回到root用户

```bash
exit
```

### 2、添加组

添加用户组

```bash
groupadd groupname
```

删除用户组

```bash
groupdel groupname
```

可以看到自己的分组和分组id

```bash
cat /etc/group
```

sum: x:1000:1000:: /usr/sum :/bin/bash\
sum: x:0:1000:: /usr/sum :/bin/bash

## 十三、环境变量

### 1、添加环境变量

```bash
echo $PATH
export PATH=/usr/local/cuda/bin:$PATH

echo $LD_LIBRARY_PATH
export LD_LIBRARY_PATH=/usr/local/lib
export LD_LIBRARY_PATH=/usr/local/lib64:$LD_LIBRARY_PATH

echo $PKG_CONFIG_PATH=~/usr/local/lib/pkgconfig
# 保存记录
source ~/.bashrc
```

### 2、pkg环境变量

```bash
# 添加变量
echo $PKG_CONFIG_PATH=~/usr/local/lib/pkgconfig

# 修改pkgconfig目录下pc文件
prefix=~/usr/local/lib

# 保存记录
source ~/.bashrc
```

### 3、protobuf示例

```bash
export PATH=~/usr/local/bin:$PATH

export LIBRARY_PATH=~/usr/local/lib
export LD_LIBRARY_PATH=~/usr/local/lib:$LD_LIBRARY_PATH

export C_INCLUDE_PATH=~/usr/local/include
export CPLUS_INCLUDE_PATH=~/usr/local/include

# 添加变量
echo $PKG_CONFIG_PATH=~/usr/local/lib/pkgconfig

# 修改pkgconfig目录下pc文件
prefix=~/usr/local/lib

# 保存记录
source ~/.bashrc
```

### 4、系统环境变量

```bash
export TMPDIR=~/.tmp        # 临时文件目录
```

## 十四、TOP

实时占用的资源:

```bash
top
```

![在这里插入图片描述](https://cdn.jsdelivr.net/gh/cuijian2b/Notable@master/notes/assert/20200831110407981.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDYyNDExNw==,size_16,color_FFFFFF,t_70#pic_center)

top命令执行结果分为两个区域：**统计信息区**和**进程信息区**

#### 1、统计信息区

**TOP：任务队列信息，与uptime命令执行结果相同.**

- 15:33:39：系统时间
- up 5:40：主机已运行时间
- 2 users：用户连接数（不是用户数，who命令）
- load average: 1.09, 1.04,
    0.98：系统平均负载，统计最近1，5，15分钟的系统平均负载

**Tasks：进程信息**

- 123 total：进程总数
- 3 running：正在运行的进程数
- 120 sleeping：睡眠的进程数
- 0 stopped：停止的进程数
- 0 zombie：僵尸进程数

**%CPU(s)：CPU信息（当有多个CPU时，这些内容可能会超过两行）**

- 42.1 us：用户空间所占CPU百分比
- 2.0 sy：内核空间占用CPU百分比
- 0.0 ni：用户进程空间内改变过优先级的进程占用CPU百分比
- 49.2 id：空闲CPU百分比
- 0.0 wa：等待输入输出的CPU时间百分比
- 6.0 hi：硬件CPU终端占用百分比
- 0.7 si：软中断占用百分比
- 0.0 st：虚拟机占用百分比

**KiB Mem：内存信息（与第五行的信息类似与free命令类似）**

- 3780.9 total：物理内存总量
- 727.4 free：已使用的内存总量
- 668.8 used：空闲的内存总量（free + userd = total）
- 2384.7 buff/cache：用作内核缓存的内存量

**KiB：swap信息**

- 2048.0 total：交换分区总量
- 2046.0 free：已使用的交换分区总量
- 2.0 used：空闲交换分区总量
- 859.6
    avail：缓冲的交换区总量，内存中的内容被换出到交换区，然后又被换入到内存，但是使用过的交换区没有被覆盖，交换区的这些内容已存在于内存中的交换区的大小，相应的内存再次被换出时可不必再对交换区写入。

#### 2、进程信息区

- PID:进程id

- USER:进程所有者的用户名

- PR:优先级

- NI:nice值。负值表示高优先级，正值表示低优先级

- RES:进程使用的、未被换出的物理内存的大小

- %CPU:上次更新到现在的CPU时间占用百分比

- %MEM:进程使用的物理内存百分比

- TIME+：进程所使用的CPU时间总计，单位1/100秒

- COMMAND:命令名/行

- PPID:父进程id

- RUSER:Real user
    name（看了好多，都是这样写，也不知道和user有什么区别，欢迎补充此处）

- UID:进程所有者的id

- VIRT:进程使用的虚拟内存总量，单位kb。VIRT=SWAP+RES

- GROUP:进程所有者的组名

- TTY:启动进程的终端名。不是从终端启动的进程则显示为?

- NI:nice值。负值表示高优先级，正值表示低优先级

- P:最后使用的CPU，仅在多CPU环境下有意义

- TIME:进程使用的CPU时间总计，单位秒

- SWAP:进程使用的虚拟内存中被被换出的大小

- CODE:可执行代码占用的物理内存大小

- DATA:可执行代码以外的部分（数据段+栈）占用的物理内存大小

- SHR:共享内存大小

- nFLT:页面错误次数

- nDRT:最后一次写入到现在，被修改过的页面数

- S:进程状态（D=不可中断的睡眠状态，R=运行，S=睡眠，T=跟踪/停止，Z=僵尸进程）

- WCHAN:若该进程在睡眠，则显示睡眠中的系统函数名

- Flags:任务标志
