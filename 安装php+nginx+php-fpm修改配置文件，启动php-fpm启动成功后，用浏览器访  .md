05.04 14:21
安装php+nginx+php-fpm修改配置文件，启动php-fpm启动成功后，用浏览器访问
先安装

stone 管理工具

stone 是基于 Termux 的一款管理工具，操作简单，安装方便，集成了许多常用软件，还可以用 gui 的方式操作，非常适合新手来使用。


▌快速安装
你可以直接在Termux内输入以下命令安装本工具：

pkg i -y wget && bash -c "$(wget -qO- 'https://gitee.com/st1020/Stone-Termux-Tool/raw/master/st.sh')"



Stone Termux管理工具


从Stone Termux管理工具中

安装

安装php

安装nginx

安装 php-fpm

pkg install php-fpm


安装完Nginx先修改配置文件在，启动Nginx

vim $PREFIX/etc/php-fpm.d/www.conf

//vim /etc/php-fpm.d/www.conf
//定位搜索listen找到

listen = /data/data/com.termux/files/usr/var/run/php-fpm.sock
将其改为

listen = 127.0.0.1:9000

————————————————

vim $PREFIX/etc/nginx/nginx.conf

#gzip  on;

    server {                                            listen       8080;
        server_name  localhost;
                                                        #charset koi8-r;                        
        #access_log  logs/host.access.log  main;
                                                        location / {
            root    /data/data/com.termux/files/
home/storage/shared/nginx;

            autoindex on;        //这里加上
             
        index  index.html index.htm index.php;
        }  //这里增加一个index.php


//vim $PREFIX/etc/nginx/nginx.conf

location ~ \.php$    {                              
                                                   //去掉注释 #  
     root           html;                  //去掉注释 #         
                           
    fastcgi_pass   127.0.0.1:9000;                          

    fastcgi_index  index.php;
                                                  //去掉注释 #  
                                                   
 fastcgi_param  SCRIPT_FILENAME /data
/data/com.termux/files/home/storage/shared/nginx$fastcgi_script_name;

              autoindex on;  //这里加上
                                                
            include        fastcgi_params;
         
    }


——————————————

启动php-fpm和nginx
在proot环境下面分别启动php-fpm和nginx,这里的nginx不在proot环境下启动后会出一些问题,感兴趣的可以自己去研究看看.

php-fpm
nginx


启动php-fpm启动成功后，用浏览器访问
就可以访问文件目录了

http://127.0.0.1:8080/

php-fpm  网页打不开，重启，php-fpm

输入命令重启
php-fpm

详细笔记

https://github.com/km3xy2/y4


