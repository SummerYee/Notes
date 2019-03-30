---
title: 数据分析1
categories: 
- 数据分析
tags:
- 数据分析
---
```
一.NumPy： 数组和⽮量计算
import numpy as np

基于NumPy的算法要⽐纯Python快10到100倍（甚⾄更快），并且使⽤的内存更少。

data = np.random.randn(2, 3)
ndarray是⼀个通⽤的同构数据多维容器，所有元素必须是相同类型的
# 取维度⼤⼩
data.shape —— (2, 3)
# 取数据数据类型
data.dtyp —— dtype('float64')
# 取维度
data.ndim —— 2

创建ndarray
arr1 = np.array([6, 7.5, 8, 0, 1])

zeros和ones分别可以创建指定⻓度或形状的全0或全1数组。
empty可以创建⼀个没有任何具体值的数组。np.empty返回的都是⼀些未初始化的垃圾值
np.zeros(10)
np.ones(5)
np.empty((2, 3, 2))

基本的索引和切⽚
切⽚[ : ]会给数组中的所有值赋值

布尔型索引
names = np.array(['Bob', 'Joe', 'Will', 'Bob', 'Will', 'Joe', 'Joe'])
data = np.random.randn(7, 4)
data[names == 'Bob'] —— 第一行第四行

数组转置和轴对换
转置是重塑的⼀种特殊形式，它返回的是源数据的视图（不会进⾏任何复制操作）
# reshape((3, 5)) 三行五列
arr = np.arange(15).reshape((3, 5))
arr.T —— 线代转置

利⽤np.dot计算矩阵内积

对于⾼维数组，transpose需要得到⼀个由轴编号组成的元组才能对这些轴进⾏转置

通⽤函数(ufunc)：快速的元素级数组函数
 np.sqrt() —— 开方
 np.exp() —— 指数
 np.maximum(x, y) —— 比较x，y的大小，并列出大的
 np.abs() —— 绝对值

返回浮点数数组的⼩数和整数部分 x,y = np.modf()
remainder, whole_part = np.modf(arr)
remainder —— 小数
whole_part —— 整数

将条件逻辑表述为数组运算
xarr = np.array([1.1, 1.2, 1.3, 1.4, 1.5])
yarr = np.array([2.1, 2.2, 2.3, 2.4, 2.5])
cond = np.array([True, False, True, True, False])
当cond中的值为True时，选取xarr的值，否则从yarr中选取
result = np.where(cond, xarr, yarr)
In [171]: result
Out[171]: array([ 1.1, 2.2, 1.3, 1.4, 2.5])

np.where(arr > 0, 2, -2)
true -> 2 ; false -> -2;

数学和统计⽅法
可以通过数组上的⼀组数学函数对整个数组或某个轴向的数据进⾏统计计算。sum、mean以
及标准差std等聚合计算（aggregation，通常叫做约简（reduction））
np.mean() —— 和
np.mean() —— 平均值
arr.mean(1)是“计算⾏的平均值”，(axis=1）
arr.sum(0)是“计算每列的和”（axis=0）
arr.cumsum() —— 所有元素的累积和
arr.cumprod() —— 所有元素的累积积

any⽤于测试数组中是否存在⼀个或多个True，⽽all则检查数组中所有值是否都是True, 这两个
⽅法也能⽤于⾮布尔型数组，所有⾮0元素将会被当做True

 arr.sort() —— 排序
 arr.sort(1) —— 行从小到大

唯⼀化以及其它的集合逻辑
找出数组中的唯⼀值并返回已排序的结果
 np.unique()

⽤于数组的⽂件输⼊输出
NumPy的内置⼆进制格式读写
np.save和np.load是读写磁盘数组数据的两个主要函数。默认情况下，数组是以未压缩的原始
⼆进制格式保存在扩展名为.npy的⽂件中的
In [213]: arr = np.arange(10)
In [214]: np.save('some_array', arr)
In [215]: np.load('some_array.npy')
Out[215]: array([0, 1, 2, 3, 4, 5, 6, 7, 8, 9])

通过np.savez可以将多个数组保存到⼀个未压缩⽂件中
将数据压缩，可以使⽤numpy.savez_compressed
伪随机数⽣成
⽤normal来得到⼀个标准正态分布的4×4样本数组

示例：随机漫步
⽤np.random模块⼀次性随机产⽣1000个“掷硬币”结果（即两个数中任选⼀个），将其分别设
置为1或－1，然后计算累计和
In [251]: nsteps = 1000
In [252]: draws = np.random.randint(0, 2, size=nsteps)
In [253]: steps = np.where(draws > 0, 1, -1)
In [254]: walk = steps.cumsum()
In [255]: walk.min()
Out[255]: -3
In [256]: walk.max()
Out[256]: 31
我们想要知道本次随机漫步需要多久才能距离初始0点⾄少10步远（任⼀⽅向均可）
In [257]: (np.abs(walk) >= 10).argmax()
Out[257]: 37

###################################################################################
二.pandas
pandas是专门为处理表格和混杂数据设计的，⽽NumPy更适合处理统⼀的数值数组数据。
import pandas as pd

pandas的数据结构介绍
两个主要数据结构：Series和DataFrame

Series
Series是⼀种类似于⼀维数组的对象，它由⼀组数据（各种NumPy数据类型）以及⼀组与之相
关的数据标签（即索引）组成
obj = pd.Series([4, 7, -5, 3])
obj.values —— 查看值
obj.index —— 查看索引
通过索引的⽅式选取Series中的单个或⼀组值
Series看成是⼀个定⻓的有序字典 索引 in Series
直接通过这个字典来创建Series
传⼊排好序的字典的键以改变顺序
NaN（即“⾮数字”（not a number），在pandas中，它⽤于表示缺失或NA值）
pandas的isnull和notnull函数可⽤于检测缺失数据
Series最重要的⼀个功能是，它会根据运算的索引标签⾃动对齐数据
Series对象本身及其索引都有⼀个name属性
 obj4.name = 'population'
 obj4.index.name = 'state'
Series的索引可以通过赋值的⽅式就地修改

DataFrame
DataFrame是⼀个表格型的数据结构，它含有⼀组有序的列，每列可以是不同的值类型（数
值、字符串、布尔值等），DataFrame既有⾏索引(index)也有列索引(columns)。
特别⼤的DataFrame，head⽅法会选取前五⾏ .head()
将DataFrame的列获取为⼀个Series
frame2['state']  frame2.year
⾏也可以通过位置或名称的⽅式进⾏获取
loc 通过名称查  frame3.loc[[2000,2001],['Ohio']] 名为2000,2001的行，Ohio列
iloc 通过索引值查 frame3.iloc[:2,0] 第0,1行，第0列
如果赋值的是⼀个Series，就会精确匹配DataFrame的索引，所有的空位都将被填上缺失值
为不存在的列赋值会创建出⼀个新列。关键字del⽤于删除列.
del frame2['eastern']
如果嵌套字典传给DataFrame，pandas就会被解释为：外层字典的键作为列，内层键则作为
⾏索引
对DataFrame进⾏转置（交换⾏和列） T
设置了DataFrame的index和columns的name属性，则这些信息也会被显示出来
values属性也会以⼆维ndarray的形式返回DataFrame中的数据 frame3.values

索引对象
Index对象是不可变的，因此⽤户不能对其进⾏修改
不可变可以使Index对象在多个数据结构之间安全共享
pandas对象的⼀个重要⽅法是reindex，其作⽤是创建⼀个新对象，它的数据符合新的索引。
时间序列这样的有序数据，重新索引时可能需要做⼀些插值处理。method选项即可达到此⽬
的，例如，使⽤ffill可以实现前向值填充
 obj3 = pd.Series(['blue', 'purple', 'yellow'], index=[0, 2, 4])
 obj3.reindex(range(6), method='ffill')
reindex可以修改（⾏）索引和列。只传递⼀个序列时，会重新索引结果的⾏
列可以⽤columns关键字重新索引

丢弃指定轴上的项
drop⽅法返回的是⼀个在指定轴上删除了指定值的新对象
对于DataFrame，可以删除任意轴上的索引值
通过传递axis=1或axis=‘columns’可以删除列的值
默认删除行 即(axis=0)

索引、选取和过滤
利⽤标签的切⽚运算与普通的Python切⽚运算不同，其末端是包含的
⽤⼀个值或序列对DataFrame进⾏索引其实就是获取⼀个或多个列

⽤loc和iloc进⾏选取
使⽤轴标签（loc）或整数索引（iloc），从DataFrame选择⾏和列的⼦集

算术运算和数据对齐
可以对不同索引的对象进⾏算术运算
对于DataFrame，对⻬操作会同时发⽣在⾏和列上
如果DataFrame对象相加，没有共⽤的列或⾏标签，结果都会是空

在算术⽅法中填充值
当⼀个对象中某个轴标签在另⼀个对象中找不到时填充⼀个特殊值（⽐如0）
相加时，没有重叠的位置就会产⽣NaN值
df1 + df2
df1.add(df2, fill_value=0) —— NaN以有的对象的值填充并+0

DataFrame和Series之间的运算
如果某个索引值在DataFrame的列或Series的索引中找不到，则参与运算的两个对象就会被重
新索引以形成并集

函数应⽤和映射
f = lambda x: x.max() - x.min()
frame.apply(f)
frame.apply(f, axis='columns')
传递axis=‘columns’到apply，这个函数会在每⾏执⾏
得到frame中各个浮点值的格式化字符串，使⽤applymap即可
Series有⼀个⽤于应⽤元素级函数的map⽅法

排序和排名
要对⾏或列索引进⾏排序（按字典顺序），可使⽤sort_index⽅法，它将返回⼀个已排序的新对象
降序排序
frame.sort_index(axis=1, ascending=False)
按值对Series进⾏排序，可使⽤其sort_values⽅法
排序时，任何缺失值默认都会被放到Series的末尾
排序⼀个DataFrame时，根据⼀个或多个列中的值进⾏排序。将⼀个或多个列的名字传递给
sort_values的by选项即可
rank为各组分配⼀个平均排名
根据值在原数据中出现的顺序给出排名
 obj.rank(method='first')
也可以按降序进⾏排名
 obj.rank(ascending=False, method='first')
DataFrame可以在⾏或列上计算排名

带有重复标签的轴索引
obj.index.is_unique
如果某个索引对应多个值，则返回⼀个Series；⽽对应单个值的，则返回⼀个标量值

汇总和计算描述统计
pandas对象拥有⼀组常⽤的数学和统计⽅法。它们⼤部分都属于约简和汇总统计，⽤于从
Series中提取单个值（如sum或mean）或从DataFrame的⾏或列中提取⼀个Series。
调⽤DataFrame的sum⽅法将会返回⼀个含有列的和的Series
传⼊axis=‘columns’或axis=1将会按⾏进⾏求和运算
NA值会⾃动被排除，除⾮整个切⽚（这⾥指的是⾏或列）都是NA。通过skipna选项可以禁⽤
该功能：

唯⼀值、值计数以及成员资格
unique，它可以得到Series中的唯⼀值数组
value_counts⽤于计算⼀个Series中各值出现的频率
isin⽤于判断⽮量化集合的成员资格
结果中的⾏标签是所有列的唯⼀值。后⾯的频率值是每个列中这些值的相应计数

#######################################################################
三.数据加载、存储和⽂件格式
读写⽂本格式的数据
pandas提供了⼀些⽤于将表格型数据读取为DataFrame对象的函数其中read_csv和
read_table⽤得最多
df = pd.read_csv('examples/ex1.csv')
# Windows !type —— 查看 \
# linux !cat —— 查看/

没有标题的⽂件，可以让pandas为其分配默认的列名，也可以⾃⼰定义列名
pd.read_csv('examples/ex2.csv', header=None) —— 这样第一行不会成为index
pd.read_csv('examples/ex2.csv', names=['a', 'b', 'c', 'd', 'message'])
假设你希望将message列做成DataFrame的索引。你可以明确表示要将该列放到索引4的位置
上，也可以通过index_col参数指定”message”
 pd.read_csv('examples/ex2.csv', names=names, index_col='message')

将多个列做成⼀个层次化索引，只需传⼊由列编号或列名组成的列表即可

有些表格可能不是⽤固定的分隔符去分隔字段的（⽐如空⽩符或其它模式）
虽然可以⼿动对数据进⾏规整，这⾥的字段是被数量不同的空⽩字符间隔开的。这种情况下，
你可以传递⼀个正则表达式作为read_table的分隔符。可以⽤正则表达式表达为\s+
# 由于列名⽐数据⾏的数量少，所以read_table推断第⼀列应该是DataFrame的索引
 result = pd.read_table('examples/ex3.txt', sep='\s+')

异形⽂件格式处理，你可以⽤skiprows跳过⽂件的第⼀⾏、第三⾏和第四⾏

缺失值处理是⽂件解析任务中的⼀个重要组成部分。缺失数据经常是要么没有（空字符串），
要么⽤某个标记值表示。默认情况下，pandas会⽤⼀组经常出现的标记值进⾏识别，⽐如NA
及NULL
字典的各列可以使⽤不同的NA标记值

逐块读取⽂本⽂件
在处理很⼤的⽂件时，或找出⼤⽂件中的参数集以便于后续处理时，可以读取⽂件的⼀⼩部分
或逐块对⽂件进⾏迭代
设置pandas显示地更紧些
 pd.options.display.max_rows = 10
如果只想读取⼏⾏（避免读取整个⽂件），通过nrows进⾏指定即可
 pd.read_csv('examples/ex6.csv', nrows=5)
要逐块读取⽂件，可以指定chunksize（⾏数）
 chunker = pd.read_csv('examples/ex6.csv', chunksize=1000)
read_csv所返回的这个TextParser对象使你可以根据chunksize对⽂件进⾏逐块迭代。⽐如说，
我们可以迭代处理ex6.csv，将值计数聚合到”key”列中
tot = pd.Series([])
for piece in chunker:
	tot = tot.add(piece['key'].value_counts(), fill_value=0)
tot = tot.sort_values(ascending=False)
tot[:10]

将数据写出到⽂本格式
数据也可以被输出为分隔符格式的⽂本
DataFrame的to_csv⽅法，我们可以将数据写到⼀个以逗号分隔的⽂件中
使⽤其他分隔符（由于这⾥直接写出到sys.stdout，所以仅仅是打印出⽂本结果⽽已）
import sys
data.to_csv(sys.stdout, sep='|')
缺失值在输出结果中会被表示为空字符串。你可能希望将其表示为别的标记值
 data.to_csv(sys.stdout, na_rep='NULL')
没有设置其他选项，则会写出⾏和列的标签。当然，它们也都可以被禁⽤
data.to_csv(sys.stdout, index=False, header=False)
你还可以只写出⼀部分的列，并以你指定的顺序排列
data.to_csv(sys.stdout, index=False, columns=['a', 'b', 'c'])

处理分隔符格式
JSON数据
pandas.read_json可以⾃动将特别格式的JSON数据集转换为Series或DataFrame
从pandas输出到JSON，使⽤to_json⽅法

XML和HTML：Web信息收集
pandas有⼀个内置的功能，read_html，它可以使⽤lxml和Beautiful Soup⾃动将HTML⽂件
中的表格解析为DataFrame对象。
安装read_html⽤到的库
conda install lxml
pip install beautifulsoup4 html5lib
tables = pd.read_html('examples/fdic_failed_bank_list.html')
failures = tables[0]
做⼀些数据清洗和分析，⽐如计算按年份计算倒闭的银⾏数
close_timestamps = pd.to_datetime(failures['Closing Date'])
close_timestamps.dt.year.value_counts()

⼆进制数据格式
pandas对象都有⼀个⽤于将数据以pickle格式保存到磁盘上的to_pickle⽅法
frame = pd.read_csv('examples/ex1.csv')
frame.to_pickle('examples/frame_pickle')
注意： pickle仅建议⽤于短期存储格式。其原因是很难保证该格式永远是稳定的；今天pickle
的对象可能⽆法被后续版本的库unpickle出来

使⽤HDF5格式
HDF5是⼀种存储⼤规模科学数组数据的⾮常好的⽂件格式。它可以被作为C标准库，带有许
多语⾔的接⼝，如Java、Python和MATLAB等。HDF5中的HDF指的是层次型数据格式
（hierarchical data format）。每个HDF5⽂件都含有⼀个⽂件系统式的节点结构，它使你能
够存储多个数据集并⽀持元数据。与其他简单格式相⽐，HDF5⽀持多种压缩器的即时压缩，
还能更⾼效地存储重复模式数据。对于那些⾮常⼤的⽆法直接放⼊内存的数据集，HDF5就是
不错的选择，因为它可以⾼效地分块读写。
 store = pd.HDFStore('mydata.h5') —— 生成mydata.h5文件
HDF5⽂件中的对象可以通过与字典⼀样的API进⾏获取
HDFStore⽀持两种存储模式，’fixed’和’table’。后者通常会更慢，但是⽀持使⽤特殊语法进⾏查询操作
pandas.read_hdf函数可以快捷使⽤这些⼯具

读取Microsoft Excel⽂件
pandas的ExcelFile类或pandas.read_excel函数⽀持读取存储在Excel 2003（或更⾼版本）中
的表格型数据。这两个⼯具分别使⽤扩展包xlrd和openpyxl读取XLS和XLSX⽂件。你可以⽤
pip或conda安装它们
将pandas数据写⼊为Excel格式
 writer = pd.ExcelWriter('examples/ex2.xlsx')
 frame.to_excel(writer, 'Sheet1')
 writer.save()
也可以这样存
 frame.to_excel('examples/ex2.xlsx')

Web APIs交互 —— 可以读取接口数据
In [113]: import requests
In [114]: url = 'http://127.0.0.1:8085/analysis/info/'
In [115]: resp = requests.get(url)
In [116]: resp
Out[116]: <Response [200]>
In [117]: data = resp.json()
In [118]: data

数据库交互 
pandas 读取本地数据并导入数据库
在商业场景下，⼤多数数据可能不是存储在⽂本或Excel⽂件中。基于SQL的关系型数据库
（如SQL Server、PostgreSQL和MySQL等）使⽤⾮常⼴泛。

SQLAlchemy项⽬是⼀个流⾏的Python SQL⼯具，它抽象出了SQL数据库中的许多常⻅差异。pandas有⼀个read_sql
函数，可以让你轻松的从SQLAlchemy连接读取数据

import sqlalchemy as sqla
import pymysql

db=sqla.create_engine('mysql+pymysql://root:123456@127.0.0.1/movie_db?charset=utf8')
pd.read_sql('select * from product', db)

#############################################################################
四.数据清洗和准备
在数据分析和建模的过程中，相当多的时间要⽤在数据准备上：加载、清理、转换以及重塑。
这些⼯作会占到分析师时间的80%或更多。

处理缺失数据

检测缺失数据 isnull()
在统计应⽤中，NA数据可能是不存在的数据或者虽然存在，但是没有观察到（例如，数据采
集中发⽣了问题）。当进⾏数据清洗以进⾏分析时，最好直接对缺失数据进⾏分析，以判断数
据采集的问题或缺失数据可能导致的偏差。
滤除缺失数据
from numpy import nan as NA

data.dropna()
data[data.notnull()]
DataFrame对象，dropna默认丢弃任何含有缺失值的⾏
传⼊how=‘all’将只丢弃全为NA的那些⾏/列
data.dropna(how='all')
data.dropna(axis=1, how='all')
# 删除⼩于n个⾮空值的⾏
 df.dropna(thresh=2)

填充缺失数据
fillna⽅法是最主要的函数。通过⼀个常数调⽤fillna就会将缺失值替换为那个常数值
df.fillna(0)
通过⼀个字典调⽤fillna，就可以实现对不同的列填充不同的值
 df.fillna({1: 0.5, 2: 0})
fillna默认会返回新对象，但也可以对现有对象进⾏就地修改
 _ = df.fillna(0, inplace=True)
对reinde行有效的那些插值⽅法也可⽤于fillna
 df.fillna(method='ffill', limit=2)
传⼊Series的平均值或中位数
data.fillna(data.mean())

数据转换
移除重复数据
DataFrame的duplicated⽅法返回⼀个布尔型Series，表示各⾏是否是重复⾏
data.duplicated()
drop_duplicates⽅法，它会返回⼀个DataFrame，重复的数组会标为False
data.drop_duplicates()
duplicated和drop_duplicates默认保留的是第⼀个出现的值组合。传⼊keep=‘last’则保留最
后⼀个

利⽤函数或映射进⾏数据转换
根据数组、Series或DataFrame列中的值来实现转换⼯作

重命名轴索引
str.title 首字母大写
str.upper 所有字母大写
data.rename(index=str.title, columns=str.upper)
rename可以结合字典型对象实现对部分轴标签的更新
就地修改某个数据集，传⼊inplace=True即可

离散化和⾯元划分
为了便于分析，连续数据常常被离散化或拆分为“⾯元”（bin）。假设有⼀组⼈员数据，⽽你
希望将它们划分为不同的年龄组
ages = [20, 22, 25, 27, 21, 23, 37, 31, 61, 45, 41, 32]
将这些数据划分为“18到25”、“26到35”、“35到60”以及“60以上”⼏个⾯元
bins = [18, 25, 35, 60, 100]
cats = pd.cut(ages, bins)
Out[78]: 
[(18, 25], (18, 25], (18, 25], (25, 35], (18, 25], ..., (25, 35], (60, 100], 
(35,60], (35, 60], (25, 35]]
Length: 12
Categories (4, interval[int64]): [(18, 25] < (25, 35] < (35, 60] < (60, 100]]
pandas返回的是⼀个特殊的Categorical对象。结果展示了pandas.cut划分的⾯元。你可以将
其看做⼀组表示⾯元名称的字符串
cats.codes
cats.categories
# ⾯元计数
pd.value_counts(cats)
跟“区间”的数学符号⼀样，圆括号表示开端，⽽⽅括号则表示闭端（包括）。哪边是闭端可以
通过right=False进⾏修改

向cut传⼊的是⾯元的数量⽽不是确切的⾯元边界，则它会根据数据的最⼩值和最⼤值计算等
长⾯元。下⾯这个例⼦中，我们将⼀些均匀分布的数据分成四组，选项precision=2，限定⼩
数只有两位。
In [85]: data = np.random.rand(20)
In [86]: cat1 = pd.cut(data, 4, precision=2)
Out[86]: [(0.76, 1.0], (0.28, 0.52], (0.76, 1.0], (0.52, 0.76], (0.037, 0.28], ..., (0.28, 0.52], (0.28, 0.52], (0.52, 0.76], (0.037, 0.28], (0.037, 0.28]]
Length: 20
Categories (4, interval[float64]): [(0.037, 0.28] < (0.28, 0.52] < (0.52, 0.76]
< (0.76, 1.0]]

qcut是⼀个⾮常类似于cut的函数，它可以根据样本分位数对数据进⾏⾯元划分。
cat2 = pd.qcut(data, 4, precision=2)
[(0.71, 1.0], (0.4, 0.71], (0.71, 1.0], (0.4, 0.71], (0.027999999999999997, 0.18
], ..., (0.18, 0.4], (0.18, 0.4], (0.4, 0.71], (0.027999999999999997, 0.18], (0.
027999999999999997, 0.18]]
Length: 20
Categories (4, interval[float64]): [(0.027999999999999997, 0.18] < (0.18, 0.4] <
 (0.4, 0.71] < (0.71, 1.0]]

cut:每个面元长度都一样，数量不一样
In [300]: pd.value_counts(cat1)
Out[300]:
(0.037, 0.28]    7
(0.52, 0.76]     5
(0.76, 1.0]      4
(0.28, 0.52]     4
dtype: int64
qcut:每个面元中的数据的数量是一样的，长度不一样
In [298]: pd.value_counts(cat2)
Out[298]:
(0.71, 1.0]                     5
(0.4, 0.71]                     5
(0.18, 0.4]                     5
(0.027999999999999997, 0.18]    5
dtype: int64


检测和过滤异常值
 data.describe() —— 数据信息大体分析

选出全部含有“超过3或－3的值”的⾏
 data[(np.abs(data) > 3).any(1)]
np.sign(data)可以⽣成1和-1

排列和随机采样
利⽤numpy.random.permutation函数可以轻松实现对Series或DataFrame的列的排列⼯作
（permuting，随机重排序）
df = pd.DataFrame(np.arange(20).reshape((5, 4)))
sampler = np.random.permutation(5)
# 按sampler重新对行索引排序 
df.take(sampler)
# 随机三行
df.sample(n=3)

要通过替换的⽅式产⽣样本（允许重复选择），可以传递replace=True到sample
In [106]: choices = pd.Series([5, 7, -1, 6, 4])
In [107]: draws = choices.sample(n=10, replace=True)
In [108]: draws

计算指标/哑变量
将分类变量（categorical variable）转换为“哑变量”或“指标矩阵
构建指标DataFrame的⽅法之⼀是从⼀个全零DataFrame开始

pandas的⽮量化字符串函数
通过data.map，所有字符串和正则表达式⽅法都能被应⽤于（传⼊lambda表达式或其他函
数）各个值，但是如果存在NA（null）就会报错。为了解决这个问题，Series有⼀些能够跳过
NA值的⾯向数组⽅法，进⾏字符串操作。通过Series的str属性即可访问这些⽅法。例如，我
们可以通过str.contains检查各个电⼦邮件地址是否含有"gmail"：

#######################################################################
五. 数据规整：聚合、合并和重塑







#####################飞常准项目
In [113]: import requests
In [114]: url = 'http://127.0.0.1:8085/analysis/info/'
In [115]: resp = requests.get(url)
result = resp.json()
ddf = pd.DataFrame(result)
# 根据航班号去重 —— 得到所有航班
ddf.drop_duplicates(['FlightNo'])
# 根据航空公司名称去重 —— 得到所有航空公司名称
companies = ddf.drop_duplicates(['FlightCompany'])
# 输出本地json
companies.to_json('xxx.json')
# 展示出来
companies.to_csv(sys.stdout, sep='|')

import sqlalchemy as sqla
import pymysql

db=sqla.create_engine('mysql+pymysql://root:123456@127.0.0.1/fly?charset=utf8')
info = pd.read_sql('select * from fly_info', db)
name = info.drop_duplicates(['FlightCompany'])
name['FlightCompany']



######################################
import matplotlib.pyplot as plt
plt.show()
#############练习1来⾃Bitly的USA.gov数据
# 取数据 list
path = 'datasets/bitly_usagov/example.txt'
records = [json.loads(line) for line in open(path)]
frame = pd.DataFrame(records)
对Series使⽤value_counts⽅法
tz_counts = frame['tz'].value_counts()
先给记录中未知或缺失的时区填上⼀个替代值。fillna函数可以替换缺失值（NA），⽽未知值（空字符串）则可以通过布尔型数组索引加以替换
clean_tz = frame['tz'].fillna('Missing')
clean_tz[clean_tz == ''] = 'Unknown'
将这种字符串的第⼀节（与浏览器⼤致对应）分离出来并得到另外⼀份⽤户⾏为摘要
results = pd.Series([x.split()[0] for x in frame.a.dropna()])
results.value_counts()[:8]
由于有的agent缺失，所以⾸先将它们从数据中移除
cframe = frame[frame.a.notnull()]
然后计算出各⾏是否含有Windows的值 np.where
cframe['os'] = np.where(cframe['a'].str.contains('Windows'),'Windows', 'Not Windows')
根据时区和新得到的操作系统列表对数据进⾏分组
 by_tz_os = cframe.groupby(['tz', 'os'])
 by_tz_os.size()
分组计数，类似于value_counts函数，可以⽤size来计算。并利⽤unstack对计数结果进⾏重塑
 agg_counts = by_tz_os.size().unstack().fillna(0)
根据agg_counts中的⾏数构造了⼀个间接索引数组
indexer = agg_counts.sum(1).argsort()
通过take按照这个顺序截取了最后10⾏最⼤值
count_subset = agg_counts.take(indexer[-10:])
pandas有⼀个简便⽅法nlargest，可以做同样的⼯作
agg_counts.sum(1).nlargest(10)
传递⼀个额外参数到seaborn的barpolt函数，来画⼀个堆积条形图
count_subset = count_subset.stack()
count_subset.name = 'total'
count_subset = count_subset.reset_index()
sns.barplot(x='total', y='tz', hue='os', data=count_subset)

这张图不容易看出Windows⽤户在⼩分组中的相对⽐例，因此标准化分组百分⽐之和为1
def norm_total(group):
	group['normed_total'] = group.total / group.total.sum()
	return group
results = count_subset.groupby('tz').apply(norm_total)
sns.barplot(x='normed_total', y='tz', hue='os', data=results)

还可以⽤groupby的transform⽅法，更⾼效的计算标准化的和
g = count_subset.groupby('tz')
results2 = count_subset.total / g.total.transform('sum')

######################练习2MovieLens 1M数据集
pd.options.display.max_rows = 10

unames = ['user_id', 'gender', 'age', 'occupation', 'zip']
users = pd.read_table('datasets/movielens/users.dat', sep='::', header=None, names=unames, engine='python')

rnames = ['user_id', 'movie_id', 'rating', 'timestamp']
ratings = pd.read_table('datasets/movielens/ratings.dat', sep='::', header=None, names=rnames, engine='python')

mnames = ['movie_id', 'title', 'genres']
movies = pd.read_table('datasets/movielens/movies.dat', sep='::', header=None, names=mnames, engine='python')

想要根据性别和年龄计算某部电影的平均得分，如果将所有数据都合并到⼀个表中的话问题就
简单多了。我们先⽤pandas的merge函数将ratings跟users合并到⼀起，然后再将movies也合
并进去。pandas会根据列名的重叠情况推断出哪些列是合并（或连接）键
data = pd.merge(pd.merge(ratings, users), movies)
按性别计算每部电影的平均得分，我们可以使⽤pivot_table⽅法
mean_ratings = data.pivot_table('rating', index='title',columns='gender', aggfunc='mean')
####groupby分组
data.groupby(['gender','age','title'])['rating'].mean()
过滤掉评分数据不够250条的电影（随便选的⼀个数字）。为了达到这个⽬的，先对title进⾏
分组，然后利⽤size()得到⼀个含有各电影分组⼤⼩的Series对象
ratings_by_title = data.groupby('title').size()
active_titles = ratings_by_title.index[ratings_by_title >= 250]
###### ratings_by_title[ratings_by_title >= 250]
标题索引中含有评分数据⼤于250条的电影名称，然后我们就可以据此从前⾯的mean_ratings
中选取所需的⾏了
 mean_ratings = mean_ratings.loc[active_titles]
为了了解⼥性观众最喜欢的电影，我们可以对F列降序排列
 top_female_ratings = mean_ratings.sort_values(by='F', ascending=False)

计算评分分歧
要找出男性和⼥性观众分歧最⼤的电影。⼀个办法是给mean_ratings加上⼀个⽤于存放平均得分之差的列，并对其进⾏排序
 mean_ratings['diff'] = mean_ratings['M'] - mean_ratings['F']
按”diff”排序即可得到分歧最⼤且⼥性观众更喜欢的电影
 sorted_by_diff = mean_ratings.sort_values(by='diff')
对排序结果反序并取出前10⾏，得到的则是男性观众更喜欢的电影
 sorted_by_diff[::-1][:10]

如果只是想要找出分歧最⼤的电影（不考虑性别因素），则可以计算得分数据的⽅差或标准
差，在统计描述中，⽅差⽤来计算每⼀个变量（观察值）与总体均数之间的差异。
rating_std_by_title = data.groupby('title')['rating'].std()
rating_std_by_title = rating_std_by_title.loc[active_titles]
rating_std_by_title.sort_values(ascending=False)[:10]

###################### 练习31880-2010年间全美婴⼉姓名
由于该数据集按年度被分隔成了多个⽂件，所以第⼀件事情就是要将所有数据都组装到⼀个
DataFrame⾥⾯，并加上⼀个year字段。使⽤pandas.concat即可达到这个⽬的
years = range(1880, 2011)
pieces = []
columns = ['name', 'sex', 'births']
for year in years:
	path = 'datasets/babynames/yob%d.txt' % year
	frame = pd.read_csv(path, names=columns)
	frame['year'] = year
	pieces.append(frame)
names = pd.concat(pieces, ignore_index=True)
# 查看信息
names.info()
这⾥需要注意⼏件事情。第⼀，concat默认是按⾏将多个DataFrame组合到⼀起的；第⼆，必
须指定ignore_index=True，因为我们不希望保留read_csv所返回的原始⾏号。
利⽤groupby或pivot_table在year和sex级别上对其进⾏聚合了
 total_births = names.pivot_table('births', index='year',columns='sex', aggfunc=sum)
 total_births.plot(title='Total births by sex and year')
#############
ddf = names.groupby(['year','sex'])['births'].sum()
info = ddf.unstack()
info.plot(title='Total births by sex and year')
#############
插⼊⼀个prop列，⽤于存放指定名字的婴⼉数相对于总出⽣数的⽐例
def add_prop(group):
	group['prop'] = group.births / group.births.sum()
	return group
names = names.groupby(['year', 'sex']).apply(add_prop)
在执⾏这样的分组处理时，⼀般都应该做⼀些有效性检查，⽐如验证所有分组的prop的总和是否为1
names.groupby(['year', 'sex']).prop.sum()
⼯作完成。为了便于实现更进⼀步的分析，我需要取出该数据的⼀个⼦集：每对sex/year组合
的前1000个名字。这⼜是⼀个分组操作
def get_top1000(group):
	return group.sort_values(by='births', ascending=False)[:1000]
grouped = names.groupby(['year', 'sex'])
top1000 = grouped.apply(get_top1000)

top1000.reset_index(inplace=True, drop=True)

分析命名趋势
⾸先将前1000个名字分为男⼥两个部分
 boys = top1000[top1000.sex == 'M']
girls = top1000[top1000.sex == 'F']
⽣成⼀张按year和name统计的总出⽣数透视表
total_births = top1000.pivot_table('births', index='year',columns='name',aggfunc=sum)
 subset = total_births[['John', 'Harry', 'Mary', 'Marilyn']]
 subset.plot(subplots=True, figsize=(12, 10), grid=False,title="Number of births per year")

评估命名多样性的增长
计算最流⾏的1000个名字所占的⽐例，按year和sex进⾏聚合并绘图
table = top1000.pivot_table('prop', index='year',columns='sex', aggfunc=sum)
table.plot(title='Sum of table1000.prop by year and sex',yticks=np.linspace(0, 1.2, 13), xticks=range(1880, 2020, 10))
从图中可以看出，名字的多样性确实出现了增长（前1000项的⽐例降低）
另⼀个办法是计算占总出⽣⼈数前50%的不同名字的数量，这个数字不太好计算。我们只考虑
2010年男孩的名字
 df = boys[boys.year == 2010]
在对prop降序排列之后，我们想知道前⾯多少个名字的⼈数加起来才够50%。虽然编写⼀个
for循环确实也能达到⽬的，但NumPy有⼀种更聪明的⽮量⽅式。先计算prop的累计和
cumsum，然后再通过searchsorted⽅法找出0.5应该被插⼊在哪个位置才能保证不破坏顺序
 prop_cumsum = df.sort_values(by='prop',ascending=False).prop.cumsum()
 prop_cumsum.values.searchsorted(0.5)
由于数组索引是从0开始的，因此我们要给这个结果加1，即最终结果为117
def get_quantile_count(group, q=0.5):
	group = group.sort_values(by='prop', ascending=False)
	return group.prop.cumsum().values.searchsorted(q) + 1
diversity = top1000.groupby(['year', 'sex']).apply(get_quantile_count)
diversity = diversity.unstack('sex')
diversity.plot()

“最后⼀个字⺟”的变⾰
近百年来，男孩名字在最后⼀个字⺟上的分布发⽣了显著的变化
⾸先将全部出⽣数据在年度、性别以及末字⺟上进⾏了聚合
get_last_letter = lambda x: x[-1]
last_letters = names.name.map(get_last_letter)
last_letters.name = 'last_letter'
table = names.pivot_table('births', index=last_letters,columns=['sex', 'year'], aggfunc=sum)
选出具有⼀定代表性的三年，并输出前⾯⼏⾏
 subtable = table.reindex(columns=[1910, 1960, 2010], level='year')
subtable.head
按总出⽣数对该表进⾏规范化处理，以便计算出各性别各末字⺟占总出⽣⼈数的⽐例
subtable.sum()

⽣成⼀张各年度各性别的条形图
fig, axes = plt.subplots(2, 1, figsize=(10, 8))
letter_prop['M'].plot(kind='bar', rot=0, ax=axes[0], title='Male')
letter_prop['F'].plot(kind='bar', rot=0, ax=axes[1], title='Female',
 legend=False)
可以看出，从20世纪60年代开始，以字⺟”n”结尾的男孩名字出现了显著的增长
回到之前创建的那个完整表，按年度和性别对其进⾏规范化处理，并在男孩名字中选取⼏个字
⺟，最后进⾏转置以便将各个列做成⼀个时间序列
letter_prop = table / table.sum()
dny_ts = letter_prop.loc[['d', 'n', 'y'], 'M'].T
dny_ts.head()
通过其plot⽅法绘制出⼀张趋势图
dny_ts.plot()

变成⼥孩名字的男孩名字
另⼀个有趣的趋势是，早年流⾏于男孩的名字近年来“变性了”，例如Lesley或Leslie。回到
top1000数据集，找出其中以”lesl”开头的⼀组名字
all_names = pd.Series(top1000.name.unique())
lesley_like = all_names[all_names.str.lower().str.contains('lesl')]
lesley_like
利⽤这个结果过滤其他的名字，并按名字分组计算出⽣数以查看相对频率
 filtered = top1000[top1000.name.isin(lesley_like)]
filtered.groupby('name').births.sum()
按性别和年度进⾏聚合，并按年度进⾏规范化处理
table = filtered.pivot_table('births', index='year',columns='sex', aggfunc='sum')
table = table.div(table.sum(1), axis=0)
table.tail()
table.plot(style={'M': 'k-', 'F': 'k--'})

################################USDA⻝品数据库
美国农业部（USDA）制作了⼀份有关⻝物营养信息的数据库。
 import json
db = json.load(open('datasets/usda_food/database.json'))
db中的每个条⽬都是⼀个含有某种⻝物全部数据的字典。nutrients字段是⼀个字典列表，其中
的每个字典对应⼀种营养成分
 db[0].keys()
 db[0]['nutrients'][0]
nutrients = pd.DataFrame(db[0]['nutrients'])
info_keys = ['description', 'group', 'id', 'manufacturer']
info = pd.DataFrame(db, columns=info_keys)
通过value_counts，你可以查看⻝物类别的分布情况
pd.value_counts(info.group)[:10]
⾸先，将各⻝物的营养成分列表转换为⼀个DataFrame，并添加⼀个表示编号的列，然后将该
DataFrame添加到⼀个列表中。最后通过concat将这些东⻄连接起来就可以了
由于两个DataFrame对象中都有”group”和”description”, 需要对它们进⾏重命名
col_mapping = {'description' : 'food','group' : 'fgroup'}
info = info.rename(columns=col_mapping, copy=False)
nutrients = []
for rec in db:
	fnuts = pd.DataFrame(rec['nutrients'])
	fnuts['id'] = rec['id'] 
	nutrients.append(fnuts)
nutrients = pd.concat(nutrients,ignore_index = True)

col_mapping = {'description' : 'nutrient','group' : 'nutgroup'}
nutrients = nutrients.rename(columns=col_mapping, copy=False)

将info跟nutrients合并起来
ndata = pd.merge(nutrients, info, on='id', how='outer')
 ndata.iloc[30000]

根据⻝物分类和营养类型画出⼀张中位值图
result = ndata.groupby(['nutrient', 'fgroup'])['value'].quantile(0.5)
result['Zinc, Zn'].sort_values().plot(kind='barh')

###########################################2012联邦选举委员会数据库










select c.gender, c.age, a.title, avg(b.ratings)
from movies a
inner join ratings b
on a.movie_id = b.movie_id
inner join users c
on b.user_id = c.user_id
group by c.gender, c.age, a.title;

select max(b.rating),c.gender,a.title
from movies a
inner join ratings b
on a.movie_id = b.movie_id
inner join users c
on b.user_id = c.user_id
where c.gender = 'F'
group by c.gender, a.title;
```