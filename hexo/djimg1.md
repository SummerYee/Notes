---
title: Django的一些模板配置(2)
categories: 
- WEB
tags:
- WEB后端
---
```
### 一.配置
**1.使用pymysql连接数据库: 因为python3没有MySQLdb驱动，无法直接连接MySQL**
![dj0](C:\Users\Administrator\Desktop\django_img\dj0.png)
**2.添加名为'app'的app的应用setting.py** 
![dj1](C:\Users\Administrator\Desktop\django_img\dj1.png)
**3.创建模板文件夹templates以及添加模板 setting.py**
![dj2](C:\Users\Administrator\Desktop\django_img\dj2.png)
**4.数据库配置setting.py**
![dj3](C:\Users\Administrator\Desktop\django_img\dj3.png)
**5.创建static文件夹以及添加STATICFILES_DIRS setting.py**
![dj5](C:\Users\Administrator\Desktop\django_img\dj5.png)
**6.在urls.py中添加**
![dj4](C:\Users\Administrator\Desktop\django_img\dj4.png)
**7.在views.py中添加**
![dj6](C:\Users\Administrator\Desktop\django_img\dj6.png)

### 二.模板
```
1.解析模板文件的位置：
​	TEMPALATES创建在与应用同级。再在里面创建模板文件。
​	配置: 在settings.py文件中定义TEMPALTES的目录地址
​	'DIRS':[]中添加`os.path.join(BASE_DIR, 'templates')`
2.后端渲染模板：	views.py
​	使用`render()`渲染模板
​	render方法可接收三个参数，一是request参数，二是待渲染的html模板文件,三是保存具体数据的字典参数。
​	它的作用就是将数据填充进模板文件，最后把结果返回给浏览器。
​	传递参数给模板, render(request, 模板名, {key1:value1, key2: value2})
3.前端渲染数据：	模板
解析变量: `{{ 变量 }}`
​	标签for中自带的变量forloop
​	从1开始: {{ forloop.counter }}
​	从0开始: {{ forloop.counter0 }}
​	倒序1结束: {{ forloop.revcounter }}
​	倒序0结束: {{ forloop.revcounter0 }}
​	判断循环第一次: {{ forloop.first }}, 返回布尔值
​	判断循环最后一次: {{ forloop.last }}, 返回布尔值
解析标签: `{% 标签 %} {% end标签 %}`
​	标签: for
​	{% for i in xxx %} {% empty %}  {% endfor %}
​	标签: if
​	{% if name %} {% else %} {% endif %}
​	标签: ifequal
​	{% ifeuqal forloop.counter 值 %}  {% endifequal %}
​	标签:url
​	{% url '' %}
注解
​	web形式注解: <!--  注解内容-->, 需注意不能定义错误的标签语法
​	django框架中的单行注解: {# 注解内容 #}
​	django框架中的多行注解: {% comment %}  {% endcomment %}
4.引入静态文件
​	第一种: <link rel="stylesheet" href="/static/css/style.css">
​	第二种: {% load static %}    <link rel="stylesheet" href="{% static 'css/style.css' %}">
5.过滤器
​	语法: {{ a | 过滤器 }}
​	safe: 解析变量中的样式
​	upper/lower: 变量的大小写转换
​	default: 默认值
​	first/last: 取出变量中的首/尾元素
​	length: 计算变量的长度
6.继承
​	父模板(挖坑)
​		概念: 定义好可以被子模板动态填充内容的block块
​		定义block块的名字不能重复
		{% block 名称 %} {% endblock %}

​	子模板(填坑)
​		概念: 继承于父模板，并实现填充block块的内容
​		定义的block块的名字一定要存在于父模板中
​		继承: {% extends '父模板' %}
​		动态填充block块的内容: {% block 名称 %} {% endblock %}
​		获取父模板中坑之前定义好的内容: {{ block.super }}
```

### 三.路由URLS
1.配置路由分发
   ` path('app/', include('app.urls')),`
   ` path('app/', include(('app.urls', 'app'), namespace='app')),`
2.接收参数
​	**path**
​    接收整型:<int:id> 其中id参数为int类型
​    接收字符串: <str: name> 其中name参数为str类型
​    接收uuid类型: <uuid: uid> 其中uid参数为uuid类型的值
​    接收path路径: <path: path> 其中path参数为路径
​    注意: 接收参数的方法中，需要指定接收参数的变量名
​	**re_path**
​	re_path: /index/(\d+)/其中路由匹配的值为int类型
​	re_path('params/(\d+)/(\d+)/(\d+)/', views.params): 注意方法中接收参数的定义顺序和路由中定义的正则表达式的顺序一致
​	re_path('params/(?P<year>\d+)/(?P<month>\d+)/(?P<day>\d+)/', views.params): 定义路由中接收参数的名字，分别为year，month，day

### 四.请求request
1.请求方式method: GET,POST
​	GET方式: 用于查询数据
​	POST方式: 用于增、删、改数据
2.传参GET
​	获取get请求传递的参数
​	request.GET['key']
​	request.GET.get('key')
3.传参POST
​	获取post请求传递的参数
​	request.POST['key']
​	request.POST.get['key']
4.传参FILES
​	获取上传的文件、图片等内容
​	request.FILES['key]
​	request.FILES.get('key')
5.路径path
​	获取当前请求的路径
​	request.path
6.COOKIES
​	存储网站的一些基本信息: 如登录的信息
​	COOKIES存储的内容是存储在客户端(浏览器)
​	request.COOKIES['key']或request.COOKIES.get('key')
​	

### 五.响应response

1.HttpResponse: 响应字符串`HttpResponse(字符串)`
2.JsonResponse: 响应json，用于ajax异步请求`JsonResponse({'code': 200, 'msg': '请求成功'})`
3.render: 渲染页面 `render(request, template_name, context)`
​	request  请求体对象
​    template_name  模板路径
​    context  字典参数，用来填坑{}
此方法仅为渲染，地址不变
4.HttpResponseRedirect: 响应重定向：可以实现服务器内部的跳转(改变地址)
​	第一种: 硬编码跳转地址， HttpResponseRedirect('/app/index/1/')
`from django.urls import reverse`——从django中导入reverse
​	第二种: 反向解析跳转地址: HttpResponseRedirect(reverse('namespace:name', kwargs={key:value}))
5.反向解析

```python
# 在模板中进行反解析
# 在根urls中定义：
path(’apps/’, include((‘app.urls’, 'app'), namespace=’app’))
# 在应用app的子urls中定义：
path(’hello/<int：id>/’, views.hello, name=’sayhello’)
```
```python
# 在views中使用反向解析
HttpResponseRedirect(reverse(namespace:name, kwargs = {key1 : value1, key2 : value2}))
# kwargs 是字典
HttpResponseRedirect(reverse('app:sayhello'))
```
### 六.cookie与session
1.cookie
​	产生背景: 由于http无状态协议，导致后端无法知道当前发送请求的人是‘谁’
用于存储一些不是很重要的内容
​	存储: set_cookie(key, value, max_age=None,expris=None)
​		max_age :  整数，指定cookie过期时间，以秒为单位
​		exprise： 整数，指定过期时间，还支持是一个datetime或者timedelta，可以指定一个具体日期时间
​		设置10天后过期：`exprise=datetime.datetime.now() + timedelta(days=10) `
​		永不过期：exprise设置为None表示为永不过期
​	获取:GET.get(key)
​	删除: delete_cookie(key)
```python
# 设置cookie
res = HttpResponseRedirect(reverse('user:index'))
s = '1234567890qwertyuiopasdfghjklzxcvbnm'
token = ''
for i in range(20):
	token += random.choice(s)
res.set_cookie('token', token, max_age=30)
# 删除数据库中先前此用户创建的cookie值
UserToken.objects.filter(user_id=user.id).delete()
# 保存token到数据库user_token表中
UserToken.objects.create(user_id=user.id, token=token)
```
2.session
django迁移数据库后自带django_session表
​	产生背景: 由于cookie中存储空间有限，很容易被截取，因此cookie存重要内容不安全，需要使用session进行数据存储
​	向session中存数据: request.session[key]=value
​	取session中存的数据: request.session['key']或request.session.get('key')
​	删除cookie和session中所有信息: request.session.flush()
​	删除user_id键值对: del request.session['user_id']

![123](C:\Users\Administrator\Desktop\123.png)
3. 用户认证系统的cookies和session的工作流程：
![222](C:\Users\Administrator\Desktop\222.png)
### 七.django提供的加密解密密码方式
`from django.contrib.auth.hashers import make_password, check_password`
1. 校验加密密码 `check_password(输入的密码, 数据库的密码)`
2. 创建加密密码 `密码变量 = make_password(输入的密码)`

### 八.使用装饰器完成登录状态的校验

### 九.[中间件]()

### 十.分页
`page = int(request.GET.get('page', 1))`# 获取分页的脚码
使用切片实现 `stus = Student.objects.all()[((page-1)*3):(page*3)]`
使用Paginator实现
```python
	stus = Student.objects.all()
    pg = Paginator(stus, 3)
    stus = pg.page(page)
```
pg = Paginator(总数据, 分页条数)
取某一页信息: pg.page(某页)
page_range: 页码
has_previous: 是否有上一页
previous_page_number: 上一页的脚码
has_next: 是否有下一页
next_page_numebr: 下一页的脚码
number: 当前页
num_pages: 共多少页

### 十一.图片上传
前端模板: form表单中需要添加enctype="multipart/form-data"
后端: 获取图片request.FILES.get(key)或request.FILES['key']
模型定义: ImageField(upload_to='upload'), 需安装pip install Pillow
模板中解析: <img src='/media/{{ stu.icon }}'>
配置访问media文件的路由地址
导包: from django.contrib.staticfiles.urls import static
urlpatterns += static(MEDIA_URL, document_root=MEDIA_ROOT)
```