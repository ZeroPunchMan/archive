# VPS搭建SS
这是我使用过，目前可靠的fan-。-qiang方式.

搭建好以后,建议分享给关系亲密的亲友一同使用,一方面VPS带宽完全用不完,另一方面也促进信息传播,__抵制网络封锁__.

## 服务器搭建
我买的VPS是vultr,国内ISP是电信,目前大多数节点被封,需要建多个主机尝试,用其他厂商的VPS也可以,VPS所用OS为Ubuntu 16.04.假设你具备基本的linux使用经验,已经部署好了主机,按下面的步骤操作即可.

1. apt-get install python-pip

2. apt-get install python-setuptools

3. pip install --upgrade pip

4. vim /usr/bin/pip 最后行改为sys.exit(__main__.main())并重启终端,否则会抱错,网上找到的解决办法,我也不懂python,不知道原因.

5. pip install shadowsocks 

6. vim /etc/ss.cfg 输入以下内容

{
    "server":"服务器的ip",
    "server_port":服务器的端口,
    "local_address":"127.0.0.1",
    "local_port":1080,
    "password":"密码",
    "timeout":300,
    "method":"rc4-md5",
    "fast_open":false
}

服务器ip改为买的vps的ip,端口随便指定一个空闲的,再设置密码,method可以修改成其他加密方式.

7.ssserver -c /etc/ss.cfg  &

到这一步,服务器端就算搭建好了,如果熟悉shell的话,可以写成启动脚本.

## 客户端
[ss客户端](https://github.com/shadowsocks)

到上面这个页面，去找到对应平台的客户端，比如windows，就去shadowsocks-windows下载release.运行后如下图.

![](https://raw.githubusercontent.com/knightlyj/archive/master/img/ss-client.png)

红框中的内容,与上面服务器配置相同,然后确定即可.然后到右下角的任务栏,钩选ss启动.可以选择PAC模式,或全局模式.PAC模式下,仅被qiang网站会通过代理访问.

![](https://raw.githubusercontent.com/knightlyj/archive/master/img/ss-started.png)

# 补充
通常一个节点在使用一段时间后会被封,被封时间长短不确定.

比如上周我在使用新加坡的节点,这周发现连不上了,尝试了几个节点之后,有一个巴黎的节点可以连接.

使用起来还是不那么方便,获得一些非常合理的信息,要付出一点成本.

