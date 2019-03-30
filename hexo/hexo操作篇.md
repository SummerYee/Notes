---
title: hexo操作篇
categories: 
- 博客相关
tags:
- HEXO
---
## 一.添加分类

1.创建分类
`categories`页是用来展示所有分类的页面，如果在你的博客`source`目录下还没有`categories/index.md`文件，那么你就需要新建一个，命令如下：
```bash
hexo new page "categories"
```
创建完成后,打开创建分类中的index.md。

在其中添加`type: "categories"`,`layout: "categories"`

2.给文章添加分类属性

打开需要添加分类的文章，为其添加分类属性。categories: -web 表示添加这篇文章到“web”这个分类。注意：hexo一篇文章只能属于一个分类，也就是说如果在“- web”下方添加“-xxx”，hexo不会产生两个分类，而是把分类嵌套（即该文章属于 “- web”下的 “-xxx ”分类）。

## 二.添加分类及标签

1.创建标签

hexo new page "tags"

创建完成后,打开创建分类中的index.md。

在其中添加`type: "tags"`,`layout: "tags"`

2.给文章添加标签属性

打开需要添加分类的文章，为其添加分类属性。tags: -web 表示添加这篇文章到“web”这个标签。注意：hexo一篇文章只能属于一个分类，也就是说如果在“- web”下方添加“-xxx”，hexo不会产生两个分类，而是把分类嵌套（即该文章属于 “- web”下的 “-xxx ”分类）。

## 三.给自定义添加
类似上面操作