---
layout: post
published: true
tags:
- nginx


---

- Only Open ports to 0.0.0.0 for Web Servers such as Nginx

  Let the web-server routes requests to applications, I am not only aiming for nginx's great performance, but also for a better control over incoming request(you will see what I meant here).

  Here is the bash script I used to update Cloudflare's ips so that only these IP can set the real ip header which I can use to filter out unwanted source ip.

  ```bash
  #!/bin/bash
  
  ip4s=$(curl https://www.cloudflare.com/ips-v4)
  ip6s=$(curl https://www.cloudflare.com/ips-v6)
  if [ ${#ip4s[@]} -eq 0 ]; then
          echo "ip4s empty"
          return 1
  fi
  if [ ${#ip6s[@]} -eq 0 ]; then
          echo "ip6s empty"
          return 1
  fi
  echo "Begin updating nginx cf ips"
  
  echo "#Cloudflare ip addresses" > /etc/nginx/conf.d/00_real_ip_cloudflare_00.conf;
  echo "" >> /etc/nginx/conf.d/00_real_ip_cloudflare_00.conf;
  
  echo "# - IPv4" >> /etc/nginx/conf.d/00_real_ip_cloudflare_00.conf;
  for i in $ip4s; do
          echo "set_real_ip_from $i;" >> /etc/nginx/conf.d/00_real_ip_cloudflare_00.conf;
  done
  
  echo "" >> /etc/nginx/conf.d/00_real_ip_cloudflare_00.conf;
  echo "# - IPv6" >> /etc/nginx/conf.d/00_real_ip_cloudflare_00.conf;
  for i in $ip6s; do
          echo "set_real_ip_from $i;" >> /etc/nginx/conf.d/00_real_ip_cloudflare_00.conf;
  done
  
  echo "" >> /etc/nginx/conf.d/00_real_ip_cloudflare_00.conf;
  echo "real_ip_header CF-Connecting-IP;" >> /etc/nginx/conf.d/00_real_ip_cloudflare_00.conf;
  
  service nginx configtest && service nginx reload
  echo "nginx cf ips updated"
  ```

  In my nginx routing config for a specific application, I have a map variable to identify whether the incoming request is from a legit source ip or not; if it is not, I can let nginx send back a 403 immediately.

  ```nginx
  geo $http_x_forwarded_for $is_legit {
      default        0;
      1.1.1.1 1;
  }
  server {
      listen 127.0.0.1:8443 ssl http2;
      server_name xxx.com;
    
      location / {
          if ($is_legit != 1) {
              return 403;
          }
          proxy_pass  http://127.0.0.1:8000;
          proxy_set_header        Host               $host;
          proxy_set_header        X-Real-IP          $remote_addr;
          proxy_set_header        X-Forwarded-Proto  $scheme;
          proxy_set_header        X-Forwarded-For    $proxy_add_x_forwarded_for;
      }
  }
  ```

  

- Use iptables to drop unwanted packet

  In my case, I only allow packet coming from Cloudflare; here is the bash script I am using to update the iptables every day to make sure the whitelist is up-to-date:

  ```bash
  #!/bin/bash
  ip4s=$(curl https://www.cloudflare.com/ips-v4)
  ip6s=$(curl https://www.cloudflare.com/ips-v6)
  if [ ${#ip4s[@]} -eq 0 ]; then
          echo "ip4s empty"
          return 1
  fi
  if [ ${#ip6s[@]} -eq 0 ]; then
          echo "ip6s empty"
          return 1
  fi
  echo "Begin Updating Iptables"
  
  iptables -D INPUT -m set --match-set cf4 src -p tcp -m multiport --dports http,https -j ACCEPT
  iptables -D INPUT -p tcp --dport http -j DROP
  iptables -D INPUT -p tcp --dport https -j DROP
  ip6tables -D INPUT -m set --match-set cf6 src -p tcp -m multiport --dports http,https -j ACCEPT
  ip6tables -D INPUT -p tcp --dport http -j DROP
  ip6tables -D INPUT -p tcp --dport https -j DROP
  
  ipset destroy cf4
  ipset create cf4 hash:net
  ipset destroy cf6
  ipset create cf6 hash:net family inet6
  for x in $ip4s; do ipset add cf4 $x; done
  for x in $ip6s; do ipset add cf6 $x; done
  ipset add cf4 127.0.0.1
  
  iptables -A INPUT -m set --match-set cf4 src -p tcp -m multiport --dports http,https -j ACCEPT
  iptables -A INPUT -p tcp --dport http -j DROP
  iptables -A INPUT -p tcp --dport https -j DROP
  
  ip6tables -A INPUT -m set --match-set cf6 src -p tcp -m multiport --dports http,https -j ACCEPT
  ip6tables -A INPUT -p tcp --dport http -j DROP
  ip6tables -A INPUT -p tcp --dport https -j DROP
  
  echo "Iptables updated"
  ```

  

