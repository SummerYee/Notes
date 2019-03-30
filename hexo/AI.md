---
title: AI算法
categories: 
- AI
tags:
- 算法
---
AI

 from collections import defaultdict


```
线性代数

向量

向量加法
def vector_add(v, w):
	 """adds two vectors componentwise"""
	 return [v_i + w_i for v_i, w_i in zip(v,w)]

向量减法



多向量累加
from functools import partial, reduce
def vector_sum(vectors):
 	return reduce(vector_add, vectors)

向量乘以标量 = 向量每个元素乘标量

向量点乘
两个向量点乘表示对应元素的分量乘积之和
向量平⽅和
计算向量的⼤⼩（或长度）
两个向量的距离

矩阵
如果A是⼀个矩阵，那么A[i][j]就表示第i⾏第j列的元素
# 2⾏3列
A = [[1, 2, 3], [4, 5, 6]]

矩阵A有len(A)⾏和len(A[0])列，叫形状(shape)
⼀列当作⻓度为n的向量

矩阵⽤途：
每个向量看作矩阵的⼀⾏，⽤矩阵表示⼀个包含多维向量的数据集
表示⼆维关系，之前将关系表示为数据对(i, j)，还可以通过矩阵来表示。
如果节点i和节点j有关系，⽤矩阵A[i][j]为1来表示

算法复杂度 O(1) O(n)

查找⼀个节点的所有连接
friends_of_five = [i for i, is_friend in enumerate(friendships[5]) if 
is_friend]

 Scipy
SciPy是基于NumPy开发的⾼级模块，它提供了许多数学算法和函数的实现，⽤于解决科学计
算中的⼀些标准问题。例如数值积分和微分⽅程求解，扩展的矩阵计算，最优化，概率分布和
统计函数，甚⾄包括信号处理等。
⽣成矩阵和矩阵计算

统计均值、⽅差、中位数、最⼤值和最⼩值

中位数 （median)

众数 (mode)
出现次数最多的⼀个或多个数
def mode(x):
 """returns a list, might be more than one mode"""
	counts = Counter(x)
	max_count = max(counts.values())
	return [x_i for x_i, count in counts.items()
	if count == max_count]
mode(num_friends)

离散度
数据离散程度的⼀种度量，如果统计的值接近零，表示数据聚集在⼀起，离散程度很⼩，如果
值很⼤，表示数据的离散度很⼤。

算法——机器学习

################ 对离散型的数据
1.k-近邻算法
k近邻法(k-nearest neighbor, k-NN)是⼀种基本分类的机器学习。监督学习
它的⼯作原理是：存在⼀个样本数据集合，也称作为训练样本集，并且样本集中每个数据
都存在标签，即我们知道样本集中每⼀个数据与所属分类的对应关系。输⼊没有标签的新数据
后，将新的数据的每个特征与样本集中数据对应的特征进⾏⽐较，然后算法提取样本最相似数
据(最近邻)的分类标签。⼀般来说，我们只选择样本数据集中前k个最相似的数据，这就是k-近
邻算法中k的出处，通常k是不⼤于20的整数。最后，选择k个最相似数据中出现次数最多的分
类，作为新数据的分类。

k-近邻算法步骤如下：四个参数(训练集 分类标签 测试集 k值)
a.计算已知类别数据集中的点与当前点之间的距离；
b.按照距离递增次序排序；
c.选取与当前点距离最⼩的k个点；
d.确定前k个点所在类别的出现频率；
e.返回前k个点所出现频率最⾼的类别作为当前点的预测分类。

我们通常采⽤的⽅法是将数值归⼀化，如将取值范围处理为０到１或者-１到１之间。下⾯的公式可以将任意取值范围的特征值转化为０到１区间内的值
newValue = (oldValue - min) / (max - min)

# sklearn⼿写数字识别

2. 决策树
分类与回归 监督学习 机器学习
选最优特征 
信息增益值
信息熵


决策树(decision tree)是⼀种基本的分类与回归⽅法。
流程图就是⼀个决策树，⻓⽅形代表判断模块(decision block)，椭圆形成代表终⽌模块(terminating block)，表示已经得出结论，可以终⽌运⾏。从判断模块引出的左右箭头称作为分⽀(branch)，它可以达到另⼀个判断模块或者终⽌模块。

决策树构建
构建过程： 特征选择、决策树的⽣成和决策树的修剪

3.朴素贝叶斯
朴素贝叶斯算法是有监督的学习算法，解决的是分类问题。
概率

############对连续型的数据
4.线性回归
回归⽅程（regression equation）
求这些回归系数（regression weights）的过程就是回归。

⽤线性回归找到最佳拟合直线
拟合曲线
过拟合 欠拟合

5.Logistic回归算法

with open('stopwords_cn.txt','r',encoding='utf8') as f:
    # 逐行读取 读出是列表
    txts_list = f.readlines()
    # print(txts)
    for txt in txts_list:
    	# 字符串
        print(txt)
        # 列表去掉左右空格
        print(txt.strip().split())

############ 
绘图和可视化

import matplotlib.pyplot as plt
import numpy as np

%matplotlib —— 直接看 不用plt.show()

Figure和Subplot
matplotlib的图像都位于Figure对象中。你可以⽤plt.figure创建⼀个新的Figure
fig = plt.figure()
不能通过空Figure绘图。必须⽤add_subplot创建⼀个或多个subplot才⾏
ax1 = fig.add_subplot(2, 2, 1)

设置标题、轴标签、刻度以及刻度标签
要改变x轴刻度，最简单的办法是使⽤set_xticks和set_xticklabels。前者告诉matplotlib要将刻度放在数据范围中的哪些位置，默认情况下，这些位置也就是刻度标签。但我们可以通过set_xticklabels将任何其他的值⽤作标签

 plt.xticks([0, 250, 500, 750, 1000], ['one', 'two', 'three', 'four', 'five'], rotation=30, fontsize='small')
 plt.title('My first matplotlib plot')
 plt.xlabel('Stages')
图表保存到⽂件
plt.savefig('/DYF/figpath.png', dpi=400, bbox_inches='tight')

# 直⽅图 
直⽅图（histogram）是⼀种可以对值频率进⾏离散化显示的柱状图。
数据 bins 面元 color 颜色 alpha 透明度
plt.hist(np.random.randn(100), bins=20, color='k', alpha=0.3)
将100个随机数采用20个面元分 统计数据在面元出现的次数
pd.Series(np.random.randn(20)).plot.hist()

# 密度图 pandas、seaborn有 
pd.Series(np.random.randn(20)).plot.density()

comp1 = np.random.normal(0, 1, size=200)
comp2 = np.random.normal(10, 2, size=200)
 values = pd.Series(np.concatenate([comp1, comp2]))
 sns.distplot(values, bins=100, color='k')

# 散点图
plt.scatter(np.arange(30), np.arange(30) + 3 * np.random.randn(30))

使⽤seaborn的regplot⽅法，它可以做⼀个散布图，并加上⼀条线性回归的线
 sns.regplot(np.arange(30), np.arange(30) + 3 * np.random.randn(30), data=trans_data)





# 线型图 数据 线 
# matplotlib的plot函数接受⼀组X和Y坐标，还可以接受⼀个表示颜⾊和线型的字符串缩写。
# “k—-“是⼀个线型选项，⽤于告诉matplotlib绘制⿊⾊虚线图
plt.plot(np.random.randn(50).cumsum(), 'k--')

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Series和DataFrame都有⼀个⽤于⽣成各类图表的plot⽅法。默认情况下，它们所⽣成的是线型图
 s = pd.Series(np.random.randn(10).cumsum(), index=np.arange(0, 100, 10))
 s.plot()

 df = pd.DataFrame(np.random.randn(10, 4).cumsum(0),columns=['A', 'B', 'C', 'D'],index=np.arange(0, 100, 10))
 df.plot()

# 柱状图
plot.bar()和plot.barh()分别绘制⽔平和垂直的柱状图。这时，Series和DataFrame的索引将会被⽤作X（bar）或Y（barh）刻度
 fig, axes = plt.subplots(2, 1)
data = pd.Series(np.random.rand(16), index=list('abcdefghijklmnop'))
data.plot.bar(ax=axes[0], color='k', alpha=0.7)
 data.plot.barh(ax=axes[1], color='k', alpha=0.7)
设置stacked=True即可为DataFrame⽣成堆积柱状图，这样每⾏的值就会被堆积在⼀起
df.plot.barh(stacked=True, alpha=0.5)
~~~~~~~~~~~~~~~~~~~~~~~~
import seaborn as sns
sns.barplot(x='tip_pct', y='day', data=tips, orient='h')
```













