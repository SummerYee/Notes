---
title: Django的一些玩法4
categories: 
- WEB
tags:
- WEB后端
---
# restful
### 一.概念：
REST是所有Web应用都应该遵守的架构设计指导原则。
Representational State Transfer，翻译是”表现层状态转化”。
REST核心: 资源， 状态转移， 统一接口

资源: 是REST最明显的特征,是指对某类信息实体的抽象，资源是服务器上一个可命名的抽象概念，资源是以名词为核心来组织的，首先关注的是名词。

状态转移: 是指客户端痛服务端进行交互的过程中，客户端能够通过对资源的表述，实现操作资源的目的

统一接口: REST要求，必须通过统一的接口来对资源执行各种操作。对于每个资源只能执行一组有限的操作。 比如，客户端通过HTTP的4个请求方式(POST, GET, PUT, PATCH)来操作资源，也就意味着不管你的url是什么，不管请求的资源是什么但操作的资源接口都是统一的。

1.api定义规范
API（Application Programming Interface,应用程序编程接口）是一些预先定义的函数，目的是提供应用程序与开发人员基于某软件或硬件得以访问一组例程的能力，而又无需访问源码，或理解内部工作机制的细节。
`http://xxx.com/api/`
2.资源
在RESTful架构中，每个网址代表一种资源（resource），所以网址中不能有动词，只能有名词，而且所用的名词往往与数据库的表格名对应。一般来说，数据库中的表都是同种记录的"集合"（collection），所以API中的名词也应该使用复数。
举例来说，有一个API提供动物园（zoo）的信息，还包括各种动物和雇员的信息，则它的路径应该设计成下面这样。
```python
https://api.example.com/v1/zoos

https://api.example.com/v1/animals

https://api.example.com/v1/employees
```
3.http请求方式
GET（SELECT）：从服务器取出资源（一项或多项）
```
127.0.0.1:8080/student/  GET 获取所有学生信息
127.0.0.1:8080/student/1/  GET 获取id是1的学生信息
```
POST（CREATE）：在服务器新建一个资源
`127.0.0.1:8080/student/  POST 创建学生信息`
PUT（UPDATE）：在服务器更新资源（客户端提供改变后的完整资源）——更新整个资源
`127.0.0.1:8080/student/1/  PUT 修改id为1的学生信息`
PATCH（UPDATE）：在服务器更新资源（客户端提供改变的属性）——执行某项操作并更新资源的某些字段，更新局部资源
`127.0.0.1:8080/student/1/xxx  PATCH 修改id为1的学生信息的xxx信息`
DELETE（DELETE）：从服务器删除资源
`127.0.0.1:8080/student/  DELETE 删除整个学生信息`
`127.0.0.1:8080/student/1/  DELETE 删除id为1的学生信息`
4.filter过滤
5.状态码
6.错误处理
### 二.Django中使用
1.django中安装
在终端命令行输入
`pip install djangorestframework`
`pip install django-filter  # Filtering support`
2.settings.py配置的修改
在工程目录中的settings.py文件的INSTALLED_APPS中需要添加rest_framework
```python
INSTALLED_APPS = [
	...

    'rest_framework',
]
```
3.在应用app中定义路由URL
定义路由需要注意：
使用router.register注册的url为资源，而且资源只能为名词不能为动词。
定义的资源不要加'/'斜杠，在访问地址的时候，URL中会默认的添加反斜杠'/'
```
from django.urls import path
from rest_framework.routers import SimpleRouter

from user import views

# 声明路由对象
router = SimpleRouter()
# 使用router注册名为'student'的资源
router.register('student', views.StuView)
urlpatterns = [
    path('login/', views.login, name='login'),
    path('index/', views.index, name='index'),
    path('logout/', views.logout, name='logout'),
    path('register/', views.register, name='register'),
    ...
]
# 路由地址加进urlpatterns
urlpatterns += router.urls
```
4.在视图views.py文件中定义StuView类
通过定义StuView并继承了mixins的方法，即可有对应个增删改查的方法。在StuView中还定义了两个变量queryset、serializer_class。使用框架写内容基本重构。
```python
from rest_framework import viewsets, mixins

from app.models import Student
from user.serializers import StuSerializer


class StuView(viewsets.GenericViewSet,
              mixins.ListModelMixin,  # 查看所有资源 GET
              mixins.RetrieveModelMixin,  # 查看指定id的资源 GET
              mixins.DestroyModelMixin,  # 删除指定的资源 DELETE
              mixins.CreateModelMixin,  # 创建资源 POST
              mixins.UpdateModelMixin):  # 修改资源 PUT/PATCH
    # 指定资源返回的内容
    queryset = Student.objects.all()
    # queryset = Student.objects.filter(is_delete=0)
    # 序列化queryset的结果
    serializer_class = StuSerializer

    # 查询所有信息时，过滤掉被删除的学生信息
    def get_queryset(self):
        queryset = self.queryset
        return queryset.filter(is_delete=0)
	# 重写删除方法
    def perform_destroy(self, instance):
        instance.is_delete = 1
        instance.save()
    # 重构retrieve方法
    def retrieve(self, request, *args, **kwargs):
        try:
            instance = self.get_object()
            serializer = self.get_serializer(instance)
            return Response(serializer.data)
        except Exception as e:
            return Response({'code': 404, 'msg': '获取失败', 'data': ''})
```
get_queryset方法:
get_queryset(self)：返回视图使用的查询集，是列表视图与详情视图获取数据的基础，默认返回queryset属性，可以重写，例如对查询结果集进行再次过滤，可是如上：
5.定义序列化类
序列化类需要继承ModelSerializer，使用ModelSerializer表明序列化整个Student模型，并且可以指定虚拟化哪些字段。
```python

from rest_framework import serializers

from app.models import Student


class StuSerializer(serializers.ModelSerializer):
	# 对s_name添加约束，定义校验字段
	s_name = serializers.CharField(max_length=10,
                                   min_length=2,
                                   error_messages={
                                       'required': '姓名必填',
                                       'max_length': '最大不能超过10字符',
                                       'min_length': '最小不能小于2字符'
                                   })
    class Meta:
        # 序列化的模型
        model = Student
        # 序列化哪些字段
        fields = ['s_name', 's_gender', 's_age', 'id']
        
	# to_representation方法
    def to_representation(self, instance):
    	# 序列化是会默认调用该方法，返回的结果为当前instance对象的序列化结果
        data = super(StuSerializer, self).to_representation(instance)
        # 
        data['s_gender'] = '男' if data['s_gender'] else '女'
        return data
```
to_representation方法——对序列化结果进行修改:
概念:序列化器的每个字段实际都是由该字段类型的to_representation方法决定格式的，可以通过重写该方法来决定格式。
如上重定义to_representation方法，把每一列数据（其中instance代表每一列数据）进行修改重组，然后返回，如下示例将返回文章的类型字段进行修改再填充回数据中进行返回。
6.重构响应结果Response:
先在utils下新建jsonrenderer.py文件
```python

from rest_framework.renderers import JSONRenderer


class MyJSONRenderer(JSONRenderer):
    """
    {
        'code': 200,
        'msg': '请求成功',
        'data': {data}
    }
    """
    # 重构render方法，data为数据
    def render(self, data, accepted_media_type=None, renderer_context=None):
    	# 从data中获取各种数据
    	# 若data中有数据
        try:
            code = data['code']
            msg = data['msg']
            result = data['data']
       	# 若data中没有数据，给个默认值
        except:
            code = 200
            msg = '请求成功'
            result = data
		# 修改响应内容
        my_data = {
            'code': code,
            'msg': msg,
            'data': result
        }
        return super().render(my_data, accepted_media_type, renderer_context)
```
在settings.py中加配置
```python
# 配置rest_framework的相关信息
REST_FRAMEWORK = {
    # 使用自定义的render方法
    'DEFAULT_RENDERER_CLASSES': (
        'utils.jsonrenderer.MyJSONRenderer',
    )
}
```
