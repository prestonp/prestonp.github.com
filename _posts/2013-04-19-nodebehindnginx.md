---
layout: post
title: Set up nginx reverse proxying for node apps
category: posts
---
Typically, you would never want to use port 80 to host a node.js application. You can, 
but you will need to run it as root. If someone were to hijack your application, they
could do all sorts of damage as root.. so it's advised to run a server like nginx
to front-end node apps. Let's set up nginx to proxy port 80 to node apps running on 
higher, unreserved ports such as 3000. Note: this is aimed at Ubuntu, but should
work for other debian-based OS's.

#Install zlib#
One of the nginx dependencies is zlib, so we'll need to install it first. 
Go to [zlib.net](zlib.net) and find a link to the latest source code

    wget http://zlib.net/zlib-1.2.7.tar.gz
    tar -xvf zlib-1.2.7.tar.gz
    cd zlib-1.2.7.tar.gz
    ./configure
    make
    sudo make install

#Install nginx#

Go to [http://nginx.org/en/download.html](http://nginx.org/en/download.html)
and find the latest stable version (or dev, if you are bleeding edge).

    wget http://nginx.org/download/nginx-1.3.16.tar.gz
    tar -xvf nginx-1.3.16.tar.gz
    cd nginx-1.3.16.tar.gz
    ./configure
    make
    sudo make install

#Configure nginx for node#

On Ubuntu, the nginx configuration file is located at `/usr/local/nginx/conf/nginx.conf`

Let's make the following changes to enable the server

    sudo vi /usr/local/nginx/conf/nginx.conf
    
Add another `server { }` block within `http { }`. Replace `myapp` with your desired domain.

    server {
        listen       0.0.0.0:80;
        server_name  myapp.com www.myapp.com;
        access_log /var/log/nginx/myapp.log;
        
        
        location / {
            proxy_pass http://127.0.0.1:3000;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_set_header Host $http_host;
            proxy_set_header X-NginX-Proxy true;
        
            proxy_redirect off;
        }
    }

#Start nginx#

    sudo /usr/local/nginx/sbin/nginx
    
#Stopping nginx#

To stop nginx, we'll need to kill the running process. 

    $ ps -ax
    $   PID TTY      STAT   TIME COMMAND
          1 ?        Ss     0:00 init
          2 ?        S      0:00 [kthreadd/112]
       7790 ?        Ss     0:00 nginx: master process /usr/local/nginx/sbin/nginx
       7791 ?        S      0:00 nginx: worker process
       7818 pts/0    R+     0:00 ps -ax
      12840 ?        Ss     0:00 cron

Take note of the master nginx process PID (process identifier) then kill it

    $ sudo kill 7790
    
Simply restart nginx and kick off your node app. I use [forever](https://github.com/nodejitsu/forever) 
for managing node daemons.

    // Install forever
    npm install -g forever
    // Navigate to your project and start
    forever start app.js

Then browse to your domain!
