# ---Nginx多域名代理以及Dnsmasq服务器部署---

# 1、 ubuntu16.04下安装Nginx（通过ubuntu安装源进行安装）

    $:sudo apt-get update
    $:sudo apt-get install nginx
    
 安装完成之后，通过:
 
    $:nginx -v 可查看nginx软件版本
    
 到此安装成功。说明：如果安装网络环境无法翻墙，则可通过修改ubuntu安装源，再执行update和install操作。修改安装源具体步骤如下：
 
   a、安装源原文件备份：
   
     $:sudo cp /etc/apt/sources.list /etc/apt/sources.list.bak
     
   b、编辑源列表文件：
   
      $:sudo vim /etc/apt/sources.list
      
   c、将该文件内容删除，然后添加如下内容（三者选其一）：
   
       阿里云安装源：
       deb http://mirrors.aliyun.com/ubuntu/ trusty main restricted universe multiverse
       deb http://mirrors.aliyun.com/ubuntu/ trusty-security main restricted universe multiverse
       deb http://mirrors.aliyun.com/ubuntu/ trusty-updates main restricted universe multiverse
       deb http://mirrors.aliyun.com/ubuntu/ trusty-proposed main restricted universe multiverse
       deb http://mirrors.aliyun.com/ubuntu/ trusty-backports main restricted universe multiverse
       deb-src http://mirrors.aliyun.com/ubuntu/ trusty main restricted universe multiverse
       deb-src http://mirrors.aliyun.com/ubuntu/ trusty-security main restricted universe multiverse
       deb-src http://mirrors.aliyun.com/ubuntu/ trusty-updates main restricted universe multiverse
       deb-src http://mirrors.aliyun.com/ubuntu/ trusty-proposed main restricted universe multiverse
       deb-src http://mirrors.aliyun.com/ubuntu/ trusty-backports main restricted universe multiverse
  
       或者：中科大安装源：
       deb http://mirrors.ustc.edu.cn/ubuntu/ xenial main restricted universe multiverse
       deb http://mirrors.ustc.edu.cn/ubuntu/ xenial-security main restricted universe multiverse
       deb http://mirrors.ustc.edu.cn/ubuntu/ xenial-updates main restricted universe multiverse
       deb http://mirrors.ustc.edu.cn/ubuntu/ xenial-proposed main restricted universe multiverse
       deb http://mirrors.ustc.edu.cn/ubuntu/ xenial-backports main restricted universe multiverse
       deb-src http://mirrors.ustc.edu.cn/ubuntu/ xenial main restricted universe multiverse
       deb-src http://mirrors.ustc.edu.cn/ubuntu/ xenial-security main restricted universe multiverse
       deb-src http://mirrors.ustc.edu.cn/ubuntu/ xenial-updates main restricted universe multiverse
       deb-src http://mirrors.ustc.edu.cn/ubuntu/ xenial-proposed main restricted universe multiverse
       deb-src http://mirrors.ustc.edu.cn/ubuntu/ xenial-backports main restricted universe multiverse
  
       或者：网易163安装源：
       deb http://mirrors.163.com/ubuntu/ trusty main restricted universe multiverse
       deb http://mirrors.163.com/ubuntu/ trusty-security main restricted universe multiverse
       deb http://mirrors.163.com/ubuntu/ trusty-updates main restricted universe multiverse
       deb http://mirrors.163.com/ubuntu/ trusty-proposed main restricted universe multiverse
       deb http://mirrors.163.com/ubuntu/ trusty-backports main restricted universe multiverse
       deb-src http://mirrors.163.com/ubuntu/ trusty main restricted universe multiverse
       deb-src http://mirrors.163.com/ubuntu/ trusty-security main restricted universe multiverse
       deb-src http://mirrors.163.com/ubuntu/ trusty-updates main restricted universe multiverse
       deb-src http://mirrors.163.com/ubuntu/ trusty-proposed main restricted universe multiverse
       deb-src http://mirrors.163.com/ubuntu/ trusty-backports main restricted universe multiverse
       
