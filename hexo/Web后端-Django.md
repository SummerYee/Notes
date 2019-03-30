---
title: Django的初始化以及模型(1)
img: /img/66.jpg
categories: 
- WEB
tags:
- WEB后端
top: true
---
### 一.构建虚拟环境
#### 注:本篇方法通过virtualenv直接在命令行模式安装，另有通过[pycharm构建](https://ddf118.github.io/2019/01/02/pycharm构建Django/)
1. **安装virtualenv**

  `pip install virtualenv`

  若不成功 配置环境变量

2. **创建虚拟环境**

  `d:` `mkdir env`  `cd env`进入保存环境的文件夹再创建

  `virtualenv --no-site-package -p 绝对路径 name`

  `--no-site-package`:表示创建的虚拟环境为纯净的环境。

  若有多个python版本
  `-p 绝对路径 name`: 表示虚拟环境中使用的python版本

3. **pip使用**

  `pip list`：查看虚拟环境下安装的所有的包　

  `pip freeze`：查看虚拟环境重通过pip安装的包

  `pip install xxx`: 安装xxx

4. **激活虚拟环境**
  cd env/Scripts/ 进入虚拟环境文件夹
  激活：
  Windows: 直接执行activate命令
  Mac/Linux: 直接执行source activate命令
  在虚拟环境中安装依赖项:
```python
pip install django pymysql django-redis djangorestframework pillow
```
5. **退出虚拟环境**
	Windows/Mac/Linux: 直接执行deactivate

### 二.Django项目

1. **创建项目**
  `django-admin startproject 项目名称`

  ![3](/img/3.jpg)


2. **启动项目**
  启动命令: `python manage.py runserver`。默认IP为127.0.0.1  默认端口为8000
  该命令是运行项目，端口可以不用写，启动的时候会默认随机创建一个可以使用的端口

  修改启动端口: python manage.py runserver 端口
  修改IP和端口: python manage.py runserver IP:端口
  IP参数: 如果为0.0.0.0表示任何人都可以通过公网IP访问django项目
  端口PORT参数: 如果端口设置为80，表示该端口可以不用写

3. **创建app**
  `python manage.py startapp 名字`

### 三.Django模式

MVT模式是由MVC模式演变出来的。

**MVT/MTV:**
​	M: models.py,模型层,定义模型和数据库中的表之间的关联关系
​	V: views.py,视图层,定义业务逻辑的地方
​	T: templates,模板,定义HTML页面的地方
​	
补充:MVC模式是所有框架遵循的模式。
**MVC:**
​	M: 模型层
​	V: 模板，HTML页面
​	C: controller，控制器，写业务逻辑的地方

