---
title: GIT操作篇
categories: 
- 博客相关
tags:
- GIT
---
### Git简介
Git是目前世界上最先进的分布式版本控制系统。Git 是 Linus Torvalds 为了帮助管理 Linux 内核开发而开发的一个开放源码的版本控制软件。
### 安装Git
现在，Git可以在Linux、Unix、Mac和Windows这
几大平台上正常运行了。
1.linux上安装Git
2.windows上安装Git
 [下载Git](https://git-scm.com/download/),然后一路默认。
 [中文参考]()
### 配置
#### 设定自己机器的全局变量
`git config --global user.name "Your Name" `—— 设定用户名字，随便写
 `git config --global user.email "email@example.com"` ——设定用户邮箱，随便写
注意git config命令的--global参数，用了这个参数，表明你这台机器上所有的Git仓库都会使用这个配置，当然也可以对某个仓库指定不同的用户名和Email地址。

#### 查看自己机器的配置信息
config 配置有system级别 global（用户级别） 和local（当前仓库）三个 设置先从system->global->local  底层配置会覆盖顶层配置 分别使用--system/global/local 可以定位到配置文件
`git config --system --list`——查看系统config
`git config --global  --list`——查看当前用户（global）配置
`git config --local  --list`—— 查看当前仓库配置信息
`git config --list `——查看全配置

#### 额外配置
`git config --global credential.helper wincred `——凭证存储
`git config --global color.ui true `——开启颜色支持
`git config --global core.quotepath false `——支持utf-8编码
### 修改配置
`git config --global --replace-all user.email "输入你的邮箱" `
`git config --global --replace-all user.name "输入你的用户名"`
### 创建版本库（repository）
mkdir directory——创建一个空目录
cd directory
windows下目录最好不用中文
git init——初始化仓库

### 查看仓库状态
git status
### 本地操作
touch ddf.txt
`git add ddf.txt`——把文件添加到仓库
`git add -A`——添加所有文件到仓库
这里如果有警告则添加git config --global core.autocrlf false；原因是路径中存在 / 的符号转义问题，false就是不转换符号默认是true，相当于把路径的 / 符号进行转义，这样添加的时候就有问题。
`git commit -m "first"`——把文件提交到仓库
git commit命令，-m后面输入的是本次提交的说明，可以输入任意内容，当然最好是有意义的，这样你就能从历史记录里方便地找到改动记录。
### 回溯操作
git dif——查看difference，即查看修改内容
git log——查看提交历史，以便确定要回退到哪个版本。
记住：commit_id（版本号）
`git reset --hard HEAD^`——HEAD指向的版本就是当前版本，该指令即为回到上个操作版本
`git reset --hard commit_id`——回溯到相应版本
`git reflog`——查看命令历史，以便确定要回到未来的哪个版本。
### 撤销修改
 `git checkout -- ddf.txt`
### 删除文件
 rm ddf.txt
### 远程仓库
git push——推送，第一次后即用
`git push  -u origin master`——第一次推送master分支的所有内容
关联操作后面补充！！！！！！！！！！！！
总的来说都是添加了一个凭证 在windows下可以查看(控制面板-凭据管理器)
### 从远程库克隆
1.通过https克隆
git clone url
2.通过ssh克隆
### 从远程添加到本地
 git pull——拉