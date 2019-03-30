---
title: Django项目部署
categories: 
- WEB
tags:
- WEB后端
---

## 零.项目部署前提 
阿里云地址：120.77.174.68
本地ip：127.0.0.1 
MySQL： 3306端口
Redis： 6379端口

## 一.安装数据库
### 安装MariaDB或者安装[MySQL]()
### 安装MariaDB
安装命令：`yum -y install mariadb mariadb-server`
安装完成MariaDB，首先启动MariaDB：`systemctl start mariadb`
设置开机启动：`systemctl enable mariadb`
## 二.设置MariaDB密码
命令: `mysql_secure_installation`
```
Enter current password for root:<–初次运行直接回车
设置密码
Set root password? [Y/n] <– 是否设置root用户密码，输入y并回车或直接回车
New password: <– 设置root用户的密码
Re-enter new password: <– 再输入一次你设置的密码
其他配置
Remove anonymous users? [Y/n] <– 是否删除匿名用户，回车
Disallow root login remotely? [Y/n] <–是否禁止root远程登录,回车,
Remove test database and access to it? [Y/n] <– 是否删除test数据库，回车
Reload privilege tables now? [Y/n] <– 是否重新加载权限表，回车
```
初始化MariaDB完成，接下来测试登录
`mysql -u root -p`
## 三.开启远程连接
在mysql数据库中的user表中可以看到默认是只能本地连接的，所有可以添加一个新的用户，该用户可以远程访问
1.创建用户（进入mysql后）
 先使用数据库
`use mysql;`

 针对ip
`create user 'root'@'192.168.10.10' identified by '密码';`

 全部（用这个就行）
 `create user 'root'@'%' identified by '密码';`
2. 授权
 给用户最大权限（用这个就行）
`grant all privileges on *.* to 'root'@'%' identified by '密码';`

 给部分权限(test 数据库)

`grant all privileges on test.* to 'root'@'%' identified by 'password' with grant option;`

 刷新权限表
`flush privileges;`

 查看
`show grants for 'root'@'localhost';`
接下来就可以在远程的数据库可视化工具中直接访问该服务器中的mysql了。

 访问数据库
`mysql -u root -p`
![1](/img/deploy1.jpg)

## 四.安装python3.6
首先安装依赖包

安装Python3.6所需要的依赖包：

`yum -y groupinstall "Development tools"`

`yum -y install zlib-devel bzip2-devel openssl-devel ncurses-devel sqlite-devel readline-devel tk-devel gdbm-devel db4-devel libpcap-devel xz-devel`

安装Python3.7还需额外安装依赖包：
`yum install libffi-devel -y`

然后根据自己需求下载不同版本的Python3，我下载的是Python3.6.2

`wget https://www.python.org/ftp/python/3.6.2/Python-3.6.2.tar.xz`

然后解压压缩包，进入该目录，安装Python3

`tar -xvJf  Python-3.6.2.tar.xz`
`cd Python-3.6.2`
`./configure --prefix=/usr/local/python3`
`make && make install`

最后创建软链接

`ln -s /usr/local/python3/bin/python3 /usr/bin/python3`

`ln -s /usr/local/python3/bin/pip3 /usr/bin/pip3`

## 五.安装环境
1. 安装virtualenv
`yum install python-virtualenv`
2. 创建虚拟环境
`virtualenv --no-site-packages -p /usr/bin/python3 env1`

`cd env1`

 激活虚拟环境
`source bin/activate`
xftp传输项目或者[scp传输]()
3. 安装环境需要的包
`pip3 install -r re_install.txt`

其中re_install.txt文件中记录的是需要安装包的名称以及对应的版本
### 环境绝对路径安装
/home/env/env1/bin/pip3 install -r /home/src/fresh_shop/requirement.txt
### 绝对路径启动项目
/home/env/env1/bin/python3 /home/src/fresh_shop/manage.py runserver 0.0.0.0:80

`netstat -lntp` 查看端口使用

