---
title: hexo的图片及超链接玩法
categories: 
- 博客相关
tags:
- HEXO
---
### hexo的图片的玩法
#### 给自己的文章添加图片(直接在网站上看，别在本地看)
在Markdown中` ![图片名](url)`，这里面的url若是网上的则直接弄过来就可以；坑为本地上传的图片地址，在hexo文件夹的source文件夹(不是主题中的source)中创建一个文件夹img用来保存图片，然后url写成`/img/图片名.格式`即可。
标题那添加则和上面类似，`img: url`，url与上面一样。
```
---
title: 
img: 
---
```

#### 给自己的字段添加超链接
在Markdown中`[字段](url)`，url建议不用本地，因为本人还没玩透。