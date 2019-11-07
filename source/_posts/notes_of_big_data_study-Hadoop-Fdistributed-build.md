---
title: 大数据学习笔记| Hadoop - Hadoop伪分布式环境搭建
date: 2017-09-28 01:51:25
tags:
	- hadoop
	- bigdata
	- note
categories:
	- Tech
	- BigData
	- Hadoop
---

# 说说「伪分布式」
Hadoop的安装配置方式有三种：单机、伪分布式和完全分布式。  </br>
__单机模式__ 顾名思义，只在一台单机上运行，不需要与其他节点交互，不使用HDFS分布式文件系统，不加载Hadoop的守护进程。当你解压了hadoop的软件包后的默认最小模式，各种配置为空。  </br>
__完全分布式__ 需要在至少3台机器上配置hadoop节点，真正分布式地完成存储和计算任务。  </br>
__伪分布式__ 也很好理解，即在单机上搭建分布式系统。虽然是在单机上运行，但是通过配置实现用不同的Java进程模仿分布式运行的各类节点，即在一个机器上，既有namenode也有datanode，既有jobtracker也有tasktracker。既然并没有在多台机器上真正的搭建分布式环境，所以称之为「伪分布式」。  </br>
  </br>

# 安装环境
Hadoop需要安装在Linux环境下（当然macos也可以），无论是物理机linux或是虚拟机Linux均可，发行版本无所谓，我这里>使用的是ubuntu16.04 server,安装的Hadoop版本为2.8.1
  </br>
<!-- more -->
# 创建用户与修改hostname 
~~<em style="color: red">这一步对于伪分布式来说必要性不大，可以省略。如果是完全分布式的话还是有必要的。</em>~~


## 创建新用户
_假设创建一个名为“hadoop”的用户_
{% codeblock lang:Bash %}
useradd -m hadoop -s /bin/bash
{% endcodeblock %}
这里稍微解释一下。-m 选项表示在创建用户的同时，新建该用户的主目录（例子中是/home/hadoop目录）；-s选项设置用户
登录的shell，例子中使用bash，如果你使用其他shell，如zsh则改为/bin/zsh
接着使用passwd命令更改hadoop用户密码
{% codeblock lang:Bash %}
passwd hadoop
{% endcodeblock %}
输两次密码即可（弱口令也可以，确认使用即可）
## 修改Hostname
修改存放主机名的hostname文件即可
需要注意的是，不同的linux发行版本，主机名的配置文件不同
ubuntu为 /etc/hostname 文件, centos为 /etc/sysconfig/network 文件
修改对应hostname字段即可

*这里假设该主机的hostname为Master*

