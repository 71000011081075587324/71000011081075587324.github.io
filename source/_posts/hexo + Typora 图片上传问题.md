---
title: hexo + Typora 图片上传问题
date: 2020-09-15 18:33:55
tags: hexo博客
---

## hexo + Typora 图片上传问题

本文解决hexo + Typora图片上传不显示的问题

<!--more-->



本文部分参考如下两篇博客博客

[解决方案](https://www.jianshu.com/p/8d28027fec76?utm_campaign=maleskine&utm_content=note&utm_medium=seo_notes&utm_source=recommendation)

[详细解释](https://blog.csdn.net/m0_43401436/article/details/107191688)



### 解决步骤：

+ 按如下步骤修改设定Typora中图片放置的位置

  这样当自己新建一个文章后，如果添加图片，会自动生成一个与文章同名的文件夹，并将图片存入该文件夹中，此后该文章中所有添加的图片均存入该文件夹中。

  <img src="image-20210227104710480.png" alt="image-20210227104710480" style="zoom: 80%;" />

  <img src="image-20210227104858400.png" alt="image-20210227104858400"  />

+ 修改博客根目录下的_config.yml文件中的post_asset_folder字段设置为true

  + 当设置 post_asset_folder 参数后，在hexo n命令建立文件时， 会自动建立一个与文章同名的文件夹，把与该文章相关的所有图片资源都放到此文件夹内，这样就可以方便的使用图片资源

  + 同时，只有当post_asset_folder设置为true后，后续安装的插件才会起作用

  <img src="image-20210227105416200.png" alt="image-20210227105416200" style="zoom: 63%;" /><img src="image-20210227105622260.png" alt="image-20210227105622260" style="zoom: 72%;" />

+ 安装插件

  到博客的根目录下执行 npm install [https://github.com/CodeFalling/hexo-asset-image](https://link.jianshu.com/?t=https%3A%2F%2Fgithub.com%2FCodeFalling%2Fhexo-asset-image) --save 命令来进行插件的安装

+ 当文章全部写完后，**使用Typora的替换功能（替换功能包含删除功能，当替换的内容什么都不输入时为全部删除）**将所有图片地址前面多余的部分删除即可

<img src="https://img-blog.csdnimg.cn/20200707235339515.png" alt="在这里插入图片描述" style="zoom:67%;" />

