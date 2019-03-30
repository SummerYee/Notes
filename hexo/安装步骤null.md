---
title: 重装服务器2
categories: 
- 服务器
tags:
- ubuntu系统
---

1.sudo apt-get install rpm    -- 安装rpm
2.sudo apt-get install yum    -- 安装yum
3.sudo passwd root  -- 给root添加密码

##################
yum为centos中使用才可以；使用Ubuntu，ubuntu默认软件包管理器不是yum，而是dpkg，安装软件时用apt-get命令，改代码为:sudo sudo apt-get install net-tools，这样就安装好了。
sudo apt-get install xxxxx
apt-cache search readline
################
4.su root -- 切换到root
5.sudo update-alternatives --install /usr/bin/python3 python3 /usr/bin/python3.5 1
sudo update-alternatives --install /usr/bin/python3 python3 /usr/bin/python3.6 2 -- 调整同样大版本的优先级
6.sudo update-alternatives --install /usr/bin/python python /usr/bin/python2 100
sudo update-alternatives --install /usr/bin/python python /usr/bin/python3 150 -- 修改python默认优先级
7.sudo apt-get install vim --  安装vim
8.sudo apt-get install python-pip -- 安装pip
9.sudo apt-get remove --auto-remove python3.6.2 -- 卸载python
10.wget https://www.python.org/ftp/python/3.6.1/Python-3.6.1.tgz -- 安装python3.6
tar -zxvf Python-3.6.1.tgz  -- 解压下载包
cd Python-3.6.1
./configure --prefix=/usr/local/python3
./configure --enable-optimizations
 -- 进入目录，编译安装
make                       
make install
安装好python3.6后会自带pip  如果没安装好 把之前安装的删了 重新安装
进入root：rm -r file -- 强制删除
11.source /bin/activate 
deactivate --进入虚拟环境
12.gzip -d name.gz --解压
13.tar -xvf file.tar --解压.tar的文件
14../pycharm.sh --安装pycharm
 步骤：ok->continue->dont send->next->不点创建启动脚本->一直无脑点下去
15.# 永久变量设置
vim /etc/profile
export PATH="$PATH:/home/taobaba/pycharm-community-2018.3.5/bin" --配置环境变量
# 刷新生效
soucre /etc/profile
16.ssh-kengen --生成公、私钥
17. ~/.pip/pip.conf
[global]
index-url = http://pypi.douban.com/simple
trusted-host = pypi.douban.com --设置豆瓣源

#############################################
index-url=https://pypi.doubanio.com/simple/
##############
apt install virtualenv

#############

18.sudo apt-get install mysql-server
sudo apt-get isntall mysql-client
sudo apt-get install libmysqlclient-dev  --安装mysql
etc/mysql/mysql.conf.d/mysqld.conf --修改mysql配置文件
19.apt-get install mongodb
./mongod --dbpath /var/lib/mongodb/ --logpath /var/log/mongodb/mongodb.log --logappend &
sudo service mongod stop　　#停止服务
sudo service mongod start　　#启动服务
sudo service mongod restart #重新启动服务
sudo service mongod status #查看状态
/etc/mongod.conf --mongodb 配置信息




