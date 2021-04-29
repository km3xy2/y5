05.04 07:11
termux教程学习笔记安装php+Nginx+php-fpm
https://www.cnblogs.com/cutesnow/p/11430833.html


https://www.cnblogs.com/cutesnow/p/11430833.html


https://mp.weixin.qq.com/s/77EQ4Ayp-TeuB89M2WcKNw


https://www.sqlsec.com/2018/05/termux.html


https://www.sqlsec.com/2018/05/termux.html


先在手机存储里创建一个文件夹
nginx


路径  是    /data/data/com.termux/files/
home/storage/shared/nginx;


nginx默认是不允许列出整个目录的。如需此功能，

打开nginx.conf文件，在location server 或 

http段中加入

autoindex on;



vim $PREFIX/etc/nginx/nginx.conf


用vim打开  nginx.conf



打开nginx.conf文件，在location server 或 http段中加入

autoindex on;



#user  nobody;

worker_processes  1;

#error_log  logs/error.log;
#error_log  logs/error.log  notice;
#error_log  logs/error.log  info;

#pid        logs/nginx.pid;


events {
    worker_connections  1024;
}


http {
    include       mime.types;                       default_type  application/octet-stream;                                                         #log_format  main  '$remote_addr - $remote_u
ser [$time_local] "$request" '                      #                  '$status $body_bytes_sent
 "$http_referer" '
    #                  '"$http_user_agent" "$htt
p_x_forwarded_for"';
                                                    #access_log  logs/access.log  main;

    sendfile        on;
    #tcp_nopush     on;                         
    #keepalive_timeout  0;
    keepalive_timeout  65;


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


                                                        #error_page  404              /404.html;                                                        # redirect server error pages to the static page /50x.html                                      #                                               error_page   500 502 503 504  /50x.html;        location = /50x.html {                              root   /data/data/com.termux/files/usr/share/nginx/html;                                    }                                       
        # proxy the PHP scripts to Apache listening on 127.0.0.1:80
        #
        #location ~ \.php$ {                            #    proxy_pass   http://127.:0.0.1;
        #}

        # pass the PHP scripts to FastCGI server listening on 127.0.0.1:9000                            #
    


     location ~ \.php$    {                              
     
     root           html;                            

    fastcgi_pass   127.0.0.1:9000;                          

    fastcgi_index  index.php;
  
 fastcgi_param  SCRIPT_FILENAME /data
/data/com.termux/files/home/storage/shared/nginx$fastcgi_script_name;

              autoindex on;  //这里加上
                                                
            include        fastcgi_params;
         }

        # deny access to .htaccess files, if Apa
che's document root
        # concurs with nginx's one                      #                                               #location ~ /\.ht {
        #    deny  all;
        #}
    }


    # another virtual host using mix of IP-, nam
e-, and port-based configuration
    #                                               #server {                                       #    listen       8000;                         #    listen       somename:8080;                #    server_name  somename  alias  another.alias;
                                                    #    location / {
    #        root   html;
    #        index  index.html index.htm;
    #    }

#}


    # HTTPS server
    #
    #server {
    #    listen       443 ssl;
    #    server_name  localhost;

    #    ssl_certificate      cert.pem;
    #    ssl_certificate_key  cert.key;

    #    ssl_session_cache    shared:SSL:1m;
    #    ssl_session_timeout  5m;

    #    ssl_ciphers  HIGH:!aNULL:!MD5;
    #    ssl_prefer_server_ciphers  on;

    #    location / {
    #        root   html;
    #        index  index.html index.htm;
    #    }
    #}
                                                }                                               ~
~
~
~                                               ~
~                                               ~


vim $PREFIX/etc/php-fpm.d/www.conf

//vim /etc/php-fpm.d/www.conf


安装并配置php-fpm
安装php-fpm

pkg install php-fpm
配置php-fpm
进入proot环境,然后编辑配置文件www.conf(先进proot可以更方便操作编写相关配置文件)

termux-chroot


vim $PREFIX/etc/php-fpm.d/www.conf



//vim /etc/php-fpm.d/www.conf
//定位搜索listen找到

listen = /data/data/com.termux/files/usr/var/run/php-fpm.sock
将其改为

listen = 127.0.0.1:9000
配置nginx
在proot环境下,然后编辑配置文件nginx.conf


vim $PREFIX/etc/nginx/nginx.conf

//vim /etc/nginx/nginx.conf
下面给出已经配置好的模板文件,直接编辑替换整个文件即可:


worker_processes  1;
events {
    worker_connections  1024;
}


http {
    include       mime.types;
    default_type  application/octet-stream;
    sendfile        on;
    keepalive_timeout  65;


    server {

        listen       8080;
        server_name  localhost;
        root   /data/data/com.termux/files/usr/share/nginx/html;
        index  index.html index.htm;


        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   /data/data/com.termux/files/usr/share/nginx/html;
        }


        location ~ \.php$ {
            root           html;
            fastcgi_pass   127.0.0.1:9000;
            fastcgi_index  index.php;
            fastcgi_param  SCRIPT_FILENAME  /usr/share/nginx/html$fastcgi_script_name;
            include        fastcgi_params;
        }
    }

}
里面的网站默认路径就是nginx默认的网站根目录:

root   /data/data/com.termux/files/usr/share/nginx/html;
fastcgi_param  SCRIPT_FILENAME  /usr/share/nginx/html$fastcgi_script_name;
要修改网站默认路径的话,只需要修改这两处即可.

建立php测试文件
在/usr/share/nginx/html目录下新建一个phpinfo.php文件,其内容是:<?php phpinfo();?>



went

 

启动php-fpm和nginx
在proot环境下面分别启动php-fpm和nginx,这里的nginx不在proot环境下启动后会出一些问题,感兴趣的可以自己去研究看看.

php-fpm
nginx
浏览器访问测试
浏览器访问http://127.0.0.1:8080/phpinfo.php 查询php文件是否解析了.


Kill process: 杀掉当前终端会话进程
