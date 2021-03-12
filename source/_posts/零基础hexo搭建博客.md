---
title: 零基础hexo搭建博客
date: 2020-09-03 16:38:21
tags: hexo博客
---

# 零基础运用hexo框架搭建个人博客

## 一.前提准备（以下所有演示均已widows系统为例，mac用户可观看文末视频链接配合文档进行搭建）

### 1.Git下载安装与环境配置

●网上教程众多，此处就不累述。给出其中一个参考链接[Git下载、安装与环境配置](https://blog.csdn.net/huangqqdy/article/details/83032408)<!--more-->

### 2.Node.js下载安装与配置

●[Node.js下载链接](https://nodejs.org/en/)下载左边LTS长期支持版。安装完成后打开cmd（同时按住Win+R，输入cmd，打开命令提示符程序），输入node -v和npm -v**(此处注意-v前面有空格，后面所有命令一样，均需注意空格，不输入空格则会造成错误)**查看是否安装成功，成功则会类似下图所示。     ![](https://blog-1302639422.cos.ap-chengdu.myqcloud.com/1.png)

●下载npm的镜像源cnpm：在cmd中输入npm install -g cnpm \-\-registry=https://registry.npm/taobao.org进行下载。   下载完成后，在cmd中输入cnpm和cnpm -v查看是否安装成功。

### 3.Markdown编辑器下载

●编写个人博客文章需要使用Markdown语言，去网上任意搜索下载一个主流Markdown编辑器即可，个人推荐使用Typora[下载链接](https://typora.io/)。

**务必注意图片的地址以及存放位置**

个人使用Typora，故Typora上传图片解决方案查看[hexo + Typora 图片上传问题]()



## 二：个人博客搭建

### 1.hexo下载使用

●在cmd中输入cnpm install -g hexo-cli，下载完成后输入hexo -v查看是否安装成功。    
●新建一个文件夹blog，在该文件夹中，右键点击Git Bush Here(后续称为黑框)(或者在任意地方打开黑框然后输入cd 文件夹的位置(eg:cd E:\myblog)),然后输入hexo init，等待安装完成后可输入ls -l查看是否安装成功。   
●再输入hexo s，会出现网址(记住)，将网址输入浏览器中即可查看已创建的本地博客网页(查看过程中不要按ctrl+c，当查看完后再按ctrl+c即停止hexo运行)(有时可能出现网址无法访问的现象，是由于hexo默认的端口4000被占用了，在黑框中输入hexo s -p 5000即可访问。如若还是无法访问也影响不大，对后续将网页部署到远端后进行访问没有影响)。   

### 2.将个人博客网页部署到远端Github/Gitee(以下演示以Github为例，Gitee操作类似)

●登录Github网页，新建一个仓库(即点击右上角的加号后，再点击New repository)，在Repository name中需填写格式'自己的GitHub名字.github.io'，必须按该格式填写，其余地方随意。    
●在文件夹blog中打开黑框后输入cnpm install \-\-save hexo-deployer-git   
●打开文件夹blog中的_config.yml，找到如下图处并按图进行修改，马赛克处填入自己的Github账号(注意每个冒号后都要加空格)。

![](https://blog-1302639422.cos.ap-chengdu.myqcloud.com/2.png)     
●再在黑框中输入hexo d，可能会出现如下图所示，即在上面输入自己Github账号下面输入密码，之后即成功，再刷新打开网页中的仓库即可看到成功，此时个人博客网址即使：https://自己Github用户名.github.io,至此个人博客即是搭建成功。   

![](https://blog-1302639422.cos.ap-chengdu.myqcloud.com/3.png)

### 3.在个人博客中发表文章

●新建文章

+ 方法一：在黑框中输入hexo n "文章标题"，即生成一篇文章在类似文件夹E:\myblog\source\_posts中，打开其在Markdown编辑其中即可进行编写。(Markdown的语法很简单，自行上网查找学习)、

+ 方法二：新建一个.md文件，在开头固定格式（title为显示的文章题目，date为显示的文章时间，tags为显示的文章的标签）：

  ```
  ---
  title: xxx
  date: xxxx-xx-xx xx:xx:xx
  tags: xx
  ---
  ```

  **ps：title，date和tag的冒号后均需留有一个空格**

●编写完成保存后，在黑框中分别依次输入hexo clean、hexo g、hexo s，运行完成后打开本地博客网页查看博客修改是否无问题，确认后再在黑框输入hexo d，这样即是成功在个人博客中发表一篇文章。



## 三.博客主题的切换以及样式修改

### 1.主题的修改(以下演示以yilia为例)

●在黑框中输入git clone https://github.com/litten/hexo-theme-yilia.git themes/yilia后，查看yilia文件夹是否在landscape文件夹所在的themes文件夹中，若不在将yilia文件夹挪到和landscape文件夹一起。

![](https://blog-1302639422.cos.ap-chengdu.myqcloud.com/4.png)

●打开文件夹blog中的_config.yml，找到如下图处修改为图中所示。再在黑框中依次输入hexo clean、hexo s、hexo g、hexo d，即是主题修改成功。

![](https://blog-1302639422.cos.ap-chengdu.myqcloud.com/5.png)

### 2.样式的修改

●打开yilia文件夹中的_config.yml，根据其中的提示即可进行部分样式的修改。

●如果要对更多的样式进行修改，功能进行添加，可自行上网搜索或者学习html、css和JavaScript。



[mac教程视频链接](https://www.bilibili.com/video/BV1Yb411a7ty?t=1402)