---
title: pycharm构建Django
categories: 
- WEB
tags:
- WEB后端
---
### 1. 通过PyCharm创建Python项目 ---> hellodjango

### 2. 在Terminal的虚拟环境中安装依赖项 ---> 

   pip install django pymysql django-redis djangorestframework pillow #安装
   
   pip check  #检查是否冲突
   
   pip freeze > requirements.txt #导出python安装包环境

备注:`pip install -r requirements.txt`导入requirements文件
### 3. 创建Django项目 ---> 

   django-admin startproject hellodjango .

### 4. 运行项目 --->
   python manage.py runserver
   http://localhost:8000
   http://127.0.0.1:8000
   python manage.py runserver 0.0.0.0:80
   http://localhost
   http://127.0.0.1
   http://10.7.187.140

### 5. 修改配置文件 ---> hellodjango/settings.py
   LANGUAGE_CODE = 'zh-hans'
   TIME_ZONE = 'Asia/Chongqing'
   DATABASES = {
   'default': {
   ​	'BACKEND': '....mysql',
   ​	'NAME': ''
   ​	'HOST': '',
   ​	'PORT': 3306,
   ​	'USER': '',
   ​	'PASSWORD': '',
   ​	'TIME_ZONE': 'Asia/Chongqing',
   }
   }

### 6. 修改__init__.py文件
   import pymysql

pymysql.install_as_MySQLdb()

### 7. 生成Django自带的数据表
   python manage.py migrate

### 8. 创建后台管理员账号
   python manage.py createsuperuser
