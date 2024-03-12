# 使用clash-premium的TUN模式在linux上开启透明代理


## 1、docker-compose.yml配置
  
~~~yaml
version: '3'

services:

  # Enable ip_forward - /etc/sysctl.conf  net.ipv4.ip_forward=1 sudo sysctl -p
  # For Ubuntu Server, you also need to disable systemd-resolved
  # 1. sudo systemctl stop systemd-resolved
  # 2. sudo systemctl disable systemd-resolved
  # Clash
  clash:
    image: dreamacro/clash-premium:latest
    container_name: clash
    restart: always
    privileged: true
    devices:
      - /dev/net/tun
    cap_add:
      - NET_ADMIN
    network_mode: "host"
    volumes:
      - ./configs/config.yaml:/root/.config/clash/config.yaml

  clash-dashboard:
    image: haishanh/yacd
    container_name: clash-dashboard
    ports:
      - "7880:80"
    restart: always
~~~

  
## 2、使用iptables将dns请求转发到clash dns端口

~~~bash
#!/bin/bash

# 清除现有的 iptables 规则
iptables -F
iptables -X
iptables -t nat -F
iptables -t nat -X


# 设置默认策略
iptables -P INPUT ACCEPT
iptables -P FORWARD ACCEPT
iptables -P OUTPUT ACCEPT


#在nat表中新建一个clash规则链
iptables -t nat -N CLASH
#在nat表中新建一个clash_dns规则链
iptables -t nat -N CLASH_DNS

  

#排除环形地址与保留地址，匹配之后直接RETURN
iptables -t nat -A CLASH -d 0.0.0.0/8 -j RETURN
iptables -t nat -A CLASH -d 10.0.0.0/8 -j RETURN
iptables -t nat -A CLASH -d 127.0.0.0/8 -j RETURN
iptables -t nat -A CLASH -d 169.254.0.0/16 -j RETURN
iptables -t nat -A CLASH -d 172.16.0.0/12 -j RETURN
iptables -t nat -A CLASH -d 192.168.0.0/16 -j RETURN
iptables -t nat -A CLASH -d 224.0.0.0/4 -j RETURN
iptables -t nat -A CLASH -d 240.0.0.0/4 -j RETURN
  

iptables -t nat -A PREROUTING -p tcp -j CLASH
iptables -t nat -A PREROUTING -p tcp --dport 53 -j CLASH_DNS
iptables -t nat -A PREROUTING -p udp --dport 53 -j CLASH_DNS

# 将收到的DNS请求转至Clash监听的5353端口。监听端口在config中设置。
iptables -t nat -A CLASH_DNS -p udp --dport 53 -j DNAT --to-destination 192.168.5.200:5335
iptables -t nat -A CLASH_DNS -p tcp --dport 53 -j DNAT --to-destination 192.168.5.200:5335

# 转发全部流量
#iptables -t nat -A CLASH -p tcp -j REDIRECT --to-ports 7892
~~~
  

虽然摸索了很多，据说clash开启TUN无须使用iptables进行转发，不知是不是因为docker问题，导致无法劫持53的请求到clash，所以还是使用了iptables转发，后面有时间再试试是什么问题吧。