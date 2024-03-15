# 给docker clash安装官方dashboard

## 1、下载clash-dashboard.tar.gz导入宿主机里。

~~~bash
cd /docker/clash # docker-compose.yml同级目录
wget -qO- https://raw.githubusercontent.com/ZIZNAH/Rules/master/files/clash/clash-dashboard.tar.gz | tar xzvf - -C ./ui/
~~~


## 2、docker-compose.yml配置

~~~yaml
volumes:
      - ./configs/config.yaml:/root/.config/clash/config.yaml
      - ./ui:/root/ui
~~~

config.yaml中开启如下配置

~~~yaml
external-controller: 0.0.0.0:9090
external-ui: /root/ui
~~~

访问http://ip:9090/ui即可