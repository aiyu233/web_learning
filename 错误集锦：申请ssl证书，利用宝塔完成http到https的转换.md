#### 1. 进入腾讯云服务器，在ssl证书页面申请一个免费的ssl证书：

![image-20220116145254679](https://s2.loli.net/2022/01/16/Ib1hNcqOETkMtnv.png)

之后按照要求填写填写证书即可，申请过程大概是半小时，完成后下载nginx的ssl证书。

#### 2. 按照宝塔面板ssl证书安装部署

![img](https://s2.loli.net/2022/01/16/bvZWNXwQeSiBRFg.png)

不出意外的话，会报错：

```nginx
#配置文件错误:
nginx: [emerg] no "ssl_certificate" is defined for the "listen ... ssl" directive in /www/server/panel/vhost/nginx/wordpress.local.conf:1 nginx: configuration file /www/server/nginx/conf/nginx.conf test failed
```

这个时候按照它的提示，在宝塔面板的文件里按照路径`/www/server/panel/vhost/nginx/wordpress.local.conf`，将第一行

![image-20220116150940491](https://s2.loli.net/2022/01/16/RpFW4SCsiyXIQa9.png)

修改为：

```nginx
    listen 80;
    listen 443 default_server ssl;
    ssl_certificate /www/server/nginx/conf/abc.com_bundle.pem; 
    ssl_certificate_key /www/server/nginx/conf/abc.com.key;
#其中abc.com是你的域名，具体可以直接复制你下载的nginx文件包里面的.pem和.key文件的文件名
```

**注意**：在这之前你应该把.pem与.key文件上传到`/www/server/nginx/conf/`里，不然会报错。

### 3. 添加443端口

宝塔面板里面的安全中添加放行端口，如果有则忽略。

![image-20220116152056362](https://s2.loli.net/2022/01/16/97TP3wESKpb8X5Q.png)

### 4.在设置的域名管理里添加域名

![image-20220116152253439](https://s2.loli.net/2022/01/16/kQWNf1dDePGH86L.png)

![image-20220116153403104](https://s2.loli.net/2022/01/16/sIBJCFAOowr2P1R.png)

### 5. 最重要的一步：清理浏览器缓存

不出意外的话，清除浏览器缓存后，https与http你都能连接，但是如果想要将http定向到https怎么办呢？

### 6.http重定向到https

![image-20220116152910134](https://s2.loli.net/2022/01/16/H5nhkRSpCrAWOlD.png)

直接在配置文件里面添加，或者按照路径`/www/server/panel/vhost/nginx/wordpress.local.conf`打开添加：

```nginx
    if ($server_port !~ 443){
       rewrite ^/.*$ https://$host$uri;
}
```

即可重定向（按照腾讯云文档似乎只需要强制https即可，但实验下来不行，不过并没有测试是不是没有清除缓存的原因）。

