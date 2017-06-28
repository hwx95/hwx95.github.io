---
layout:     post
title:      Modbus TCP Slave for Android
subtitle:   jamod在Android中的应用
date:       2017-06-27
author:     GG
header-img: img/post-bg-coffee.jpeg
catalog: true
tags:
    - Android
    - Modbus
    - jamod
---

> 比较水的 Personal Notes

## About

最近在工作中有用到Modbus协议，所以写篇博客来总结一下。关于Modbus我就不多说了，相信过来看我文章的都是冲着Modbus来着。
	
Modbus在Android的典型应用就是Android当主机，PLC或者其他节点做从机。这种形式的Android例程很多，可以搜一下Modbus4j这个库，但是我要做的恰恰相反，我是要让Android做从机（slave），查找了几天资料都没与之相关的。想自己用Modbus4j这个库自己搭一个，但是找不到相关的文档说明。后来我在网上找到一个[Java Modbus库（jamod）](http://jamod.sourceforge.net/index.html)，里面介绍的很详细，每种模式的使用方法都有例程。
	
	
## jamod 调试

当我找到这份资料的时候简直开心疯了，熬夜调试代码，先在电脑上运行Demo，测试OK。但是当我将程序下到Android上，与Modbus模拟器主机相连的时候，发现连不上，那时候一脸懵逼，不可能啊，没理由啊。看了一下log，发现可能是502端口不行，后来改了一下端口继续测试，还是不行！
	

## 崩溃

对于这个结果我是不满意的，调试了一天，整个人都快疯了！！！直到这一刻我都没能解决，希望哪位大神阔以帮帮小弟呀！
	