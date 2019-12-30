---
title: Blog 模板配置
date: 2016-08-29 14:38:02
categories: 前端
tags: Blogs
---
由于之前的主题landscape(存放在如```D:\GitHub\Blogs\themes```中)没有自带评论功能，暂时换pacman主题，它使用第三方评论系统**多说**。关于自定义评论功能再议。  
打开git shell，切换到你的```D:\GitHub\Blogs```目录下，使用命令安装主题:
>git clone https://github.com/A-limon/pacman.git themes/pacman  

完成后，打开D:\GitHub\Blogs\_config.yml，修改主题为pacman。
>theme: pacman  

再按照前面介绍的生成静态页面方法，生成后，将public文件夹下内容上传到git hub项目文件中。测试看看效果。

>hexo g #是generate的简写，将md文件编译成html静态页面
>hexo s #是server的简写启动server
<!--more-->
#新建文档
执行new命令，生成指定名称的文章至hexo\source\_posts\postName.md 
>hexo new [layout] "postName" #新建文章

其中layout是可选参数，默认值为post。有哪些layout呢，请到 scaffolds 目录下查看，这些文件名称就是layout名称。当然你可以添加自己的layout，方法就是添加一个文件即可，同时你也可以编辑现有的layout，比如post的layout默认是 hexo\scaffolds\post.md  
>title: { { title } }  
>date: { { date } }  
>categories:  
>tags:    

看一下刚才生成的文件 hexo\source\_posts\postName.md ，内容如下：

>title: postName  #文章页面上的显示名称，可以任意修改，不会出现在URL中  
>date: 2013-12-02 #文章生成时间，一般不改，当然也可以任意修改  
>categories:      #文章分类目录，可以为空，注意:后面有个空格  
>tags:            #文章标签，可空，多标签请用格式[tag1,tag2,tag3]，注意:后面有个空格

代码高亮方法:
在\`\`\`后面使用添加代码的语言，如\`\`\`cpp

参考博客:  
[张丹(Conan), 程序员Java,R,PHP,Javascript](]http://blog.fens.me/hexo-blog-github/)  
[推酷](http://www.tuicool.com/articles/AfQnQjy)  
[金石开](http://www.cnblogs.com/zhcncn/p/4097881.html)
[Pacman 使用细则](http://yangjian.me/pacman/hello/introducing-pacman-theme/)


**备注:由于这边Pacman的代码高亮显示貌似没有起到作用，故将主题换到litten，具体操作方法类似。**