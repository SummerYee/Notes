---
title: ssh的基本操作
categories: 
- 技术类
tags:
- 服务器
---


# ssh的基本操作

### 1.ssh服务器(文字接口连接服务器)

SSH是Secure Shell Protocol(安全的壳程序协议)的简写。

公钥:id_rsa.pub

1.很多服务器都是需要认证的，ssh认证是其中的一种。在客户端生成公钥，把生成的公钥添加到服务器，你以后连接服务器就不用每次都输入用户名和密码了。

2.很多git服务器都是用ssh认证方式，你需要把你生成的公钥发送给代码仓库管理员，让他给你添加到服务器上，你就可以通过ssh自由地拉取和提交代码了。

私钥:id_rsa

启动SSH服务(默认已经启动)：/etc/init.d/sshd restart

### 2.查看本机 ssh 公钥，生成公钥

生成公钥

1.生成公钥了：`ssh-keygen -t rsa`

2.接着会确认存放公钥的地址，默认就是上面说的路径，直接enter键确认

3.接着会要求输入密码和确认密码，如果不想设置密码直接不输入内容 按enter键

查看 ssh 公钥方法：

1.通过命令窗口

a. 打开你的 git bash 窗口

b. 进入 .ssh 目录：cd ~/.ssh

c. 找到 id_rsa.pub 文件：ls

d. 查看公钥：cat id_rsa.pub 或者 vim id_rsa.pub

2.或者你也可以直接输入命令 ：cat ~/.ssh/id_rsa.pub

3.或者你也可以直接打开你用户（一般都是 Administrator）下的 .ssh 文件夹，打开它里面的 id_rsa.pub 文件.

### 3.通过ssh连接服务器

`ssh 账号@主机ip`

### 4.给服务器添加公钥(阿里)

cd ~/.ssh

vim authorized_keys 然后在里面加入自己本地的公钥

### 5.远程添加

文件异地直接复制:SCP

![](/img/scp1.png)

scp id_rsa.pub root@主机地址:~/.ssh/ddf.pub              

cat ddf.pub >>authorized_keys         累加进                          

### 6.退出
`ssh -T url`测试是否设置成功
logout