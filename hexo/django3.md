---
title: Django的一些玩法3
categories: 
- WEB
tags:
- WEB后端
---

### 表单
一.概念：
Django表单的定义和模型的定义非常类似。表单的定义主要用于验证页面传递参数的正确性。比如账号注册页面，当用户在填写信息的时候，某些参数如果没有填写，或者填写错误，这些错误信息都可以通过表单校验展示出来。
二.操作
![QQ图片20190110171632](/img/QQ图片20190110171632.png)
1.1定义验证字段的表单类[forms.py]
```python
from django import forms

class StuForm(forms.Form):
	username = forms.CharField(max_length=10,
                               min_length=2,
                               required=True,
                               error_messages={
                                   'required': '姓名字段必填',
                                   'min_length': '不能少于2字符',
                                   'max_length': '不能超过10字符'
                               })
	icon = forms.ImageField(required=True,
                            error_messages={
                                  	'required': '头像必填'
                                })
    gender = forms.CharField(required=True,
                             error_messages={
                                     'required': '性别必填'
                                 })
    def clean(self):
    	pass
```
name参数定义:
forms.CharField——表单字符
forms.ImageField——表单图片
限制参数：
max_length——最大长度
min_length——最小长度
required=True——必须写,不能为空
error_messages={}——自定义错误信息
​	required
​	max_length
​	min_length
1.2校验表单中的name参数：
方法一：整体校验
```python
def clean(self):
    # 转换性别为1或者为0
    gender = self.cleaned_data.get('gender')
    if gender == '男':
    self.cleaned_data['gender'] = 1
    else:
    self.cleaned_data['gender'] = 0

    # 校验姓名是唯一的
    username = self.cleaned_data.get('username')
    stu = Student.objects.filter(s_name=username).first()
    if stu:
    raise forms.ValidationError({'username': '姓名重复'})
    return self.cleaned_data
```
方法二：分开校验
```python
def clean_username(self):
	# 只是校验姓名
	username = self.cleaned_data.get('username')
	stu = Student.objects.filter(s_name=username).first()
	if stu:
		raise forms.ValidationError('姓名重复')
	return self.cleaned_data['username']

def clean_gender(self):
	# 只是校验性别
	gender = self.cleaned_data.get('gender')
	if gender == '男':
		self.cleaned_data['gender'] = 1
	else:
		self.cleaned_data['gender'] = 0
	return self.cleaned_data['gender']
```
2.视图函数操作[views.py]
```python
def add_stu_info(request):
    if request.method == 'GET':
        return render(request, 'add_stus.html')
    if request.method == 'POST':
        # 将页面提交的内容交给StuForm表单做校验
        form = StuForm(request.POST, request.FILES)
        # 使用is_valid进行判断校验是否成功，字段校验成功返回True，否则False
        if form.is_valid():
            username = form.cleaned_data['username']
            icon = form.cleaned_data['icon']
            # 获取的gender字段为1或者是0
            gender = form.cleaned_data['gender']
            Student.objects.create(s_name=username,
                                   icon=icon,
                                   s_gender=gender)
            return HttpResponseRedirect(reverse('app:all_stu'))
        else:
            errors = form.errors
            return render(request, 'add_stus.html', {'errors': errors})
```
3.前端操作[add_stus.html]
```python
{% extends 'base_main.html' %}

{% block content %}
    <form action="" method="post" enctype="multipart/form-data">
        {% csrf_token %}
        <p>姓名:<input type="text" name="username">{{ errors.username.0 }}</p>
        <p>性别:<input type="text" name="gender">{{ errors.gender.0 }}</p>
        <p>图片:<input type="file" name="icon">{{ errors.icon.0 }}</p>
        <p><input type="submit" value="提交"></p>
    </form>
{% endblock %}
```
### CSRF
CSRF（Cross-site request forgery）跨站请求伪造。
产生背景: 在访问网站时，有可能被恶意的注入代码,执行某些请求，造成损失
防范: Django中在前端htm页面的form表单标签中添加{% csrf_token %}

### 日志
一.添加中间件
![QQ图片20190110175437](/img/QQ图片20190110175437.png)
1.定义中间件类[middleware.py]
```python
import logging

class LoggingMiddleware(MiddlewareMixin):

    def process_request(self, request):

        request.init_time = time.time()

    def process_response(self, request, response):
        try:
            # 请求到响应之间消耗时长
            count_time = time.time() - request.init_time
            # 请求地址和请求方式
            path = request.path
            method = request.method
            # 响应的状态码和内容
            status = response.status_code
            content = response.content
            # 日志记录的信息
            message = '%s %s %s %s %s' % (path, method, status, content, count_time)
            logger.info(message)
        except Exception as e:
            logger.critical('log error: %s' % e)
        return response
```
2.配置中间件[settings.py]
MIDDLEWARE = ['utils.middleware.LoggingMiddleware',]
3.配置日志[settings.py]
现在工程中创建一个log文件夹
```python
LOG_PATH = os.path.join(BASE_DIR, 'log')
LOGGING = {
    'version': 1,
    # 默认为True，禁用日志
    'disable_existing_loggers': False,
    # 定义formatters组件，定义存储日志中的格式
    'formatters': {
        'default': {
            'format': '%(asctime)s %(message)s'
        },
        'simple': {
            'format': '%(message)s'
        }
    },
    #  定义loggers组件，用于接收日志,并交给名为console的处理handlers去处理
    'loggers': {
        '': {
            'handlers': ['console'],
            'level': 'INFO'
        }
    },
    # 定义handlers组件，用户写入日志信息
    'handlers': {
        'console': {
            'level': 'INFO',
            # 定义存储日志的文件
            'filename': '%s/sys.log' % LOG_PATH,
            # 指定写入日志中信息的格式
            'formatter': 'default',
            # 指定日志文件超过5M就自动做备份
            'class': 'logging.handlers.RotatingFileHandler',
            'maxBytes': 5 * 1024 * 1024
        }
    }
}
```
4.关键字解释

- loggers
	Logger 为日志系统的入口。每个logger 是一个具名的容器，可以向它写入需要处理的消息。每个logger 都有一个日志级别。日志级别表示该logger 将要处理的消息的严重性。
	Python 定义以下几种日志级别：
	DEBUG：用于调试目的的底层系统信息
	INFO：普通的系统信息
	WARNING：表示出现一个较小的问题。
	ERROR：表示出现一个较大的问题。
	CRITICAL：表示出现一个致命的问题。
	日志级别等级CRITICAL > ERROR > WARNING > INFO > DEBUG > NOTSET
- handlers
	Handler 决定如何处理logger 中的每条消息。它表示一个特定的日志行为。
	与logger 一样，handler 也有一个日志级别。如果消息的日志级别小于handler 的级别，handler 将忽略该消息。
	Logger 可以有多个handler，而每个handler 可以有不同的日志级别。
- formatters
	日志记录需要转换成文本。
	Formatter 表示文本的格式。Fomatter 通常由包含日志记录属性的Python 格式字符串组成；
	你也可以编写自定义的fomatter 来实现自己的格式。
	如下展示了formatters格式: 
	![django_logging_model](/img/django_logging_model.png)
- filters
	Filter 用于对从logger 传递给handler 的日志记录进行额外的控制。

