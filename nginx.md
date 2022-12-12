# nginx 

nginx 是一个高性能的http和反向代理的web服务器，我们经常在项目的部署中去使用到他配置我们前端到后端的请求然后进行反向代理去分发

## 一、nginx的组成部分

第一部分是全局块

### worker_processes

最重要的配置项就是worker_processes ;

这个配置的值越大说明的我们服务器支持的并发处理请求也越多

第二部分是events块

### worker_connections

第二部分的模块最重要的就是workder_connections

worker_connections 1024;支持的最大连接数

第三部分http全局块

### server块

里面最重要的就是我们的server块 我们的一些列操作都在里面，当然可以包括多个server块针对不同的操作进行配置

## 二、反向代理

前面我们说了使用nginx最频繁的用法就是反向代理

我们只需要如下配置我们的server块

server{

​	listen 80; 首先设置我们的端口号

​	server_name: www.linqiaoyan.top ;  这里可以配置我们的域名、二级域名、或者网址来匹配用户访问进来的路径

​	location / { //最后我们配置我们最重要的location它用来匹配我们用户对照server_name域名后路径的访问结果

​			proxy_pass:127.0.0.1:8080 ；我们的方向代理就是配置我们的proxy_pass属性后面匹配我们真实要去访问的路径，这样也就成就了我们平时的二级域名都能通过80端口访问

​		 proxy_set_header附件内容这个属性是用来配置我们要发给代理服务器的请求头

}

}

## 三、负载均衡

我们去频繁的请求代理的某一个地址，有可能发生的事情就是这个地址挂掉，然后服务就无法访问了，这个时候我们可以设置多个代理地址实现负载均衡，不仅对单个代理地址减少了请求量，而且在一个代理地址挂掉后还能正常运作去请求剩下的代理地址如下配置

//配置负载均衡我们需要在http块内配置一个upstream 块并为他命名，然后在里面配置我们用来均很的ip用server属性间隔开，可以在后面配置weight配置我们的ip权重，也可以使用ip_hash

upstream zbc66{

​		ip_hash;

​		server 127.0.0.1:8080 weight 10;

​		server 127.0.0.1:5050 weight 11;

}

server{

​		listen 80;

​		server_name admin.linqiaoyan.top

​		location / {

​				//然后只需要配置我们的反向代理的连接为我们的http://刚才建立的upstream名称 即可	

​				proxy_pass:http://zbc66;

}

}

## 四、动静分离

有时候我们部署一个网站我们希望将动态文件和静态文件分开放置进行处理，这时候就可以如下对我们的静态文件进行操作

server{

​		location /data/ { 这里是我们存放静态文件的文件夹

​			root /www/ 这里是我们存放文件的根目录

}

}

## 五、高性能开发

高性能开发一般采用keepalived 详情可以查看

[(27条消息) Keepalived之——Keepalived + Nginx 实现高可用 Web 负载均衡_冰 河的博客-CSDN博客_nginx keepalied](https://blog.csdn.net/l1028386804/article/details/72801492)