# cross-origin

## 同源策略

>请求的url地址,必须与浏览器上的url地址处于同域上,也就是域名,端口,协议相同.

一旦不同，就存在跨域问题。我是通过ajax请求时，遇到了这个问题。后台使用的是spring-boot框架。

## 解决方法

>通过nginx反向代理解决

* 首先下载nginx并编译生成可执行文件，在根目录下的conf文件夹下有nginx.conf文件，向添加`location /{}`里面如下内容，然后添加`location/proxy {}`中的那些内容

* 最后如果没启动nginx，则通过`sbin`目录下的`nginx`启动，否则要么先关闭nginx、再启动，要么`./nginx -s reload -c ***nginx.conf文件绝对路径（相对路径不行）***`


```nginx
    server {
        listen       80; #这个是nginx监听的端口
        server_name  localhost;
        location / {
            add_header 'Access-Control-Allow-Origin' '*'; #允许来自所有的访问地址
            add_header 'Access-Control-Allow-Credentials' 'true';
            add_header 'Access-Control-Allow-Methods' 'GET, PUT, POST, DELETE, OPTIONS, PATCH'; #支持请求方式(不能为*，可能是有的浏览器（例如火狐浏览器）严格按照最新标准来的把)
            add_header 'Access-Control-Allow-Headers' 'cache-control,content-type,hash-referer,x-requested-with'; #同上，不能为*
        }
        location /proxy {
            rewrite ^/proxy/(.*)$ /$1 break; #不一定非得要proxy这个路径
            proxy_pass http://localhost:8080/;#要监听的本地端口
        }
    }
```

>注意，即使这样配置了，我们依然可以通过原端口访问。


