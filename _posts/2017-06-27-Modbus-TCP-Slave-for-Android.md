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

当我找到这份资料的时候简直开心疯了，熬夜调试代码，先在电脑上运行Demo，测试OK。但是当我将程序下到Android上，与[Modbus模拟器主机](http://download.csdn.net/detail/hwx121212/9886579)相连的时候，发现连不上，那时候一脸懵逼，不可能啊，没理由啊。看了一下log，发现可能是502端口不行，后来改了一下端口继续测试，还是不行！
	

## 崩溃

对于这个结果我是不满意的，调试了一天，整个人都快疯了！！！直到这一刻我都没能解决，希望哪位大神阔以帮帮小弟呀！ 

##后续

过了两个星期，再拿起这个代码调试，发现jamod里面的TCP连接跟我之前写的有点不一样  

          m_ServerSocket = new ServerSocket(m_Port, m_FloodProtection, m_Address);   

他创建socket连接的时候有3个参数，而我创建socket服务端的时候只用了一个端口的参数，后来我把后面两个参数去掉，调试一下，竟然连接上了！后来查了一下他后面的几个参数，第二个参数客户端连接请求的队列长度；第三个参数服务端绑定IP。本人Android菜鸟一枚，只知其然而不知其所以然。   

##代码

连接是连接上了，但是怎么使用这些寄存器，我又花时间研究了一下。  
  
    import android.support.v7.app.AppCompatActivity;
    import android.os.Bundle;
    import android.util.Log;

    import net.wimpi.modbus.ModbusCoupler;
    import net.wimpi.modbus.net.ModbusTCPListener;
    import net.wimpi.modbus.procimg.SimpleDigitalIn;
    import net.wimpi.modbus.procimg.SimpleDigitalOut;
    import net.wimpi.modbus.procimg.SimpleInputRegister;
    import net.wimpi.modbus.procimg.SimpleProcessImage;
    import net.wimpi.modbus.procimg.SimpleRegister;

    import java.net.Inet6Address;
    import java.net.InetAddress;
    import java.net.NetworkInterface;
    import java.net.SocketException;
    import java.util.Enumeration;

    public class MainActivity extends AppCompatActivity {
    private static String TAG = "MainActivity";
    ModbusTCPListener listener = null;
    SimpleProcessImage spi = null;

    int port = 3000;//Android 1024 以下端口属于系统端口，需要root权限
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        //prepare a process image
        spi = new SimpleProcessImage();
        //线圈寄存器
        spi.addDigitalOut(new SimpleDigitalOut(true));
        spi.addDigitalOut(new SimpleDigitalOut(true));
        spi.addDigitalOut(new SimpleDigitalOut(true));
        spi.addDigitalOut(new SimpleDigitalOut(true));
        //状态寄存器
        spi.addDigitalIn(new SimpleDigitalIn(false));
        spi.addDigitalIn(new SimpleDigitalIn(true));
        spi.addDigitalIn(new SimpleDigitalIn(false));
        spi.addDigitalIn(new SimpleDigitalIn(true));
        spi.addDigitalIn(new SimpleDigitalIn(true));
        spi.addDigitalIn(new SimpleDigitalIn(true));
        spi.addDigitalIn(new SimpleDigitalIn(true));
        spi.addDigitalIn(new SimpleDigitalIn(true));
        //保持寄存器
        spi.addRegister(new SimpleRegister(251));
        spi.addRegister(new SimpleRegister(13));
        spi.addRegister(new SimpleRegister(240));
        //输入寄存器
        spi.addInputRegister(new SimpleInputRegister(45));


        //create the coupler holding the image
        ModbusCoupler.getReference().setProcessImage(spi);
        ModbusCoupler.getReference().setMaster(false);
        ModbusCoupler.getReference().setUnitID(15);

        new Thread(networkTask).start();
        Log.e(TAG, "本机的IP = " + getHostIP());

    }
    /**
     * 网络操作相关的子线程
     */
    Runnable networkTask = new Runnable() {

        @Override
        public void run() {
            // TODO
            // 在这里进行 http request.网络请求相关操作
            listener = new ModbusTCPListener(3);
            listener.setPort(port);
            listener.start();
        }
    };
    /**
     * 获取ip地址
     * @return
     */
    public static String getHostIP() {

        String hostIp = null;
        try {
            Enumeration nis = NetworkInterface.getNetworkInterfaces();
            InetAddress ia = null;
            while (nis.hasMoreElements()) {
                NetworkInterface ni = (NetworkInterface) nis.nextElement();
                Enumeration<InetAddress> ias = ni.getInetAddresses();
                while (ias.hasMoreElements()) {
                    ia = ias.nextElement();
                    if (ia instanceof Inet6Address) {
                        continue;// skip ipv6
                    }
                    String ip = ia.getHostAddress();
                    if (!"127.0.0.1".equals(ip)) {
                        hostIp = ia.getHostAddress();
                        break;
                    }
                }
            }
        } catch (SocketException e) {
            Log.i("yao", "SocketException");
            e.printStackTrace();
        }
        return hostIp;
    }
    }    
jamod库去上面那个连接找。

##总结

[http://jamod.sourceforge.net/index.html](http://jamod.sourceforge.net/index.html)这个是jamod的库，下来后，修改net下的ModbusTCPListener里面的 
      
    m_ServerSocket = new ServerSocket(m_Port, m_FloodProtection, m_Address);   
 
把后面两个参数去掉；修改modbus的端口号，在Android上是不能使用502端口，建议使用1024以后的端口，主程序的代码如上。  

现在我将工程上传GitHub了，[https://github.com/hwx95/ModbusTCP](https://github.com/hwx95/ModbusTCP)  
添加依赖即可使用。





