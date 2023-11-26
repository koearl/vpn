## 前期准备

### VPS

- 搬瓦工 （https://www.bwh8.net/cn2gia-vps.php、https://bandwagonhost.com/cn2gia-vps.php）

  -  优惠码（很多人不知道，搬瓦工官网隐藏了一个彩蛋，就是隐藏了优惠码）

     - 选择完产品，点击Order Now进入页面的时候，别急着点击「Add to Cart」添加到购物车，这里面暗藏着一个优惠码
     - 使用浏览器查看源代码， chrome浏览器的话按F12，然后搜索「code」
     - 你会发现有一个 「Try this promo code: xxxx 」，这个xxxx就是优惠码
     - ![image](https://github.com/koearl/vpn/assets/21996270/dced74f8-c587-476e-9fb9-700b9dfc87e4)

  -  连接到搬瓦工

     -  购买完毕后你就拥有一台你自己的服务器了，接着点击Services下的MyServices，可以看到你的服务器：

     -  ![使用搬瓦工搭建VPN](https://camo.githubusercontent.com/a705a235b477b6f98de91acea6ceaa851793cf9f19d01fbb412e1787109ffb1f/68747470733a2f2f776973746265616e2e6769746875622e696f2f696d616765732f76706e362e706e67)

     -  点击「KiwiVM Control Panel」进入管理界面：

     -  ![使用搬瓦工搭建VPN](https://camo.githubusercontent.com/e1858aa95a56c97f47428ed6d65cb7de0039f63065f32eae24f6949a17cfba95/68747470733a2f2f776973746265616e2e6769746875622e696f2f696d616765732f76706e372e706e67)

     -  可以看到你服务器的信息:

     -  ![使用搬瓦工搭建VPN](https://camo.githubusercontent.com/df80b177ebff9683bbe7ed1c4550cb8fa78f02a7d1d5478f5829050ea5c8e793/68747470733a2f2f776973746265616e2e6769746875622e696f2f696d616765732f76706e392e706e67)

     -  账号是root，密码可以在这里获取：

     -  ![搬瓦工搭建ss](https://camo.githubusercontent.com/612e378ee083b0a95c14260af20fadebe74c5b621c1c785069c16ad0e26c746b/68747470733a2f2f776973746265616e2e6769746875622e696f2f696d616765732f706963322e706e67)

     -  一般来说，菜单下方有一个 openvpn 的快速安装方式，但是据说很容易被强，所以现在不推荐使用这种方式搭建自己的VPN。

        

- 域名
  namesilo：https://name.bulianglin.com

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