# jdk安装与配置
## 第一步. 安装
### 可以使用软件包管理安装openjdk
比如Debian系可以使用apt-get，redhat系可以使用yum，arch linux可以使用pacman,yaourt等前端
这里以ubuntu举例
{% codeblock lang:Bash %}
sudo apt-get install openjdk-8-jre
sudo apt-get install openjdk-8-jdk
{% endcodeblock %}
具体安装命令请移步链接[http://openjdk.java.net/install/index.html](http://openjdk.java.net/install/index.html)
### Oracle官网下载jdk安装
[Java SE Downloads](http://www.oracle.com/technetwork/java/javase/downloads/index.html)
在oracle官网里下载对应系统版本的jdk
![1hadoop-orajdk](https://cos14.flyky.org/blog8/1hadoop-orajdk.webp "Oracle_jdk-download")
## 第二步. 配置jdk环境变量
环境变量配置文件可以是/etc/profile 、 ~/.profile 、 ~/.bash_profile等其一均可
使用nano、vim等编辑器打开配置文件(注意sudo权限)，在文件末尾添加如下export配置
{% codeblock lang:Bash %}
export JAVA_HOME=/usr/lib/java/jdk1.8.0_144
export JRE_HOME=$JAVA_HOME/jre
export CLASSPATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar
export PATH=$PATH:$JAVA_HOME/bin:$JRE_HOME/bin
{% endcodeblock %}
注意你的JAVA_HOME的值要改成你所安装释放jdk的根目录（我这里将jdk安装在了/usr/lib/java目录下）
如果你是通过软件包管理安装openjdk 默认的安装根目录应该在/usr/lib/jvm/java-8-openjdk-adm64类似的目录位置，自己根据实际情况而变
  </br>
编辑过profile配置文件之后需要通过.(点）命令或者source命令将配置应用到当前shell环境中
{% codeblock lang:Bash %}
.   /etc/profile
{% endcodeblock %}或者
{% codeblock lang:Bash %}
source /etc/profile
{% endcodeblock %}
结束后可以尝试执行java和javac命令，看看是否配置成功
 </br>

# SSH server密钥对登录配置 （所谓的免密登录） 
这一步是为了集群所在主机的安全性。。。。。  </br> 
好吧其实果然还是因为，免密登录会很方便啊，否则每次hadoop登录都要输密码，一输就是要输好几次，会疯的！ 不过安全性确实很重要~
</br>    
## 安装ssh server（sshd）服务
因为大部分linux发行版，ssh client都是装好的,所以只需要安装server即可(执行一下ssh命令 如果提示找不到命令之类的错误那就是没有client啊~)      </br>
毋庸置疑使用软件包管理前端进行安装，这里同openjdk的安装，我使用的ubuntu使用apt-get命令安装openssh-server，其他的发行版本视自身情况而定。  </br>

{% codeblock lang:Bash %}
 sudo apt-get install openssh-server

#如果没有client则执行以下命令安装openssh-client
 sudo apt-get install openssh-client
{% endcodeblock %}
## 生成公私钥并配置免密登录
使用ssh-keygen命令生成密钥对
{% codeblock lang:Bash %}
ssh-keygen -t rsa 
#或者
ssh-keygen -t dsa
{% endcodeblock %}
之后一直回车（默认）即可
  </br>
操作结束后会在~/.ssh目录下生成一对public-private key pair
id_rsa，id_rsa.pub或者id_dsa，id_dsa.pub ，前者私钥 后者公钥
{% note success %}
公钥与私钥的关系大概可以这样描述
假设A免密登录B（即B为被登陆端）
B主机的`~/.ssh`目录下必须有名为`authorized_keys`（可以更改sshd的配置文件更改该文件名）的公钥列表文件，里面一行表示一个公钥，只要有这个公钥列表里的某个公钥的对应私钥，就可以通过这个私钥来登录B主机，而如果对应的私钥在A主机的`~/.ssh`目录里，就可以直接免密登录
A主机通过对应的私钥可以免密登录B主机，可以将对应的公钥内容添加进B主机的`authorized_keys`文件，或者ssh命令的 -i 选项设定可以使用的对应私钥
{% endnote %}
接下来将pub公钥内容添加到被登陆主机的	~/.ssh	目录即可
因为这里伪分布式是自己登录自己，所以直接改名为authorized_keys即可
{% codeblock lang:Bash %}
 cat id_dsa.pub >> ~/.ssh/authorized_keys
{% endcodeblock %}
哦差点忘了 看看你公钥文件和.ssh目录的权限
{% codeblock lang:Bash %}
#.ssh目录权限700
 chmod 700 -R ~/.ssh
#公钥列表文件600
 chmod 600 authorized_keys
{% endcodeblock %}
此时使用{% codeblock %}ssh localhost{% endcodeblock %}命令，不需要密码便可以登陆了

# Hadoop安装与配置 
## Hadoop的安装
![2hadoop-officialsite]([/images/2hadoop-officialsite.png](https://cos14.flyky.org/blog8/2hadoop-officialsite.webp) "Apache Hadoop Official Site")[Hadoop官网](http://hadoop.apache.org)下载hadoop release binary包
将binary包解压到你所要安装hadoop的路径目录（本例将hadoop释放到/usr/local目录下）
{% codeblock lang:Bash  %}
  sudo tar -zxvf hadoop-2.8.1.tar.gz -C /usr/local
{% endcodeblock %}
**_建议将解压后的hadoop-2.8.1目录重命名为hadoop，以方便操作_**
解压完之后的hadoop命令已经可以使用,使用hadoop version命令查看当前hadoop版本

{% codeblock lang:Bash  %}
 /usr/local/hadoop/bin/hadoop version
{% endcodeblock %}
此时的Hadoop便是最小单机模式，因为并没有对节点等进行配置

__在这里要将解压的hadoop目录的权限更改，更改所有者为当前用户，否则之后的一些操作可能会出现权限问题__
{% codeblock lang:Bash  %}
cd /usr/local
sudo chown -R hadoop:hadoop hadoop
{% endcodeblock %}
执行ls -l之后可以看到hadoop的目录变为了当前用户（本例中为hadoop用户）

为了方便使用hadoop等命令，建议将hadoop/bin目录添加到系统环境变量PATH中（过程同配置jdk环境变量）
配置系统配置文件（/etc/profile、~/.bashrc、~/.profile等均可）
添加下列语句
{% codeblock lang:Bash %}
export HADOOP_HOME=/usr/local/hadoop
export PATH=$HADOOP_HOME/bin:$HADOOP_HOME
{% endcodeblock %}
保存退出编辑器后使用.(点命令)或source命令将更改应用到当前shell
## Hadoop集群配置（伪分布式）
我们需要配置hadoop的配置文件来搭建伪分布式环境
Hadoop的配置文件存放在hadoop/etc/hadoop/目录下

需要配置的配置文件如下

1. hadoop-env.sh
   在该配置文件中，建议修改其中JAVA_HOME项为jdk安装的绝对地址

2. core-site.xml
	在该配置文件中，主要需要配置两个配置项
	__fs.defaultFS和hadoop.tmp.dir__

	编辑器打开配置文件后会发现一对configuration标签，在该标签内添加如下配置
	{% codeblock lang:xml %}
	<configuration>
		<!-- 从这里开始配置 -->
		<property>
			<name>fs.defaultFS</name>
			<value>hdfs://master:9000</value>
		</property>
		<property>
			<name>hadoop.tmp.dir</name>
			<value>file:/usr/local/hadoop/tmp</value>
		</property>
		<!-- 配置结束 -->
	</configuration>
	{% endcodeblock %}

	* * * * * * *

	{% note warning %}
		<strong style="color: red; font-size: 1.5em">关于以下xml配置文件的配置说明</strong>
		下面的配置基本都是xml文件，配置项均配置在configuration标签内的property标签
		property标签内，配置项名称（键）括在name标签内，配置的值括在value标签内，具体如下
		{% codeblock lang:xml %}
			<configuration>
				<property>
					<name>###</name>
					<value>###</value>
				</property>
			</configuration>
		{% endcodeblock %}
		以下的配置项，基本的配置项会将xml直接贴出以供参考（复制）方便食用；其他的配置项会以<span style="background-color: #0066FF; color: white; font-weight: 2px; margin: 0 2px; padding: 1px 3px">name：value</span>键值对的形式贴出，如有用到可以直接套进以上的“模板”食用
	{% endnote %}
3. hdfs-site.xml
	{% codeblock lang:xml %}
	<configuration>
		<!-- 从这里开始配置 -->
		<property>
			<name>dfs.namenode.secondary.http-address</name>
			<value>master:50090</value>
		</property>
		<property>
			<name>dfs.replication</name>
			<value>1</value>
		</property>
		<property>
			<name>dfs.namenode.name.dir</name>
			<value>file:/usr/local/hadoop/tmp/dfs/name</value>
		</property>
		<property>
			<name>dfs.datanode.data.dir</name>
			<value>file:/usr/local/hadoop/tmp/dfs/data</value>
		</property>
		<!-- 配置结束 -->
	</configuration>
	{% endcodeblock %}

4. mapred-site.xml (template)
	在目录下并无mapred-site.xml文件，但是奇怪的是有一个mapred-site.xml.template的模板？文件
	于是我们将mapred-site.xml.template复制成mapred-site.xml再在该文件内进行配置
	
	{% codeblock lang:xml %}
	<configuration>
		<!-- 从这里开始配置 -->
		<property>
			<name>mapreduce.framework.name</name>
			<value>yarn</value>
		</property>
		<property>
			<name>mapreduce.jobhistory.address</name>
			<value>10020</value>
		</property>
		<property>
			<name>mapreduce.jobhistory.webapp.address</name>
			<value>19888</value>
		</property>
		<!-- 配置结束 -->
	</configuration>
	{% endcodeblock %}

5. yarn-site.xml
	{% codeblock lang:xml %}
	<configuration>
		<!-- 从这里开始配置 -->
		<property>
			<name>yarn.resourcemanager.hostname</name>
			<value>Master</value>
		</property>
		<property>
			<name>yarn.nodemanager.aux-services</name>
			<value>mapreduce_shuffle</value>
		</property>
		<!-- 配置结束 -->
	</configuration>
	{% endcodeblock %}

6. slaves
	~~嘛，因为是伪分布式，这个就不需要配置了~~

## 格式化Namenode
{% codeblock lang:Bash  %}
  hdfs namenode -format
{% endcodeblock %}
格式化namenode成功的话，会看到 "successfully formatted" 和 "Exitting with status 0" 的提示，若为 "Exitting with status 1" 或抛出异常则是出错，检查配置文件后需要删除tmp目录并重新格式化