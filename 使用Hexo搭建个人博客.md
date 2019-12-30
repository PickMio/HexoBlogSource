---
title: 使用Hexo搭建个人博客
date: 2016-08-29 14:13:56
categories: 前端
tags: Blogs
---
hexo，一个基于Node.js的静态博客程序。能够将Markdown文件生成静态页面，输出至其根目录的public文件夹下。可以将生成的public文件的所有内容，上传至GitHub的blog项目下。通过类似pickmio.github.io(pickmio改为你自己的GitHub用户名)的网址就能访问你的Blog了。网上也有很多搭建方案，但是我自己在配置却碰到些问题，记录下来希望能有帮助。
# 下载安装GitHub

点击[GitHub下载页面](https://windows.github.com/)安装过程按照提示一步一步来。GitHub的安装是先下载一个下载器再在线安装。在下载过程中可能会失败，下载进度条并没有完全就中断了。可以按下Windows键，在搜索程序和文件中键入Internet 选项(Internet后有空格)选择Internet 选项,选中 安全选项卡,选择受信任的站点→点击站点按钮。弹出的窗口中的文本框中输入 github-windows.s3.amazonaws.com点击添加。再试着点击[GitHub下载页面](https://windows.github.com/)安装。如果没有GitHub账号，可参考[GitHub的帮助文档](https://help.github.com/articles/set-up-git/)。

#下载安装Node.Js
点击进入[Node.Js](https://nodejs.org/)下载页面。一路下一步即可。

#安装Hexo
配置好GitHub后，双击桌面的Git Shell ,使用npm安装Hexo。  

>npm install -g hexo

 
需要安装一些插件，请使用npm安装:
> npm install hexo-renderer-ejs --save  
> npm install hexo-renderer-stylus --save  
> npm install hexo-renderer-marked --save
<!--more-->  
#创建Blog文件
使用桌面上的```Git Shell```进入到你为Blog设立的的目录并键入命令```hexo init```
> cd D:\GitHub\Blogs  
> hexo init

会看到回显的提示
>INFO  You are almost done! Don't forget to run 'npm install'...

请按照她提示的键入npm install，耐心等待(这一步是必要的不然你在键入hexo ?时命令提示里面可能会没有server这条命令)  
此时你会看见Blogs目录下生成了一些文件。已经成功了一大半了。继续在Git Shell里输入命令:
>hexo g #是generate的简写，将md文件编译成html静态页面
>hexo s #是server的简写启动server

此时，可以在浏览器中输入localhost:4000来预览页面。此时的页面只是Hexo自动生成的页面，如何创建自己的页面以及页面的编写会在下篇文章出现。

#将静态页面部署到GitHub
双击桌面的GitHub，点击左上角的加号按钮，在Create页面的```Name```项输入”xxx.github.io”,”xxx”表示你自己的GitHub名称如我的GitHub名称是PickMio,就将项目名称设置为```pickmio.github.io```。在GitHub网页中将当前项目切换到```xxx.github.io```。在右侧有个settings(HTTPS clone URL的上面)，点击进去,在页面找到```Automatic page generator按钮```。再点击弹出页面右下角的绿色按钮```Continue to layouts```。在新的页面顶部随便选取一个style，点击绿色按钮```Publis page```,发布网页。此时，GitHub使用她自己的框架生成了一个页面。然而我们需要的使用Hexo创建得页面，所以得做出修改。  
将```xxx.github.io```同步到本地的文件夹下(Git使用基础)。然后将该文件夹下的除了```.git文件夹```,```.gitattributes```,```.gitignore```三个文件以外的所有文件及文件夹删除(注意:一定不要将那三个文件删除了，他们是GitHub的版本控制信息文件，删除后无法正常工作)。再将之前在本地设置的hexo页面文件目录D:\GitHub\Blogs的publish下的所有文件copy到"xxx.github.io文件夹下，再同步，完成。

#查看效果
在浏览器输入```pickmio.github.io```(请将pickmio设置为你所设置的内容)，即可看见效果。恭喜，一个新鲜的Blog诞生!  
本文参考了大牛[wsgzao](http://wsgzao.github.io/post/hexo-guide/)的博文，感谢!