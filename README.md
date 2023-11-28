# 什么是Xray、V2ray、Trojan、Trojan-go、SSR、SS？



## 1. 原理

简单介绍原理。这里忽略网络传输层，默认是 TCP/IP，也忽略域名污染等等其他内容。

![科学上网原理简单介绍.png](https://i.loli.net/2021/01/03/vYfNCrmi2Gk5QdW.png)

#### 1.1 代理

正向代理（forward proxy）：

​	是一个位于客户端和目标服务器之间的服务器(代理服务器)，为了从目标服务器取得内容，客户端向代理服务器发送一个请求并指定目标，然后代理服务器向目标服务器转交请求并将获得的内容返回给客户端。

- 正向代理用途：
  - 突破访问限制：通过代理服务器，可以突破自身IP访问限制，访问国外网站，教育网等。
  - 提高访问速度：通常代理服务器都设置一个较大的硬盘缓冲区，会将部分请求的响应保存到缓冲区中，当其他用户再访问相同的信息时， 则直接由缓冲区中取出信息，传给用户，以提高访问速度。
  - 隐藏客户端真实IP：上网者也可以通过这种方法隐藏自己的IP，免受攻击。

反向代理（reverse proxy）：

​	是指以代理服务器来接受internet上的连接请求，然后将请求转发给内部网络上的服务器，并将从服务器上得到的结果返回给internet上请求连接的客户端，此时代理服务器对外就表现为一个反向代理服务器。

- 反向代理用途：

  - 隐藏服务端真实IP：对客户端隐藏服务器的IP地址。
  - 负载均衡：根据所有真实服务器的负载情况，将客户端请求分发到不同的真实服务器上。
  - 提高访问速度：对于静态内容及短时间内有大量访问请求的动态内容提供缓存服务，提高访问速度。
  - 提供安全保证：反向代理服务器可以作为应用层防火墙，为网站提供对基于Web的攻击行为（例如DoS/DDoS）的防护，更容易排查恶意软件等。还可以为后端服务器统一提供加密和SSL加速（如SSL终端代理），提供HTTP访问认证等。

- 区别：

  - **正向代理其实是客户端的代理**，帮助客户端访问其无法访问的服务器资源。**反向代理则是服务器的代理**，帮助服务器做负载均衡，安全防护等。
  - **正向代理一般是客户端架设的**，比如在自己的机器上安装一个代理软件。而**反向代理一般是服务器架设的**，比如在自己的机器集群中部署一个反向代理服务器。
  - **正向代理中，服务器不知道真正的客户端到底是谁**，以为访问自己的就是真实的客户端。而在**反向代理中，客户端不知道真正的服务器是谁**，以为自己访问的就是真实的服务器。
  - 正向代理和反向代理的作用和目的不同。**正向代理主要是用来解决访问限制问题。而反向代理则是提供负载均衡、安全防护等作用。二者均能提高访问速度。**

  ![HTTP 代理](https://res.weread.qq.com/wrepub/epub_907764_81)

#### 1.2 OSI 七层模型和 SOCKS5

​	**SOCKS**是一种[网络传输协议](https://zh.m.wikipedia.org/wiki/网络传输协议)，主要用于客户端与外网服务器之间通讯的中间传递。SOCKS是”SOCKet Secure”的缩写。

​	当[防火墙](https://zh.m.wikipedia.org/wiki/防火墙_(网络))后的客户端要访问外部的服务器时，就跟SOCKS[代理服务器](https://zh.m.wikipedia.org/wiki/代理服务器)连接。这个代理服务器控制客户端访问外网的资格，允许的话，就将客户端的请求发往外部的服务器。

​	这个协议最初由David Koblas开发，而后由NEC的Ying-Da Lee将其扩展到SOCKS4。最新协议是SOCKS5，与前一版本相比，增加支持[UDP](https://zh.m.wikipedia.org/wiki/用户数据报协议)、身份验证，以及[IPv6](https://zh.m.wikipedia.org/wiki/IPv6)。

​	SOCKS是一种网络传输协议，主要用于客户端与外网服务器之间通讯的中间传递。根据[OSI模型](https://zh.m.wikipedia.org/wiki/OSI模型)，SOCKS是[会话层](https://zh.m.wikipedia.org/wiki/会话层)的协议，位于[表示层](https://zh.m.wikipedia.org/wiki/表示层)与[传输层](https://zh.m.wikipedia.org/wiki/传输层)之间。

![TCP/IP_Protocol.png](https://i.loli.net/2020/10/15/QWDC8PHZRobkSr5.png)

![img](https://upload-images.jianshu.io/upload_images/28790576-c46649f257b0481f.png?imageMogr2/auto-orient/strip|imageView2/2/w/582/format/webp)

## 2.概述

1. ####  SS？

   ​	SS 是 Shadowsocks 的缩写，英文名为shadowsocks，中文名为影梭，一开始是个人开发的一个开源软件，主要用于翻墙，也就是俗称“科学上网”的用途。据说目前作者已被请去“喝茶”，该软件已停止了更新。

   ​	是一种基于Socks5代理方式的加密传输协议，也可以指实现这个协议的各种开发包。Shadowsocks 仍然有不少国外社区成员在维护更新。后来贡献者Librehat也为Shadowsocks补上了一些数据混淆类特性，甚至增加了类似Tor的可插拔传输层功能。

2. #### SSR？

   ​	SSR 是 ShadowsocksR 的缩写。又被称为酸酸乳、小飞机，SSR作者声称SS不够隐匿，容易被防火墙检测到，所以SSR在Shadowsocks的基础上增加了一些数据混淆方式，修复了部分安全问题并提高QoS优先级，更难被防火墙检测到。简单地说，SSR是SS的增强版。

   ​	SS和SSR两者原理相同，都是基于socks5代理。客户端与服务端没有建立专有通道，客户端和实际要访问的服务端之间通过代理服务器进行通信，客户端发送请求和接受服务端返回的数据都要通过代理服务器。

3. #### V2ray？

   ​	V2Ray 是在Shadowsocks 被封杀之后，为了表示抗议而开发的，属于后起之秀，功能更加强大，为抗GFW封锁而生。

   ​	V2Ray 现在已经是 Project V 项目的核心工具，而 Project V 是一个平台，其中也包括支持 Shadowsocks 协议。由于 V2Ray 早于 Project V 项目，而且名声更大，所以我们习惯称 Project V 项目为 V2Ray，所以我们平时所说的 V2Ray 其实就是 Project V 这个平台，也就是一个工具集。其中，只有 VMess协议是V2Ray社区原创的专属加密通讯协议，被广泛应用于梯子软件。

   V2Ray目前支持以下协议（截止到2019年12月）：

   - Blackhole：中文名称“黑洞”，是一个出站数据协议，它会阻碍所有数据的出站，配合路由（Routing）一起使用，可以达到禁止访问某些网站的效果。

   - Dokodemo-door：中文名称“任意门”，是一个入站数据协议，它可以监听一个本地端口，并把所有进入此端口的数据发送至指定服务器的一个端口，从而达到端口映射的效果。

   - Freedom：是一个出站协议，可以用来向任意网络发送（正常的） TCP 或 UDP 数据。

   - HTTP：超文本传输协议，是传统的代理协议

   - MTProto：Telegram 的开发团队开发的专用协议，是一个 Telegram 专用的代理协议。在 V2Ray 中可使用一组入站出站代理来完成 Telegram 数据的代理任务。目前只支持转发到 Telegram 的 IPv4 地址。

   - Shadowsocks：最早被个人开发的科学上网梯子协议，但 V2Ray 目前不支持 ShadowsocksR。

   - Socks：标准 Socks 协议实现，兼容 Socks 4、Socks 4a 和 Socks 5，也是传统的代理协议。

   - VMess：是V2Ray 专用的加密传输协议，它分为入站和出站两部分，通常作为 V2Ray 客户端和服务器之间的桥梁。因为增加了混淆和加密，据说比 Shadowsocks 更安全。现在的机场支持 V2Ray，一般是指支持 VMess 协议。VMess 依赖于系统时间，请确保使用 V2Ray 的系统 UTC 时间误差在 90 秒之内，时区无关。在 Linux 系统中可以安装`ntp`服务来自动同步系统时间。

     

   ​	截止到2019年12月，V2Ray 可选的传输层配置有：TCP、mKCP、WebSocket、HTTP/2、DomainSocket、QUIC。其中，mKCP、QUIC和TCP用于优化网络质量；WebSocket用于伪装；HTTP/2和DomainSocket用于传输以及TLS加密。

   ​	V2Ray不仅可以在传输层配置 TLS 使 HTTP 和 SOCKS 变成 HTTPS 和 SOCKS over TLS 协议，也可以使MTProto、Shadowsocks 和 VMess 通过传输层配置TLS加密伪装成 TLS 流量。所以，VMess 配置 TLS 加密是最常见的做法，但没人会对 Shadowsocks 使用 TLS 加密，因为这完全没意义。

4. #### Xray？

   ​	Xray与V2Ray完全类同，Xray 是 Project X 项目的核心模块。因为Xray和XTLS黑科技的作者rprx曾经是V2fly社区的重要成员，所以Xray直接Fork全部V2Ray的功能，然后进行性能优化，并增加了新功能，使Xray在功能上成为了V2Ray的超集，且完全兼容V2Ray。

   ​	**简而言之，**Xray是V2Ray的项目分支，Xray是V2Ray的超集，就跟Trojan-Go和Trojan-GFW的关系类似，而且Xray性能更好、速度更快，更新迭代也更频繁。由于自V2ray-core 4.33.0 版本起，删除了XTLS黑科技，但仍然支持VLESS，所以是否原生支持XTLS是Xray和V2Ray最大的区别之一。

5. #### Trojan、Trojan-Go？

   ​	Trojan，原来多是指特洛伊木马，是一种计算机病毒程序。但是，我们今天所说的Trojan是一种新的科学上网技术，全称为Trojan-GFW，是目前最成功的科学上网伪装技术之一。你可以认为Trojan是V2Ray的“WS+TLS”模式的精简版，速度比V2Ray更快，伪装比V2Ray更逼真，更难以被GFW识别。

   ​	Trojan工作原理：Trojan通过监听443端口，模仿互联网上最常见的 HTTPS 协议，把合法的Trojan代理数据伪装成正常的 HTTPS 通信，并真正地完整完成的TLS 握手，以诱骗GFW认为它就是 HTTPS，从而不被识别。Trojan处理来自外界的 HTTPS 请求，如果是合法的，那么为该请求提供服务，否则将该流量转交给Caddy、Nginx等 web 服务器，由 Caddy、Nginx 等为其提供网页访问服务。基于整个交互过程，这样能让你的VPS更像一个正常的web服务器，因为Trojan的所有行为均与 Caddy、Nginx等 web 服务器一致，并没有引入额外特征，从而达到难以识别的效果。

   ​	Trojan-Go是Trojan-GFW的分支项目，对Trojan进行性能优化，并增加不少新功能，Trojan-Go性能和功能均有大幅度的提升，而且支持分流和CDN。

## 3. 区别及优缺点

VPN、SS/SSR、V2Ray/Xray 和 Trojan/Trojan-Go 优缺点

### 3.1 原理不同

​	VPN强调对公网传输过程中数据的加解密，SS/SSR/V2Ray/Xray/Trojan都是专注于在客户端和服务器端加解密，公网传输数据过程中特征没有VPN明显。

### 3.2 目的不同

​	VPN是走在公网中自建的虚拟专用通道，使用强大的加解密算法，为数据传输安全性、私密性而生，被广泛应用于企业、高校、科研部门等远程数据传输的领域；SS/SSR/V2Ray/Xray/Trojan/Trojan-Go是为了数据能够安全通过GFW而生，更强调的是对数据的混淆和伪装，加解密只是为了更好的隐藏数据特征而顺利绕过GFW的检测，数据内容加密可以有效绕过关键词的检测。

| 项目名称      | 创建时间   | 支持协议                                               | 对比                                                         |
| :------------ | :--------- | :----------------------------------------------------- | :----------------------------------------------------------- |
| Shadowsocks   | 2015年前   | socks5                                                 |                                                              |
| Shadowsocks-R | 2016年前后 | socks5+混淆协议                                        |                                                              |
| V2Ray         | 2019下半年 | Blackhole Freedom HTTP MTProto Shadowsocks Socks VMess | V2ray比较成熟，支持的配套客户端是最多的，隐秘性良好          |
| Trojan        | 2019年底   | 类似V2ray“WS+TLS”模式的精简版                          | 相比V2ray，速度更快，更轻量级，相比trojan-go 比较老了，因此排名后面 |
| Trojan-Go     | 2020年8月  | 类似V2ray“WS+TLS”模式的精简版                          | 速度方面次于Xray、隐秘更强，客户端比较单一                   |
| Xray          | 2020年11月 | V2ray的升级版（包含V2ray所有协议） VLESS协议           | Xray性能最好、速度更快，隐秘方面也是很不错，更新比较快，支持的客户端也多 |

## 4. 名词释义

​	socks5 代理的原理是把你的网络数据请求先发送到你的代理服务器，然后由代理服务器转发给目标；如果目标有反馈发送到代理服务器，那么代理服务器会将数据包直接传回你的本地网络，整个过程只是数据的二次传输，并没有做额外的处理。比如，现在你在深圳，你的代理服务器在日本，如果你想要访问Google，那么你首先要把数据请求通过本地 socks5 代理客户端发给你在香港的服务器上的 socks5 代理服务端，然后你在香港的服务器将数据请求发送给Google，再把Google反馈的结果传回你的本地电脑的 socks5 客户端，这样就可以绕开GFW的检测而实现科学上网。

​	显而易见，socks5代理的所有数据走的仍然是公网，而且在公网传输过程中，没有对数据进行任何加密和混淆，这跟VPN在公网建立虚拟专用通道传输过程中，对数据高强度加密的方式完全不同。Shadowsocks 和 ShadowsocksR 只在客户端和服务器端对数据做了简单加密和认证，主要功能是流量转发，过墙才是主要目的。虽然现在 ShadowsocksR 已经停止更新很久了，而 Shadowsocks 仍处于社区人员的更新维护之中，不断修复漏洞并增加新功能，所以现在 Shadowsocks 比 ShadowsocksR 更强大。

## 5. 总结

​	Trojan/Trojan-Go为科学上网而生，天生只为了模仿互联网最流行的HTTPS协议而存在，是目前最成功的伪装工具之一，功能与V2Ray的“Vmess+WS+TLS”模式相当，但是更轻量，伪装更逼真，目前GFW几乎无法识别其特征，而且目前Trojan-Go在性能和速度方面的表现均优于V2Ray的VMess和VLESS协议；Xray是V2Ray的超集，新一代科学上网协议，使用XTLS黑科技，速度表现更出色。所以，我认为Trojan/Trojan-Go/Xray会跟V2Ray一样成为将来科学上网的主流工具之一，SSR由于长期得不到维护而逐渐退出历史舞台，Shadowsocks/SS依然是最轻量的科学上网代理协议，没有之一。





### VPS

- Google Cloud（https://cloud.google.com）

- Amazon （https://www.amazon.com）

- Vultr VPS （https://www.vultr.com/）

- Racknerd（https://www.racknerd.com/）

- 搬瓦工 （https://www.bwh8.net/cn2gia-vps.php、https://bandwagonhost.com/cn2gia-vps.php）

  - 优惠码（很多人不知道，搬瓦工官网隐藏了一个彩蛋，就是隐藏了优惠码）

    - 选择完产品，点击Order Now进入页面的时候，别急着点击「Add to Cart」添加到购物车，这里面暗藏着一个优惠码
    - 使用浏览器查看源代码， chrome浏览器的话按F12，然后搜索「code」
    - 你会发现有一个 「Try this promo code: xxxx 」，这个xxxx就是优惠码
    - ![image](https://github.com/koearl/vpn/assets/21996270/dced74f8-c587-476e-9fb9-700b9dfc87e4)

  - 连接到搬瓦工

    - 购买完毕后你就拥有一台你自己的服务器了，接着点击Services下的MyServices，可以看到你的服务器：

    - ![使用搬瓦工搭建VPN](https://camo.githubusercontent.com/a705a235b477b6f98de91acea6ceaa851793cf9f19d01fbb412e1787109ffb1f/68747470733a2f2f776973746265616e2e6769746875622e696f2f696d616765732f76706e362e706e67)

    - 点击「KiwiVM Control Panel」进入管理界面：

    - ![使用搬瓦工搭建VPN](https://camo.githubusercontent.com/e1858aa95a56c97f47428ed6d65cb7de0039f63065f32eae24f6949a17cfba95/68747470733a2f2f776973746265616e2e6769746875622e696f2f696d616765732f76706e372e706e67)

    - 可以看到你服务器的信息:

    - ![使用搬瓦工搭建VPN](https://camo.githubusercontent.com/df80b177ebff9683bbe7ed1c4550cb8fa78f02a7d1d5478f5829050ea5c8e793/68747470733a2f2f776973746265616e2e6769746875622e696f2f696d616765732f76706e392e706e67)

    - 账号是root，密码可以在这里获取：

    - ![搬瓦工搭建ss](https://camo.githubusercontent.com/612e378ee083b0a95c14260af20fadebe74c5b621c1c785069c16ad0e26c746b/68747470733a2f2f776973746265616e2e6769746875622e696f2f696d616765732f706963322e706e67)

    - 一般来说，菜单下方有一个 openvpn 的快速安装方式，但是据说很容易被强，所以现在不推荐使用这种方式搭建自己的VPN。

       

- 域名

  -  namesilo：https://name.bulianglin.com

- CA证书

  -  cartbot：https://certbot.eff.org/



### 节点搭建

```
#更新软件源
apt update
#启用 BBR TCP 拥塞控制算法
echo "net.core.default_qdisc=fq" >> /etc/sysctl.conf
echo "net.ipv4.tcp_congestion_control=bbr" >> /etc/sysctl.conf
sysctl -p

#安装x-ui：
bash <(curl -Ls https://raw.githubusercontent.com/vaxilu/x-ui/master/install.sh)

#安装nginx
apt install nginx
#安装acme：
curl https://get.acme.sh | sh
#添加软链接：
ln -s  /root/.acme.sh/acme.sh /usr/local/bin/acme.sh
#切换CA机构： 
acme.sh --set-default-ca --server letsencrypt
#申请证书： 
acme.sh  --issue -d 你的域名 -k ec-256 --webroot  /var/www/html
#安装证书：
acme.sh --install-cert -d 你的域名 --ecc --key-file /etc/x-ui/server.key  --fullchain-file /etc/x-ui/server.crt --reloadcmd "systemctl force-reload nginx"
```

### 寻找适合的伪装站

> http站点优先，个人网盘符合单节点大流量特征

示例关键字：intext:登录 Cloudreve

### 配置nginx

配置文件路径：/etc/nginx/nginx.conf

```
user www-data;
worker_processes auto;
pid /run/nginx.pid;
include /etc/nginx/modules-enabled/*.conf;

events {
    worker_connections 1024;
}

http {
    sendfile on;
    tcp_nopush on;
    tcp_nodelay on;
    keepalive_timeout 65;
    types_hash_max_size 2048;

    include /etc/nginx/mime.types;
    default_type application/octet-stream;
    gzip on;

    server {
        listen 443 ssl;
        
        server_name nicename.co;  #你的域名
        ssl_certificate       /etc/x-ui/server.crt;  #证书位置
        ssl_certificate_key   /etc/x-ui/server.key; #私钥位置
        
        ssl_session_timeout 1d;
        ssl_session_cache shared:MozSSL:10m;
        ssl_session_tickets off;
        ssl_protocols    TLSv1.2 TLSv1.3;
        ssl_prefer_server_ciphers off;

        location / {
            proxy_pass https://bing.com; #伪装网址
            proxy_redirect off;
            proxy_ssl_server_name on;
            sub_filter_once off;
            sub_filter "bing.com" $server_name;
            proxy_set_header Host "bing.com";
            proxy_set_header Referer $http_referer;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header User-Agent $http_user_agent;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_set_header X-Forwarded-Proto https;
            proxy_set_header Accept-Encoding "";
            proxy_set_header Accept-Language "zh-CN";
        }


        location /ray {   #分流路径
            proxy_redirect off;
            proxy_pass http://127.0.0.1:10000; #Xray端口
            proxy_http_version 1.1;
            proxy_set_header Upgrade $http_upgrade;
            proxy_set_header Connection "upgrade";
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        }
        
        location /xui {   #xui路径
            proxy_redirect off;
            proxy_pass http://127.0.0.1:9999;  #xui监听端口
            proxy_http_version 1.1;
            proxy_set_header Host $host;
        }
    }

    server {
        listen 80;
        location /.well-known/ {
               root /var/www/html;
            }
        location / {
                rewrite ^(.*)$ https://$host$1 permanent;
            }
    }
}
```

> 每次修改nginx配置文件后必须使用 systemctl reload nginx 命令重新加载配置文件

### 多用户合租

> 通过修改nginx的配置文件实现ws path路径分流

```
location /ray {   #分流路径
    proxy_redirect off;
    proxy_pass http://127.0.0.1:10000; #Xray端口
    proxy_http_version 1.1;
    proxy_set_header Upgrade $http_upgrade;
    proxy_set_header Connection "upgrade";
    proxy_set_header Host $host;
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
}
```



