---
title: flask项目部署
categories: 
- WEB
tags:
- WEB后端
---
## 一.基本配置
在服务器中首先需要完成以下五步基础环境搭建，才能开始部署操作：
步骤1：创建安装环境文件夹、配置信息文件夹、日志信息文件夹、项目代码文件夹。
`mkdir`
/home/env	/home/conf	/home/logs	/home/src
步骤2：上传项目代码到src文件夹中
通过xftp即可
步骤3：创建虚拟环境，并安装项目所需要的包
安装：`yum install python-virtualenv`
创建：在env文件夹里面`virtualenv --no-site-packages -p /usr/bin/python3 ihomeenv`
安装包：
1.绝对路径安装：
`/home/env/ihomeenv/bin/pip3 install -r /home/src/ihome/requirement.txt`
2.相对路径激活安装：
在flaskenv文件夹里面，激活虚拟环境
`source bin/activate` 
然后在安装指定项目中的requerement.txt文件
`install -r /home/src/ihome/requirement.txt`
步骤4：
在服务器上打开端口

## 二.测试环境中部署
### 直接启动项目
`.home/env/ihomeenv/bin/python3 /home/src/ihome/manage.py runserver -p 8080 -h 0.0.0.0`
### 通过脚本文件，启动
在src文件夹里面`vim ihome_start.sh`
在里面加入`/home/env/ihomeenv/bin/python3 /home/src/ihome/manage.py runserver -p 8080 -h 0.0.0.0`
给脚本文件授权 `chmod -R 777 ihome_start.sh`
通过nohup启动项目
`nohup ./ihome_start.sh &`

## 三.正式环境中部署
1.配置nginx+uwsgi参数
在conf文件夹里面创建两个配置文件
配置nginx `vim ihomenginx.conf`
```
server {
	listen		8080;
	server_name 120.77.174.68;

	access_log /home/logs/ihomeaccess.log;
	error_log /home/logs/ihomeerror.log;

	location / {
		include uwsgi_params;
		uwsgi_pass 127.0.0.1:8099;

		uwsgi_param UWSGI_CHDIR /home/src/ihome;
		uwsgi_param UWSGI_SCRIPT manage:app;
	}
}

```
配置uwsgi `vim ihomeuwsgi.ini`
```
[uwsgi]
master = true
socket = 127.0.0.1:8099
chdir = /home/src/ihome
pythonpath = /home/env/ajenv/bin/python3
callable = app
logto = /home/logs/ihomeuwsgi.log

```
2.配置nginx基本参数
`vim /etc/nginx/nginx.conf`
```
# For more information on configuration, see:
#   * Official English Documentation: http://nginx.org/en/docs/
#   * Official Russian Documentation: http://nginx.org/ru/docs/

user nginx;
worker_processes auto;
error_log /var/log/nginx/error.log;
pid /run/nginx.pid;

# Load dynamic modules. See /usr/share/nginx/README.dynamic.
include /usr/share/nginx/modules/*.conf;

events {
    worker_connections 1024;
}

http {
    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';

    access_log  /var/log/nginx/access.log  main;

    sendfile            on;
    tcp_nopush          on;
    tcp_nodelay         on;
    keepalive_timeout   65;
    types_hash_max_size 2048;

    include             /etc/nginx/mime.types;
    default_type        application/octet-stream;

    # Load modular configuration files from the /etc/nginx/conf.d directory.
    # See http://nginx.org/en/docs/ngx_core_module.html#include
    # for more information.
    include /etc/nginx/conf.d/*.conf;
	include /home/conf/*.conf;

    server {
        listen       80 default_server;
        listen       [::]:80 default_server;
        server_name  _;
        root         /usr/share/nginx/html;

        # Load configuration files for the default server block.
        include /etc/nginx/default.d/*.conf;
        #设置编码方式，防止中文乱码
		charset utf-8;

	#共享设置-在浏览器中输入服务器ip地址，会展示共享目录的文件列表
	#root /home/dyf118;#想要共享的目录地址(设置权限为)
	#location / {
	#	autoindex on;#打开显示目录的权限
	#	autoindex_exact_size on;#打开显示文件大小权限
	#	autoindex_localtime on;#打开显示修改时间权限
	#}	
	#默认设置-在浏览器中输入服务器ip地址，显示index.html页面
        #location / {
		#root html;
		#root /home/my_web/京东购物车;
		#index index.html index.htm;
        #}
		location / {
		}
	#自定义服务器页面
	#location / {
	#	root /home/ddf;#设置输入ip打开的文件对应的目录
	#	index fun.txt fun.tx;#设置输入ip需要打开的是哪个文件
	#}

        error_page 404 /404.html;
            location = /40x.html {
        }

        error_page 500 502 503 504 /50x.html;
            location = /50x.html {
        }
    }

# Settings for a TLS enabled server.
#
#    server {
#        listen       443 ssl http2 default_server;
#        listen       [::]:443 ssl http2 default_server;
#        server_name  _;
#        root         /usr/share/nginx/html;
#
#        ssl_certificate "/etc/pki/nginx/server.crt";
#        ssl_certificate_key "/etc/pki/nginx/private/server.key";
#        ssl_session_cache shared:SSL:1m;
#        ssl_session_timeout  10m;
#        ssl_ciphers HIGH:!aNULL:!MD5;
#        ssl_prefer_server_ciphers on;
#
#        # Load configuration files for the default server block.
#        include /etc/nginx/default.d/*.conf;
#
#        location / {
#        }
#
#        error_page 404 /404.html;
#            location = /40x.html {
#        }
#
#        error_page 500 502 503 504 /50x.html;
#            location = /50x.html {
#        }
#    }

}

```

3.在正式环境中使用nginx+uwsgi进行部署
步骤1：在manage.py启动文件中，添加访问首页的路由地址，如下所示：
```
# 启动首页地址
@app.route('/')
def home_index():
	return render_template('index.html')
```
步骤2：启动nginx
`systemctl restart nginx`
步骤3：启动uwsgi `指定虚拟环境中uwsgi的路径 --ini 指定xxuwsgi.ini路径`
`/home/env/ihomeenv/bin/uwsgi --ini /home/conf/ihomeuwsgi.ini &`

通过如上的测试，即可部署项目成功。如果访问网站出现‘502 bad gateway’表示网关超时，需要重新启动nginx或者uwsgi。如果出现500 服务器内部错误，则需要查看在xxuwsgi.ini中设置的uwsgi的日志文件。从这两个地方去排查错误，基本可以解决绝大部分的部署问题。
在logs文件夹里面`tail -f ihomeuwsgi.log`查看ihomeuwsgi的日志文件

## 其他适用指令
1.查看端口网络情况`netstat -lntp`
2.查看xxx网络情况 `ps aux | grep xxx`
3.杀进程`kill -9 PID`
4.nginx相关命令
```
#启动： systemctl start nginx
#暂停： systemctl stop nginx
#重启： systemctl restart nginx
#查看状态： systemctl status nginx
```