## 六.部署
### 1. 测试环境中部署方式
1.1修改settings.py配置文件，将DEBUG=False模式，修改ALLOEWD_HOST=['*']
1.2.中间件的修改
```
# 不需要做登录校验的地址
        not_need_check = ['/user/register/', '/user/login/',
                          '/goods/index/', '/goods/detail/.*/',
                          '/cart/.*/','/static/.*/','/media/.*/']
# '/static/.*/','/media/.*/'
```
1.3.修改工程目录下的urls.py
```
from django.contrib import admin
from django.urls import path, include,re_path
from django.contrib.staticfiles.urls import static

from fresh_shop.settings import MEDIA_URL, MEDIA_ROOT, STATICFILES_DIRS

from goods import views
from django.views.static import serve

urlpatterns = [
    path('admin/', admin.site.urls),
    # 导入应用
    path('cart/', include(('cart.urls', 'cart'), namespace='cart')),
    path('goods/', include(('goods.urls', 'goods'), namespace='goods')),
    path('order/', include(('order.urls', 'order'), namespace='order')),
    path('user/', include(('user.urls', 'user'), namespace='user')),
    # 首页路径
    path('', views.index),
    re_path(r'^static/(?P<path>.*)$', serve, {"document_root": STATICFILES_DIRS[0]}),
    re_path(r'^media/(?P<path>.*)$', serve, {"document_root": MEDIA_ROOT}),
```
1.4.创建脚本文件
在src目录中创建一个sh脚本
[root@izwz9gtgjgu8as4jaamzxzz src]# touch freshshop.sh
在sh脚本里面添加绝对路径启动
[root@izwz9gtgjgu8as4jaamzxzz src]# vim freshshop.sh
`/home/env/env1/bin/python3 /home/src/fresh_shop/manage.py runserver 0.0.0.0:80`
1.5.修改脚本文件权限
[root@izwz9gtgjgu8as4jaamzxzz src]# chmod -R 777 freshshop.sh 
1.6.启动
nohup：不挂断地运行命令。
&：挂后台
[root@izwz9gtgjgu8as4jaamzxzz src]# `nohup ./freshshop.sh &`

### 2. 正式环境中部署方式
正式环境中部署为nginx+uwsgi来部署django项目
2.1 安装nginx
a）添加nginx存储库
`yum install epel-release`
b) 安装nginx
`yum install nginx`
c) 运行nginx
Nginx不会自行启动。要运行Nginx则需输入
`systemctl start nginx`
nginx的运行命令：
 `systemctl status nginx` 查看nginx的状态
 `systemctl start/stop/enable/disable nginx` 启动/关闭/设置开机启动/禁止开机启动
d）系统启动时启用Nginx
`systemctl enable nginx`
e）如果您正在运行防火墙，请运行以下命令以允许HTTP和HTTPS通信：

`sudo firewall-cmd --permanent --zone=public --add-service=http `

`sudo firewall-cmd --permanent --zone=public --add-service=https`

`sudo firewall-cmd --reload`

### 3.配置uwsgi
3.1 安装uwsgi
在虚拟环境中安装uwsgi，假设虚拟环境安装在/home/env/env1中
使用`/home/env/env1/bin/pip3 install uwsgi`
### 4.配置项目代码，配置项目nginx，配置uwsgi.ini等
本案例的配置文件，都习惯将每一个项目的配置文件，日志文件，虚拟环境放在一起，这样开发方便，运维也方便维护
项目的目录结构如下：`conf env logs src`
其中：

conf是配置文件，用于存放项目的nginx.conf文件，uwsgi.ini文件

logs是日志文件，用于存放nginx的启动成功和失败文件，以及uwsgi的运行日志文件

env是用于存放虚拟环境

src是项目文件，该目录下上传的是目录代码

4.1 配置nginx.conf文件
在conf下面创建一个名为freshnginx.conf的文件
首先：编写自己项目的conf文件如下：
```
server {
	listen		80;
	server_name 120.77.174.68;
	
	access_log /home/logs/freshaccess.log;
	error_log /home/logs/fresherror.log;

	location / {
		include uwsgi_params;
		uwsgi_pass 127.0.0.1:8890;
	}

	location /static/ {
		alias /home/src/fresh_shop/static/;
	}

	location /media/ {
		alias /home/src/fresh_shop/media/;
	}

}
```
其次：修改总的nginx的配置文件，让总的nginx文件包含我们自定义的项目的freshnginx.conf文件

总的nginx配置文件在：/etc/nginx/nginx.conf中

![2](/img/deploy2.png)
以上步骤操作完成以后，需要重启nginx：
`systemctl restart nginx`
查看运行状态
`systemctl status nginx`
4.2 配置uwsgi文件
在conf文件夹下除了包含自定义的freshnginx.conf文件，还有我们定义的uwsgi.ini文件
```
[uwsgi]
# 守护进程
master = true
# 进程个数
processes = 4
# 项目地址
chdir = /home/src/fresh_shop
# 指定python版本
pythonpath = /home/env/env1/bin/python3
# 指定uwsgi文件
module = fresh_shop.wsgi
# 和nginx通信地址:端口
socket = 127.0.0.1:8890
# 日志文件地址
logto = /home/logs/freshuwsgi.log
```
运行项目:
`/home/env/env1/bin/uwsgi --ini /home/conf/freshuwsgi.ini &`