# 2、Nginx配置文件修改

   a、编辑nginx.conf配置文件：
    
     $:vim /etc/nginx/nginx.conf 
     
   文件内容如下：
     
        user www www;
        #user nobody nobody;
        worker_processes  4;

        #error_log  logs/error.log;
        #error_log  logs/error.log  notice;
        #error_log  logs/error.log  info;

        #pid        logs/nginx.pid;


        events {
           worker_connections  1024;
        }


        http {
            include       mime.types;
            default_type  application/octet-stream;

            #log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
            #                  '$status $body_bytes_sent "$http_referer" '
            #                  '"$http_user_agent" "$http_x_forwarded_for"';

            #access_log  logs/access.log  main;

            sendfile        on;
            #tcp_nopush     on;

            #keepalive_timeout  0;
            keepalive_timeout  65;
            client_max_body_size 100M;
            #gzip  on;

            #server {
            #    listen       80;
            #    server_name  localhost;

            #charset koi8-r;

            #access_log  logs/host.access.log  main;

            #    location / {
            #        root   html;
            #        index  index.html index.htm;
            #    }
            #       location ~ \.php {
            #                       fastcgi_pass 127.0.0.1:9000;
            #                       fastcgi_index /index.php;
            #                       include /usr/local/nginx/conf/fastcgi_params;
            #                       fastcgi_split_path_info ^(.+\.php)(/.+)$;
            #                       fastcgi_param PATH_INFO $fastcgi_path_info;
            #                       fastcgi_param PATH_TRANSLATED $document_root$fastcgi_path_info;
            #                       fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
            #       }

            #error_page  404              /404.html;

            # redirect server error pages to the static page /50x.html
            #
            #    error_page   500 502 503 504  /50x.html;
            #    location = /50x.html {
            #        root   html;
            #    }

            # proxy the PHP scripts to Apache listening on 127.0.0.1:80
            #
            #location ~ \.php$ {
            #    proxy_pass   http://127.0.0.1;
            #}

            # pass the PHP scripts to FastCGI server listening on 127.0.0.1:9000
            #
            #location ~ \.php$ {
            #    root           html;
            #    fastcgi_pass   127.0.0.1:9000;
            #    fastcgi_index  index.php;
            #    fastcgi_param  SCRIPT_FILENAME  /scripts$fastcgi_script_name;
            #    include        fastcgi_params;
            #}

            # deny access to .htaccess files, if Apache's document root
            # concurs with nginx's one
            #
            #location ~ /\.ht {
            #    deny  all;
            #}
            #}

            include ./vhosts/*.conf;

            # another virtual host using mix of IP-, name-, and port-based configuration
            #
            #server {
            #    listen       8000;
            #    listen       somename:8080;
            #    server_name  somename  alias  another.alias;

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

        }
        
   b、在nginx文件目录下添加vhost文件目录：
    
         /etc/nginx$:mkdir vhosts
         
   c、在vhosts文件下添加serverA.conf、serverB.conf（根据实际需要，配置多少个域名就配置多少个.conf文件）
    
   示例1：
      
        /etc/nginx/vhosts$:vim serverA.conf
        
   编辑内容如下：
        
          server { 
	          listen 80; 
	          server_name www.A.com;       #绑定域名 
	          index index.htm index.html index.php; #默认文件 
	          root /opt/www/Aserver/dist/;       #网站根目录路径
	          #include location.conf;         #调用其他规则，也可去除
            location / {
                add_header 'Access-Control-Allow-Origin' "*";
                add_header 'Access-Control-Allow-Credentials' "true";
                try_files $uri $uri/ /index.php?$query_string;
              }
	          location /api {
                proxy_set_header Host $proxy_host;
                proxy_pass http://api.D.com;       #配置的接口域名
                proxy_buffer_size  128k;
                proxy_buffers   32 32k;
                proxy_busy_buffers_size 128k;
              }	
	          location ~ \.php {
		            fastcgi_pass 127.0.0.1:9000;
		            fastcgi_index /index.html;        #网站根目录路径下的index.htm index.html index.php，根据实际已有文件去填
		            include /etc/nginx/fastcgi_params;  #根据实际nginx安装路径去填写
		            fastcgi_split_path_info ^(.+\.php)(/.+)$;
		            fastcgi_param PATH_INFO $fastcgi_path_info;
		            fastcgi_param PATH_TRANSLATED $document_root$fastcgi_path_info;
		            fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
	            }
            }
            
   示例2：
      
        /etc/nginx/vhosts$:vim serverB.conf
        
   编辑内容如下：
      
          server { 
	          listen 80; 
	          server_name www.B.com api.D.com;       #绑定域名(绑定2个域名，中间空格间隔开，api开头的可作为接口域名：见示例1) 
	          index index.htm index.html index.php; #默认文件 
	          root /opt/www/Bserver/public/;       #网站根目录
            location / {
                 add_header 'Access-Control-Allow-Origin' "*";
                 add_header 'Access-Control-Allow-Credentials' "true";
                 try_files $uri $uri/ /index.php?$query_string;
                }
	          location ~ \.php {
		            fastcgi_pass 127.0.0.1:9000;
		            fastcgi_index /index.php;         #网站根目录路径下的index.htm index.html index.php，根据实际已有文件去填
		            include /etc/nginx/fastcgi_params;       #根据实际nginx安装路径去填写 
		            fastcgi_split_path_info ^(.+\.php)(/.+)$;
		            fastcgi_param PATH_INFO $fastcgi_path_info;
		            fastcgi_param PATH_TRANSLATED $document_root$fastcgi_path_info;
		            fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
	            }
            }
            
   示例3：
      
        /etc/nginx/vhosts$:vim serverC.conf
        
   编辑内容如下：
      
          server { 
	          listen 80; 
	          server_name www.C.com;       #绑定域名 
	          index index.htm index.html index.php; #默认文件 
	          root /opt/www/Cserver/dist/;       #网站根目录
	          #include location.conf;         #调用其他规则，也可去除
            location / {
                add_header 'Access-Control-Allow-Origin' "*";
                add_header 'Access-Control-Allow-Credentials' "true";
                try_files $uri $uri/ /index.php?$query_string;
              }
	          location ~ \.php {
		            fastcgi_pass 127.0.0.1:9000;
		            fastcgi_index /index.html;    #网站根目录路径下的index.htm index.html index.php，根据实际已有文件去填
		            include /etc/nginx/fastcgi_params;  #根据实际nginx安装路径去填写
		            fastcgi_split_path_info ^(.+\.php)(/.+)$;
		            fastcgi_param PATH_INFO $fastcgi_path_info;
		            fastcgi_param PATH_TRANSLATED $document_root$fastcgi_path_info;
		            fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
	            }
            }
            
   说明：示例1、2、3中，其中3最为简单，配置单任意域名网站；示例1示例2中，示例2的其中一个域名作为示例1的接口域名，方便示例1调用示例2的接口。【在配置时根据实际需要需配置自己所需的域名】
      
   d、配置好nginx代理服务器后，在nginx/sbin目录下使用如下命令：
    
      nginx/sbin$:./nginx -t    #测试是否成功
      nginx/sbin$:./nginx -s reload  #重启nginx服务
      
   e、在/etc/hosts文件中，编辑添加配置的域名：
    
        IP www.A.com
        IP www.B.com
        IP www.C.com
        IP api.D.com
        
   说明：IP指部署网站所在的服务器的IP地址，如网站部署在192.168.5.10上，则IP就设为该IP地址
         
# 3、Dnsmasq服务器配置

   a、安装Dnsmasq：
      
      $:sudo apt-get install dnsmasq
      
   b、编辑dnsmasq的配置文件：
    
      $:sudo vi /etc/dnsmasq.conf
      
      # or if you want it to read another file, as well as /etc/hosts, use
      # this.
      #addn-hosts=/etc/banner_add_hosts
      addn-hosts=/etc/hosts   #将此行取消注释

   b、重启Dnsmasq服务：
    
      $:systemctl restart dnsmasq
      
 # 4、测试局域网是否能正常访问（两种方法）
 
   方法a、将所有局域网内的客户端网络DNS均设为Dnsmasq服务器IP地址
     打开网络和共享中心->本地连接->属性->IPV4->使用下面的DNS服务器地址，将Dnsmasq服务器IP地址填入
     浏览器分别打开
     
              www.A.com
              www.B.com
              www.C.com
              api.D.com
   观察能否正常访问。
     
   方法b、所有局域网客户端电脑配置host文件
     计算机->C盘（系统盘锁在位置）->windows->System32->drivers->etc，编辑添加hosts文件，内容同2中的e，完成后保存。
     
              IP www.A.com
              IP www.B.com
              IP www.C.com
              IP api.D.com
              
   说明：IP指部署网站所在的服务器的IP地址，如网站部署在192.168.5.10上，则IP就设为该IP地址
    浏览器分别打开www.A.com;www.B.com;www.C.com;api.D.com，观察能否正常访问
    
