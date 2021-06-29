---
layout: post
published: true
tags:
- nginx


---

I was tring to have some custom nginx logs for different server names, so I read the doc for log_format and access_log, and then tested it with the following code.

```nginx
log_format cool_log '$http_cf_connecting_ip  [$time_local] '
                           '"$request" $status';
server {
    listen 127.0.0.1:80;
    server_name balabala.com;

    root /var/www/html;
    index index.php index.html index.htm;

    access_log /var/log/nginx/balabala_access.log cool_log;
    error_log  /var/log/nginx/balabala_error.log;
}
```

It works, but later I wanted every logs follow the same pattern, and I assumed the error_log follow the same parameters definition, so I appended the cool_log level to the error_log too, like this:

```nginx
...
    access_log /var/log/nginx/balabala_access.log cool_log;
    error_log  /var/log/nginx/balabala_error.log cool_log;
...
```

Now it complains:

```
nginx: [emerg] invalid log level "cool_log" in ...
```

At first I thought there might be mistakes in my log_format definiton.

After some digging, I found out:

![image-20210629144546658](../images/2021-06-29-nginx- [emerg] invalid log level/image-20210629144546658.png)

