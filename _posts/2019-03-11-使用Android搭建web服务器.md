---
layout:     post
title:      使用Android搭建web服务器
subtitle:   AndServer，一个Android端的web服务器
date:       2019-03-11
author:     GG
header-img: img/post-bg-building.jpg
catalog: true
tags:
    - Android
    - AndServer
    - web服务器
    
---


>最近公司需要开发一款Android网关，有一个功能是这样的：
>网关在出厂后需要根据实际情况需要配置一些参数，但是又不能有屏幕，有屏幕的话就可以跟手机一样操作了。想过两种方案，第一种，将网关设置成无线AP模式，然后通过socket传参数；第二钟就是将网关设置成web服务器。最后我否定了第一种方式，采用第二种，下面就是我的整个开发学习过程。


首先，感谢[严振杰](https://blog.csdn.net/yanzhenjie1003)大神的一篇技术博文[AndServer，一个Android端的web服务器](https://blog.csdn.net/yanzhenjie1003/article/details/64090436)，关于AndServer的性能等知识请移步大神的博客。

第一步，在Module中添加依赖
    
    annotationProcessor 'com.yanzhenjie.andserver:processor:2.0.4'
    implementation 'com.yanzhenjie.andserver:api:2.0.4'
    implementation 'com.yanzhenjie:loading:1.0.0'
    implementation 'com.alibaba:fastjson:1.1.68.android'
    implementation 'org.apache.commons:commons-lang3:3.8.1'
    implementation 'org.apache.commons:commons-collections4:4.2'

在Project中添加依赖

      dependencies {
        classpath 'com.github.dcendents:android-maven-gradle-plugin:2.1'
        classpath 'com.jfrog.bintray.gradle:gradle-bintray-plugin:1.7.3'
    }
 
将大神的sample移植进来根据错误修改一下，成功启动web服务。

***
>如果不需适配Android4.2机型的童鞋们不用往下看了。

但是当我把demo程序烧写进Android网关的时候频频报错。很是纳闷，后来终于找到了错误，是大神使用的com.yanzhenjie.andserver:api:2.0.4中有一个类不支持Android4.2（由于这文章是我过了个把月写的，具体哪个找不到了），然后我替换了一下就行了。我替换的库在我上传的demo中有。

替换我的库的步骤
将我的api库拷贝到工程目录下，然后修改工程目录的settings.gradle

     include ':app',':api'
    

然后在app中将下面库注释

     implementation 'com.yanzhenjie.andserver:api:2.0.4'
  添加下面代码
  
     implementation project(':api')
  
完美运行！

最后附上代码
GitHub：[https://github.com/hwx95/AndServer](https://github.com/hwx95/AndServer)
