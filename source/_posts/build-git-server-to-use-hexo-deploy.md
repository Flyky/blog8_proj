---
title: 自建git服务实现hexo博客部署
date: 2019-11-05 13:45:01
tags:
    - git
    - hexo
categories:
	- Tech
	- hexo
---

一直以来我的hexo博客都是用自带的server跑，然后nginx反代，说白了`hexo s `只是用于生产环境下使用的，我却一只用于部署。没用到hexo生成静态页面和部署，有点遗憾也有不少的弊端。
但是hexo deploy大多数人是部署到github上，我并不想用github的域名，也不想部署到github上再把自己的域名解析CNAME到上面，因为国内github的访问时不时就有问题，而且这样有一种“寄人篱下”的感觉，很不爽。  
于是就得自建git服务再部署了。  
所幸过程也并不繁琐，简单来说也就下面几步：
1. 先建立git服务，配置好远程仓库
2. 在hexo配置中设置push到远程仓库
3. 设置git hook，钩子把静态资源拷贝到自定义网站目录
4. nginx配置web server
剩下的就用hexo原生的生成静态文件，上传部署，两步走即可
**下面讲讲详细过程**
<!-- more -->

# 在服务器上搭建git服务并配置
> 当然首先服务器上得装好git，用软件包管理安装即可，不多赘述

## 创建git用户
这里我们创建一个git用户，用来运行git服务
{% codeblock lang:bash %}
sudo adduser git
{% endcodeblock %}
## 设置免密登录（ssh密钥对）
由于你的本地Git仓库和Git仓库之间的传输是通过SSH加密的，所以之后本机hexo部署上传需要设置密钥登录
首先在本地机生成密钥对`ssh-keygen -t rsa`
{% note info %}这里详细的关于密钥对的设置和介绍我曾经在hadoop的那篇博文里提过了  
不了解ssh的可以去看一下 [点击这里跳转](https://blog8.flyky.org/20170928/notes_of_big_data_study-Hadoop-Fdistributed-build/#生成公私钥并配置免密登录)
{% endnote %}
**收集所有需要登录的用户的公钥，就是他们自己的`id_rsa.pub`文件，把所有公钥内容导入到`/home/git/.ssh/authorized_keys`文件里，一行一个。**
这里我们就直接把本地的`~/.ssh/id_rsa.pub`内容直接复制添加到服务器的`/home/git/.ssh/authorized_keys`文件即可。

之后本机尝试免密登录服务器git用户`ssh git@server.ip`，如果可以表示之前的设置无误。
那现在我们为了安全起见就可以**禁用git用户登录shell了**
编辑服务器`/etc/passwd`文件，找到类似下面的这行  
`git:x:1001:1001:,,,:/home/git:/bin/bash`  
将其改为  
`git:x:1001:1001:,,,:/home/git:/usr/bin/git-shell`
即可。这样，git用户可以正常通过ssh使用git，但无法登录shell。

{% note warning %}这里测试时如果无法免密登录，有很大的原因是因为服务器git用户目录的.ssh目录权限和里面文件权限问题  这里通过chmod命令将.ssh目录权限设置成700，里面的文件权限为600，并且owner为git
 这个在[之前的hadoop博文](https://blog8.flyky.org/20170928/notes_of_big_data_study-Hadoop-Fdistributed-build/#生成公私钥并配置免密登录)里也是提到了，可以去了解
{% endnote %}

## 初始化远程Git仓库
首先自定义git仓库位置，假定是`/home/git/blog.git`，在`/home/git`目录下输入命令
{% codeblock lang:bash %}
sudo git init --bare blog.git
{% endcodeblock %}   
Git就会创建一个裸仓库  
   
然后我们把该目录的owner改为git    
{% codeblock lang:bash %}
sudo chown -R git:git blog.git
{% endcodeblock %}   
  
现在我们在本地随便什么地方，试试clone这个裸仓库，如果没问题即可 
{% codeblock lang:bash %}
git clone git@server.ip:~/blog.git 
{% endcodeblock %}   

# 设置git hook（钩子）
首先cd到远程仓库的hooks目录下，`cd /home/git/blog.git/hooks`  
之后创建并编写`post-receive`文件，`vim post-receive`  
{% codeblock lang:bash %}
#!/bin/bash -l
GIT_REPO=/home/git/blog.git
TMP_GIT_CLONE=/home/git/tmp/blog
PUBLIC_WWW=/home/git/project/blog
rm -rf ${TMP_GIT_CLONE}
git clone $GIT_REPO $TMP_GIT_CLONE
rm -rf ${PUBLIC_WWW}
cp -rf ${TMP_GIT_CLONE} ${PUBLIC_WWW}
{% endcodeblock %}
其中public_www就是用于最终存放nginx映射的静态资源文件目录  
*** 通过此脚本，每次仓库接收到新上传的内容时都会自动拷贝资源到该目录下  ***

> 这里的`tmp`目录可能要自己新建，`mkdir /home/git/tmp`即可。  

然后记得更改脚本和各个目录的权限
{% codeblock lang:bash %}
chmod +x /home/git/blog.git/hooks/post-receive
chmod 777 -R /home/git/blog.git
chmod 777 -R /home/git/project
chmod 777 -R /home/git/tmp

chown -R git:git /home/git/
{% endcodeblock %} 

# 在hexo配置中设置push到远程仓库
修改hexo工程的`_config.yml`文件，在`deploy`项中填写：
{% codeblock lang:yaml %}
deploy:
    type: git
    message: update
    repo: git@server.ip:~/blog.git
    branch: master
{% endcodeblock %}   

# Nginx 配置
**这一步熟悉Nginx的小伙伴应该可以自己写**，我这里就贴一个样例，目录啥的都是上面提到的
{% codeblock %}
server {
    listen  80;
    server_name your.server.name
    location / {
        root    /home/git/project/blog;
    }
}
{% endcodeblock %} 
这种最简单的就可以了

*当然如果你是像我这样用http的话就得稍微改一改，还得把80重定向到443  
这种设置如果你用到了不会的话就自行百度吧～*

# 本地测试hexo博客后上传部署
这就是最后啦，每次写完博文或者修改之后，先在本地工程目录下跑一下服务
{% codeblock lang:bash %}
hexo s -p 端口号
{% endcodeblock %}   
然后打开浏览器查看测试的博客页面

没有问题的话就可以生成静态资源了，当然先清除掉`public`目录下已有的静态资源文件  
{% codeblock lang:bash %}
hexo clean
hexo g
{% endcodeblock %}   
  
这之后你可以使用这个命令测试
{% codeblock lang:bash %}
hexo s -s -p 端口号
{% endcodeblock %} 
多加一个`-s`表示只用静态资源跑服务器测试，测试一下静态资源的生成，同样打开浏览器查看网站  
  
现在确认无误之后就要**把静态资源上传部署到服务器**了
{% codeblock lang:bash %}
hexo d
{% endcodeblock %} 
不报错的话说明没有问题啦，大功告成