---
layout:     post
title:      Ubuntu华硕WiFi问题
subtitle:   华硕安装Ubuntu出现WiFi物理开关禁止问题的解决
date:       2017-07-07
author:     GG
header-img: img/contact-bg.jpg
catalog: true
tags:
    - 华硕
    - Ubuntu
    - WiFi
---



> 常见问题解决


# 前言

华硕安装ubuntu会出现wifi物理开关禁止，按华硕的物理wifi开关fn+f2并没有启动wifi，我们打开终端输入:rfkill list all会看到phy0列下的hard blocked是yes，也就是说被禁止了，然后我们用rfkill unblock all之后发现phy0的hard blocked仍然是yes。在试物理开关的组合键时不经意按了fn+f1，休眠后重启发现wifi能用了。然而这并不是一个一劳永逸的方法。  
在ubuntu的论坛里面发现一篇文章解决了这个问题，链接如下:[http://ubuntuforums.org/showthread.php?t=2181558](http://ubuntuforums.org/showthread.php?t=2181558)。下面简单翻译一下问题及其解决方案。  

# 验证步骤

## 1、检查驱动是否安装成功

    lspci -nnk | grep -A2 0280  

例如输出显示“Kernel driver in use:ath9k”,记住后面的ath9k，接下来要用到。  

## 2、检查asus_ nb_wmi驱动是否正常使用

    lsmod | grep -e ath9k -e asus  

其中ath9k是上面步骤1中的输出。在这一步如果正常情况下是能够看到wifi网卡的驱动以及一个“asus_ nb_wmi”的字样输出。  

## 3、检查一下wifi的“Hard blocked”状态

    rfkill list all

如果phy0上面显示“Hard blocked:yes”，将系统挂起，然后重新唤醒系统，wifi是否能够正常使用？



**如果上述四个步骤确认下来，那么你的系统就存在了这个bug了，可以通过下面的操作来解决这个问题。**  
 
# 解决

打开终端输入下列代码:  

    echo "options asus_nb_wmi wapf=4" | sudo tee /etc/modprobe.d/asus_nb_wmi.conf   

子系统会在开启的时候自动加载华硕wifi驱动的内核模块，重启系统就可以解决这个问题。    
