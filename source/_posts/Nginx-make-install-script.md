---
title: Nginx编译安装指北
date: 2019-06-30 10:19:46
tags:
  - nginx
  - make install
categories:
	- Tech
	- nginx
---

Nginx 无疑是我最最常用的Web Server。就我多年使用的经验来说，这玩意的安装方式(Linux)，最最好还是编译安装  
虽然不如软件包管理来的方便，但是万一哪天你想装个插件写个插件啥的你就哭去吧，等着你卸载重装  
另外就是软件包管理的nginx版本，有的可以老出几个世纪（笑）  
编译安装的话在这方面就灵活的多啦～～～

*当然今天突然想起来写这篇还是因为之前腾讯云阿里云的学生认证到期了，得重新买了，当然这边环境也就重新搭，顺便写一下  
（话说这个安装脚本一直都是记在OneNote上的，哪天心情好把记在OneNote里的东西都转到博客上来呢，，hh我还是太懒）*

<!-- more -->

# 依赖安装
编译安装nginx前需要解决两个问题   
1. 编译所需的编译器等软件，比如`gcc`，`make`之类的   
2. 第三方依赖包，安装nginx的第三方依赖一般是这三个
   - pcre库（支持rewrite模块）『一个Perl库，包括 Perl兼容的正则表达式库』
   - zlib库（支持gzip模块）『提供数据压缩用的函式库』
   - openssl库（支持ssl模块）『一个强大的安全套接字层密码库』   
    
    那这些库就完全没有必要编译安装了，一般的linux发行版直接用软件包管理安装即可

**下面就贴出软件包管理安装这些依赖的命令**
{% tabs 安装依赖 %}
  <!-- tab RedHat系(yum) -->
    `yum -y install gcc gcc-c++ automake pcre-devel zlib-devel openssl-devel`
  <!-- endtab -->
  <!-- tab Debian系(apt) -->
    `sudo apt-get install build-essential libpcre3-dev libssl-dev zlib1g-dev`
  <!-- endtab -->
{% endtabs %}  
****************
{% note primary %}
当然这三个第三方库也可以编译安装
 - 先去[pcre](http://www.pcre.org/)、[zlib](http://zlib.net/)、[openssl](https://www.openssl.org/)的官网或者镜像站下载源码包  
 - 解压包之后在源码包根目录 configure, make, sudo make install 即可，编译安装如遇问题自行百度  
  **记得configure的时候prefix安装位置**，比如
  {% tabs configures %}
  <!-- tab pcre -->
    `./configure --prefix=/usr/local/libpcre`
  <!-- endtab -->
  <!-- tab zlib -->
    `./configure --prefix=/usr/local/zlib`
  <!-- endtab -->
  <!-- tab openssl -->
    `./Configure darwin64-x86_64-cc --prefix=/usr/local/openssl --openssldir=/usr/local/openssl`
  <!-- endtab -->
{% endtabs %} 
 - 在下面的脚本后添加如下几段选项配置
    {% codeblock lang:bash %}
    --with-pcre=/xxx/pcre_source \
    --with-zlib=/xxx/zlib_source \
    {% endcodeblock %}
    这里面的`/xxx/source`要替换成你下载解压的pcre和zlib的**源码包目录路径**，注意是源码包
{% endnote %}


# configure脚本
**废话也不多说了，直接上脚本，会用的直接拿去，不用听我在下面解释了～～～**
{% codeblock lang:bash %}
./configure --prefix=/usr/local/nginx \
--sbin-path=/usr/sbin/nginx \
--conf-path=/etc/nginx/nginx.conf \
--error-log-path=/var/log/nginx/error.log \
--pid-path=/var/run/nginx/nginx.pid \
--lock-path=/var/run/nginx.lock \
--with-http_ssl_module \
--user=nginx \
--group=nginx \
--http-log-path=/var/log/nginx/access.log \
--http-client-body-temp-path=/var/cache/nginx/client_temp \
--http-proxy-temp-path=/var/cache/nginx/proxy_temp \
--http-fastcgi-temp-path=/var/cache/nginx/fastcgi_temp \
--http-uwsgi-temp-path=/var/cache/nginx/uwsgi_temp \
--http-scgi-temp-path=/var/cache/nginx/scgi_temp \
--with-http_sub_module \
--with-http_realip_module \
--with-http_dav_module \
--with-http_mp4_module \
--with-http_flv_module \
--with-http_gzip_static_module \
--with-http_gunzip_module \
--with-http_stub_status_module \
--with-http_random_index_module \
--with-http_secure_link_module \
--with-http_auth_request_module \
--with-mail \
--with-mail_ssl_module \
--with-file-aio \
--with-threads \
--with-stream_ssl_module \
--with-http_slice_module \
--with-http_v2_module
{% endcodeblock %}

这是我每次编译安装nginx都会用到的选项配置（虽然里面有很多选项都是默认配置，不过列出来方便修改也直观），我基本上除了外加的nginx插件配置，其他的安装配置都在这里面了

# 添加第三方Nginx插件
Nginx能够添加许多第三方插件，但是大部分都需要编译安装添加  
这里我以[`nginx-http-flv-module`](https://github.com/winshining/nginx-http-flv-module)模块举例
> [nginx-http-flv-module](https://github.com/winshining/nginx-http-flv-module)是一个基于[nginx-rtmp-module](https://github.com/arut/nginx-rtmp-module)的流媒体服务器
> 因为以前用nginx搭过直播服务器，机缘巧合了解的

- 首先下载该模块源码，解压到nginx的源码包根目录(推荐)，将模块源码目录名改成`nginx-http-flv-module`
- 修改上面的脚本，添加下面的代码
     {% codeblock lang:bash %}
    --add-module=nginx-http-flv-module
    {% endcodeblock %}
    *这里如果你没有把解压的模块源码包放在nginx包根目录也没有改名，那么在`--add-module=`后面写它的绝对路径 或者 于nginx包的相对路径*
- 之后按下面的继续编译安装即可

{% note warning %}
注意解决该模块自身的依赖问题，具体详见自己安装的模块的文档描述{% endnote %}

# configure脚本使用
嘛这个使用起来很简单的嘞，先把nginx官网下载的源码包解压了，然后进入到源码包根目录，新建一个sh文件，那我这里一般就简单粗暴的叫`c.sh`，然后把上面的脚本粘进去保存。
记得给`c.sh`执行权限，之后执行这个脚本就可以了
{% codeblock lang:bash %}
chmod +x c.sh
./c.sh
{% endcodeblock %}  

# 编译安装
> 至于编译安装的过程就不赘述了，configure之后就是make加make install三步走就完了，不报错就行了
> make install因为上面脚本配置上涉及写入文件到一些需要root权限的目录，非root用户记得加`sudo`
{% codeblock lang:bash %}
make
sudo make install
{% endcodeblock %} 

安装完之后使用nginx命令启动不报错即可，如果需要停止nginx，使用`nginx -s stop`，修改配置后重新启动使用`nginx -s reload`