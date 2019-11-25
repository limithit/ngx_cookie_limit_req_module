# ngx_cookie_limit_req_module
 
## Introduction

The *ngx_cookie_limit_req_module* module not only limits the access rate of cookies but also limits the number of malicious ip forged cookies.

Table of Contents
=================
* [cookie_limit_req_zone](#cookie_limit_req_zone)
* [cookie_limit_req_zone](#cookie_limit_req)
* [cookie_limit_req_log_level](#cookie_limit_req_log_level)
* [cookie_limit_req_status](#cookie_limit_req_status)
* [Installation](#Installation)
* [About](#About)
* [Donate](#Donate)

## cookie_limit_req_zone
Sets parameters for a shared memory zone that will keep states for various keys. In particular, the state stores the current number of excessive requests. The key can contain text, variables, and their combination. Requests with an empty key value are not accounted. 
```
 Syntax:  cookie_limit_req_zone key zone=name:size rate=rate [sync]  redis=127.0.0.1 block_second=time cookie_max=number;
 Default: —
 Context: http
 ```
 
## cookie_limit_req
Sets the shared memory zone and the maximum burst size of requests. If the requests rate exceeds the rate configured for a zone, their processing is delayed such that requests are processed at a defined rate. Excessive requests are delayed until their number exceeds the maximum burst size in which case the request is terminated with an error. By default, the maximum burst size is equal to zero.
```
 Syntax:  cookie_limit_req zone=name [burst=number] [nodelay | delay=number];
 Default: —
 Context: http, server, location, if
```

## cookie_limit_req_log_level
Sets the desired logging level for cases when the server refuses to process requests due to rate exceeding, or delays request processing. Logging level for delays is one point less than for refusals; for example, if “cookie_limit_req_log_level notice” is specified, delays are logged with the info level.
```
 Syntax:  cookie_limit_req_log_level info | notice | warn | error;
 Default: cookie_limit_req_log_level error;
 Context: http, server, location
```

## cookie_limit_req_status 
Sets the status code to return in response to rejected requests.
```
 Syntax:  cookie_limit_req_status code;
 Default: cookie_limit_req_status 503;
 Context: http, server, location, if
```

     

## Configuration example：
```nginx

    worker_processes  2;
    events {
        worker_connections  1024;
    }
    http {
        include       mime.types;
        default_type  application/octet-stream;
        sendfile        on;
        keepalive_timeout  65;
        
cookie_limit_req_zone $http_cookie zone=two:10m rate=30r/s redis=127.0.0.1 block_second=300 cookie_max=5;
cookie_limit_req zone=two burst=30 nodelay;
cookie_limit_req_status 403;

        
        
        server {
            listen       80;
            server_name  localhost;
            location / {
                root   html;
                index  index.html index.htm;
            }
            error_page   403 500 502 503 504  /50x.html;
            location = /50x.html {
                root   html;
            }
        }
    }
   
```

## Installation

###  Option #1: Compile Nginx with module bundled
    cd redis-4.0**version**/deps/hiredis
    make 
    make install 
    echo /usr/local/lib >> /etc/ld.so.conf
    ldconfig
    
    cd nginx-**version**
    ./configure --add-module=/path/to/this/ngx_cookie_limit_req_module 
    make
    make install


###  Option #2: Compile cookie module for Nginx

Starting from NGINX 1.9.11, you can also compile this module as a cookie module, by using the ```--add-cookie-module=PATH``` option instead of ```--add-module=PATH``` on the ```./configure``` command line above. And then you can explicitly load the module in your ```nginx.conf``` via the [load_module](http://nginx.org/en/docs/ngx_core_module.html#load_module) directive, for example,

```nginx
    load_module /path/to/modules/ngx_cookie_limit_req_module.so;
```

## Donate
The developers work tirelessly to improve and develop ngx_cookie_limit_req_module. Many hours have been put in to provide the software as it is today, but this is an extremely time-consuming process with no financial reward. If you enjoy using the software, please consider donating to the devs, so they can spend more time implementing improvements.

 ### Alipay:
![Alipay](https://github.com/limithit/shellcode/blob/master/alipay.png)

Author
Gandalf zhibu1991@gmail.com
