---
layout: post
title: Set up nginx reverse proxying for node apps
category: posts
---
Setup nginx to reverse proxy node.js apps
=============

First download zlib for nginx
--------------------
Go to [zlib.net](zlib.net) and find a link to the latest source code

    wget http://zlib.net/zlib-1.2.7.tar.gz
    tar -xvf zlib-1.2.7.tar.gz
    cd zlib-1.2.7.tar.gz
    ./configure
    sudo make install

Install nginx
-------------
Go to [http://nginx.org/en/download.html](http://nginx.org/en/download.html)
and find the latest stable version (or dev, if you are bleeding edge).

    wget http://nginx.org/download/nginx-1.3.16.tar.gz
    tar -xvf nginx-1.3.16.tar.gz
    cd nginx-1.3.16.tar.gz
    ./configure
    make
    sudo make install

Configure nginx for node
------------------------
Typically, you don't want to run node on port 80. So we have nginx fronting
port 80 to some other user-defined node port like 3000.

Follow these [steps](http://stackoverflow.com/questions/5009324/node-js-nginx-and-now)

I use [forever](https://github.com/nodejitsu/forever) for managing node daemons.

    // Install forever
    npm install -g forever
    // Navigate to your project and kick off your app
    forever start app.js

Then browse to your domain!

