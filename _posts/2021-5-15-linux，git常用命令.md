---
layout: post
title: linux常用的相关命令
categories: shell
description: 技术杂谈
keywords: shell，cp，ps，pwd
---
### 情景描述

开发使用开发机，测试使用测试机，都需要ssh上服务器进行操作，shell语句必不可少，本文写下几个常用但过长的shell命令进行解读，并用于复制粘贴，后续持续添加。

#### 路径相关

ls: （list files）列出目录下所有文件与文件夹；

cd \<dir>:（change directorys）进入目录，可为绝对目录和相对目录；

cd - 返回上一次的目录，在上传目录和开发目录切换十分好用。

#### 文件相关
df -h: （Disk free剩余磁盘容量）（-h -human 说人话）用人能懂（剩下多少G）的形式输出磁盘剩余；

du -sh：（Disk usage当前目录容量大小）（-h 同上，-s --summarize 仅显示总计）用人能懂（多少G）的形式输出当前文件夹的大小，用于估计上传服务器时间；

cp -r：（copy）（-r 递归的复制）把文件夹连同它的内容拷贝到目标路径下，ps：scp可以跨服务器；

### 运行相关
python test.py : 用python2运行一个脚本test.py 此时终端持续输出脚本打印内容，要执行其他任务只能再开一个终端。
接上，此时用ctrl+s是暂停任务，ctrl+q是继续任务，可方便调试看打印输出，执行时使用ctrl+z可实现和下一条语句类似的效果。
暂停时使用bg %1使暂停的任务后台运行。后台运行后disown -h %1可以忽略hup信号。即使终端断掉也能继续运行。

jobs 查看后台运行的任务，jobs只能查看当前xshell连接服务器窗口创建的任务，这句话的意思是如果多人使用同一台开发机，我们看不到其他人的进程。

python test.py &: 用python2后台运行一个脚本test.py

nohup python test.py &：直接后台看运行test.py,并且直接会创建一个nohup.out文件用于储存输出。

sh test.sh :用sh时会重新开启一个终端用于运行命令，这意味着bashrc的某些命令没有执行，如果用了alias
的命令别名，在test.sh里直接使用别名的话会报错，应当使用原命令。

ps -aux | sort -k4nr | head -N：ps（Process Status） -参数a指代all——所有的进程，u指代userid——执行该进程的用户id，x指代显示所有程序，不以终端机来区分
，sort -k4nr中（k代表从根据哪一个关键词排序，后面的数字4表示按照第四列排序；n指代numberic sort，根据其数值排序；r指代reverse，这里是指反向比较结果，输出时默认从小到大，反向后从大到小）
。head：-N可以指定显示的行数，默认显示10行。 