### 四.数据库准备工作
1. **settings.py中配置**
```python
'ENGINE': 'django.db.backends.mysql',  #ENGINE参数:选用mysql
'NAME': 'dj8', #NAME参数: 数据库名
'HOST': 'localhost',  #HOST参数: 访问数据库的地址
'PORT': 3306,  #PORT参数: 访问数据库的端口
'USER': 'root',  #USER参数: 访问用户
'PASSWORD': '123456',  #PASSWORD参数: 密码
'TIME_ZONE': 'Asia/Chongqing',  #TIME_ZONE:时区
'OPTIONS':{'isolation_level':None} #OPTIONS
```
[配置详见](https://docs.djangoproject.com/en/2.1/ref/settings/#databases)

```python
LANGUAGE_CODE = 'zh-hans' #设置为中文
TIME_ZONE = 'Asia/Chongqing' #时区设为重庆
```
2. **安装pymysql**
  使用pymysql连接数据库: 因为python3没有MySQLdb驱动，无法直接连接MySQL
  在工程目录的`__init__.py`文件中定义  `pymysql.install_as_MySQLdb()`
3. **迁移**
  第一次迁移: `python manege.py migrate`
  除开第一次迁移
  生成迁移文件: `python manage.py makemigrations`
  执行迁移文件: `python manage.py migrate`
4. **插入管理员账号**
  `python manage.py createsuperuser`

### 五.模型层

1. **概念**
objects: 管理器，默认模型的属性
ORM(objects relationship mapping): 对象关系映射

>添加新的M模型
在`models.py`中添加

>添加新的路由 
在`urls.py`中添加
`path('name/',views.name),`

>添加新的方法——CDUS
在`views.py`中添加
sql在Django中的方法:

2. **创建C:**
法一:`create()方法: 模型名.objects.create(字段1=值,字段2=值)`
`Student.objects.create(s_name='谢霆锋')`
法二:`save()方法: 模型对象.save()`

```python
stu = Student()
stu.s_name = '唐嫣'
stu.s_gender = 1
stu.save() 
```

3. **删除D:**
`delete()方法: 模型名.objects.filter(条件).delete()`
`Student.objects.filter(id=2).delete()`

4. **修改U:**
法一:`update()方法: 模型名.objects.filter(条件).update(字段1=值,字段2=值)`
`Student.objects.filter(id=1).update(s_name='谢霆锋')`
法二:`save()方法: 模型对象.save()`
```python
stu = Student.objects.filter(id=1).first()
stu.s_name = '杨幂'
stu.save()
```
update()和save()的区别: 是否更新使用auto_now约束条件的字段,即是否更新更新时间，法一不会更新，法二会更新。

5. **查询S:**
  **filter()方法:** 查询满足条件的信息: `模型名.objects.filter(条件)`
  **all()方法:** 查询所有: `模型名.objects.all()`
  **get()方法:** 查询指定的条件: `模型名.objects.get()`
  ​	只能返回唯一的一个对象结果
  ​	条件必须成立
  **first()方法:** 取出结果中的第一个对象: `模型名.objects.filter().first()`
  **exclude()方法:** 查询出不满足条件的信息: `模型名.objects.exclude(条件)`
  **order_by()方法:** 升序:`模型名.objects.order_by('id')`  降序:`模型名.objects.order_by('-id')`
  **values()方法:** 取查询对象中的字段: `模型名.objects.all().values()`
  **exists()方法:** 判断结果是否存在,存在返回True,否则返回False `模型名.objects.filter().exists()`
  **count()方法:** 计算结果的条数 `模型名.objects.filter().count()`
  **pk:** 主键（就是id）
  **运算符:**
  ​	语法: `字段__运算符`
  **模糊查询:**
  ​	contains: 包含
  ​	`Student.objects.filter(s_name__contains='小')`
  ​	startswith: 以什么开头
  ​	`Student.objects.filter(s_name__startswith='小')`
  ​	endswith: 以什么结束
  ​	`Student.objects.filter(s_name__endswith='嫣')`
  ​	in: 在某个范围之内
  ​	`Student.objects.filter(id__in=[1,2,3,4])`
  ​	`Student.objects.filter(pk__in=[1,2,3,4])`
  ​	gt,gte: 大于,大于等于
  ​	# 查询年龄大于等于18且小于20
  ​	`Student.objects.filter(s_age__gte=18, s_age__lt=20)`
  ​	lt,lte: 小于,小于等于
  ​	`Student.objects.filter(s_age__lt=20)`
  **聚合:**
  ​	`from django.db.models import Max, Avg, Min, Sum, Count`
  ​	语法: `模型名.objects.all().aggregate(Sum(字段))`
  ​  `Student.objects.all().aggregate(Max('s_age'))`
  ​	`Student.objects.all().aggregate(Avg('s_age'))`
  ​	`Student.objects.all().aggregate(Sum('s_age'))`
  **Q()	Q查询对象:**
  ​	`from django.db.models import Q`
  ​	与1: Q(条件1), Q(条件2)
  ​	`Student.objects.filter(Q(s_age__gte=18), Q(s_age__lt=20))`
  ​	与2: Q(条件1) & Q(条件2)
  ​	`Student.objects.filter(Q(s_age__gte=18) & Q(s_age__lt=20))`
  ​	或: Q(条件1) | Q(条件2)
  ​	`Student.objects.filter(Q(s_age__gte=18) | Q(s_age__lt=20))`
  ​	非: ~Q(条件1)
  ​	`Student.objects.filter(~Q(s_age__gte=18))`
  **F()	F查询对象:**
  ​	`from django.db.models import F`
  ​	用于比较两个字段,可使用+,-符号
  ​	`模型名.objects.filter(physics__gt=F('math')-10)`

### 六.模型设计
1. 迁移表名的定义 models.py
  指定db_table参数，表示模型迁移时，映射到数据库中的表名称
  如果没指定db_table参数,则数据库中模型映射的表名为: 应用名app_模型名称的小写
```python
    class Meta:
        db_table = 'course'
```
2. 字段类型  models.py
  CharField: 字符串类型
  IntegerField: 整型
  ImageField: 字符串类型,用于存图片
  BooleanField: 布尔， True 或者 False
  DateTimeField: 日期, 年月日时分秒
  DateField: 年月日
  TextField: 存储长文本内容, areatext标签
  FloatField: 浮点类型
  DecimalField: 浮点类型，限制最大长度和小数点后的长度
3. 约束 models.py
  unique: 是否唯一
  default: 默认值
  null: 是否可以为空
  primary_key: 主键
  auto_now_add: 创建时，默认赋值为当前时间
  auto_now: 创建或修改时，默认赋值为当前时间
  max_length: 最大长度
  related_name: 反向查询名称
  on_delete: 删除的约束条件
  ​	models.CASCADE 删除主表，从表也会被删
  ​	models.PROTECT 不让删除主表，从表可以删
  ​	models.SET_NULL 删除主表，从表的关联字段设置为空
4. 一对一，1:1 
  存储	 views.py
  ​	`stu_info = StudentInfo()`
  ​	关联字段存储1： `stu_info.关联字段 = 关联对象`
  ​	`stu_info.name = '大霸王'`
  ​	`stu_info.stu = Student.objects.get(id=5)`
  ​	关联字段存储2: `stu_info.关联字段_id = 主键id值`
  ​	`stu_info.stu_id = 5`
  查询 	 views.py
  ​	学生模型和学生拓展模型的定义，一对一
  ​	**查询1:学生对象查询拓展表对象:**  
  ​	获得学生对象
  ​	`stu1 = Student.objects.filter(s_name='唐嫣').first()`
  ​	通过学生对象查询拓展表信息
  ​	**没设置related_name参数时：**`学生对象.拓展模型名称的小写`
  ​      ​	法一:正向查询
  ​      ​	​      ​	`stu_info = StudentInfo.objects.filter(stu=stu1).first()`
  ​      ​		`stu_info = StudentInfo.objects.filter(stu_id=stu1.id).first()`
  ​      ​	法二：反向查询
  ​      ​	​      ​	`stu_info = stu1.studentinfo`
  ​	**设置related_name参数时：**`学生对象.related_name参数`
  ​		`stu_info = stu1.info`
  ​	**查询2:拓展表对象查询学生对象:** 
  ​	获得拓展表对象
  ​	`stu_info = StudentInfo.objects.filter(s_no='89757').first()`
  ​	通过拓展表对象查询学生信息
  ​	**没设置related_name参数时：**`拓展表对象.OneToOneField定义的字段`
  ​	`stu1 = stu_info.stu`
  ​	**设置related_name参数时：**`拓展表对象.OneToOneField定义的字段`
  ​	`stu1 = stu_info.stu`
  定义模型：OneToOneField: 定义在关联模型的任何一方都可以

5. 一对多
	存储
	​	#查询id=4的学生对象
	​	`stu = Student.objects.get(pk=4)`
	​	关联字段的存储1:` stu.关系字段 = 关联模型对象`
	​	`stu.g = Grade.objects.get(pk=1)`
	​	关联字段的存储2: `stu.关系字段_id = 关联表的主键id值`
	​	`stu.g_id = 1`
	查询:
	​	**查询1：学生查询班级**
	​			`stu = Student.objects.filter(s_name='小花').first()`
	​		**没设置related_name参数时：**`多的一方(学生)对象.关联字段`
	​			`grade = stu.g`
	​		**设置related_name参数时：**`多的一方(学生)对象.关联字段`
	​			`grade = stu.g`
	​		
	​	**查询2：班级查询学生**
	​			`grade = Grade.objects.filter(g_name='c1801').first()`
	​		**没设置related_name参数时：**`一的一方（班级）.关联模型名的小写_set.filter().all()`
	​			`stus = grade.student_set.all()`
	​		**设置related_name参数时：**`一的一方(班级).related_name参数.filter().all()`
	​			`stus = grade.stu.all()`
	定义模型：ForeignKey: 定义在多(N)的一方
6. 多对多
	存储：同一对多
	查询：
	​	**查询1：课程加入学生**
	​		`cou = Course.objects.filter(c_name='高数').first()`
	​		`stu = Student.objects.filter(s_name='杨幂').first()`
	​		**没设置related_name参数时：**`课程对象.关联字段`
	​		`cou.stu.add(stu)`
	​		**设置related_name参数时：**`课程对象.关联字段`
	​		`cou.stu.add(stu)`
	​	**查询2：学生加入课程**
	​		**没设置related_name参数时：**`学生对象.关联模型名的小写_set`
	​		`stu.course_set.add(cou)`
	​		**设置related_name参数时：**`学生对象.related_name参数`
	​		`stu.cou.add(cou)`
	定义模型：ManyToManyField: 定义在模型的任何一方都可以

### 7.模板
```
	解析模板文件的位置：
		TEMPALATES创建在与应用同级。再在里面创建模板文件。
		配置: 在settings.py文件中定义TEMPALTES的目录地址
		'DIRS':[]中添加`os.path.join(BASE_DIR, 'templates')`
	后端渲染模板：	views.py
		使用`render()`渲染模板
		传递参数给模板, render(request, 模板名, {key1:value1, key2: value2})
	前端渲染数据：	模板
		解析变量: `{{ 变量 }}`
		解析标签: `{% 标签 %} {% end标签 %}`
			标签: for
```

