---
title: ~ frp内网穿透配置 ~
date: 2018-12-06 19:07:02
tags:
    - frp
    - note
categories: 
    - Tech
    - 端口映射
    - frp
---

# 内网穿透
即NAT穿透，本质上是通过映射端口，使得内网的网络服务可以被外网所连接
嘛，，实际意义就是，大家家里的网络都是运营商分配的ip，并不是外网ip，无法直接用家里的电脑/服务器提供网络服务，譬如网站等
通过内网穿透即可将内网中你所提供的网络服务的端口映射到外网某ip的某端口，即可实现通过被映射的外网端口访问到内网服务
一般有很多提供这种服务的服务商譬如花生壳等，也有很多人知道一些譬如Ngrok的服务，而这里我所要描述的是一款名为**frp**的应用

# frp
{% centerquote %}frp 是一个可用于内网穿透的高性能的反向代理应用，支持 tcp, udp, http, https 协议。{% endcenterquote %}
*来自文档描述*
[frp项目github地址](https://github.com/fatedier/frp)

<!-- more -->

# frps (server) 部署配置

# frpc (client) 配置