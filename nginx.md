# Windows上使用nginx
## 一、准备工作
下载地址：http://nginx.org/en/download.html
![](/source/img/nginx/2020-07-09-02-12-36.png)
> 注意：务必选择Stable version稳定版进行下载。
## 二、安装
把解压后的文件夹，放到合适的位置，然后路径配置进环境变量即可。
比如：C:\Program Files\nginx-1.18.0
## 三、常用命令
```
start nginx                # 启动
nginx -s reload            # 重新载入配置文件
nginx -s reopen            # 重启 Nginx
nginx -s stop              # 停止 Nginx
nginx -s quit              # 逐步停止nginx
```
## 四、使用场景配置
- 配置文件：**.\conf\nginx.conf**
- 参数说明：
    - listen：代理服务器的访问端口
    - server_name： 访问入口地址
    - root：静态文件启动入口文件。
    - index：静态
    - proxy_pass：真实的服务端地址
    - upstream：设置服务组。
    -  
    -

- 静态服务器配置
>说明：客户端访问server_name:listen的时候，就会直接访问root中的项目。这种配置方式和tomcat之类的服务器软件的作用没什么两样。
```
server {
    listen       80;    
    server_name  localhost;   
    location / {
        root   html/ljindex;    
        index  index.html index.htm;  
    }
}
```

- 正向向代理服务器配置
![](/source/img/nginx/2020-07-09-03-08-08.png)
>说明：客户端访问server_name:listen的时候，会自动将地址转发到proxy_pass中。这种配置方式，服务端不能记录到正确的客户端IP，所有的请求都来源于nginx所在IP。当然，也会隐藏服务端的真实IP。客户无法获取到真实的服务端IP。
```
server {
    listen       1234;
    server_name  localhost;
    location / {
        proxy_pass http://192.168.0.116:2333;
    }
}
```
- 反向代理+负载均衡配置
![](/source/img/nginx/2020-07-09-03-08-36.png)
>说明：客户端访问server_name:listen的时候，会自动将地址转发到proxy_pass中。使用upstream设置一个服务组，通过这种配置方式，默认所有server_name:listen过来的请求，会依次转发到allserver所包含的服务中。

weight = 1 表示权重，意思就是优先谁来处理这次请求，这里小编设置两个都是一样的。
max_fails = 2 连接失败次数，如果该地址连接失败两次，则表示该服务器已经挂了，就不会在分配任务给它。
fail_timeout = 3 超时时长，多久没连接上则表示连接失败
```
upstream allserver {
        ip_hash;  # 通过ip生成hash值均匀分配到服务器 ip_hash
        least_conn;  # 使用最少连接数 least_conn
        server 192.168.0.116:2333 weight=1 max_fails=2 fail_timeout=3;
        server 192.144.148.91:2333 weight=1 max_fails=2 fail_timeout=3;
 }
server {
    listen       1234;
    server_name  localhost;
    location / {
        proxy_pass http://allserver;
    }
}
```
- 正向+反向代理混合模式
![](/source/img/nginx/2020-07-09-03-46-44.png)

- 通过反向代理解决跨域问题
>说明：在开发环境中，前端开发和后端开发不在同一个域名下面，当前端调用后端的接口的时候，就会产生跨域的问题。所以可以通过nginx反向代理的方式来绕过跨域的问题。将nginx搭建到后端环境上，前端调用nginx的地址，来实现对后端接口的调用。
```
server {
    listen       1234;
    server_name  0.0.0.0;
    location / {
        proxy_pass http://127.0.0.1:8080;
    }
}
```


## 踩坑记录
1、重载配置文件出现问题
```
nginx: [error] CreateFile() "C:\Program Files\nginx-1.18.0/logs/nginx.pid" failed (2: The system cannot find the file specified)
```
解决方法：任务管理器中关闭nginx进程后，重新启动。
