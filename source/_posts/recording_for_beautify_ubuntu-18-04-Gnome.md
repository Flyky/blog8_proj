---
title: ubuntu 18.04 (Gnome桌面)等的个人美化过程记录
date: 2019-11-04 16:04:00
tags:
	- ubuntu
	- 美化
	- Gnome
	- zsh
	- oh my zsh
	- grub美化
categories:
  - Tech
	- ubuntu
	- 美化
---

今天才把物理机上的Ubuntu升到了18.04，原因有很大一部分是因为不想去鼓捣桌面界面之类的，毕竟***Ubuntu从18.04开始，桌面程序放弃了unity而回归了Gnome***～～美化是一定要美化的，毕竟这是自己电脑里用作图形桌面的，又不是要做server，要看的东西当然要赏心悦目啦。

# 写在前面
首先先看看美化后的桌面效果，这里用的是仿Mac的Mojave-dark主题
![front1](https://cos14.flyky.org/blog8/front1.webp)

<!-- more -->
由于ubuntu回归Gnome桌面，以下的美化过程也是针对Gnome3的，所以**大部分的美化过程可以套用在其他系统的Gnome桌面上**，可能主题解压安装的目录会有些不同。
嘛，安装自己慢慢鼓捣就好，上网搜一搜，毕竟用的都是别人写的设计的主题。

# 美化资源下载网站等介绍
## 资源网站介绍
[Gnome的外观主题等资源网站： https://www.gnome-look.org/](https://www.gnome-look.org/)
这个网站里包含了Gnome桌面的各种外观资源，网站左侧分类栏里可以看到如GTK3、GTK2的系统主题，图标icon主题，grub主题，Plymouth开机画面主题、GDM (GNOME Display Manager,用户登录界面，显示管理器)主题 乃至鼠标主题等
![gnomelook-left](https://cos14.flyky.org/blog8/gnomelook-left.webp)
## 下载外观资源
### GTK3桌面主题
在左侧分类栏选择想要下载的外观资源，首先是桌面主题，选择GTK3的主题（GTK3 Themes），然后会列出所有的GTK3主题，列表默认是Latest最新，点击Score按评分列出。
![gtk-score](https://cos14.flyky.org/blog8/gtk-score.webp)
那写这篇的这个时间里名为[【McMojave】](https://www.gnome-look.org/p/1275087/)的GTK3主题评分最高，当然我安装的也就是这个主题，以下也是描述安装这个主题，*当然如果你想安装其他的主题也都是一样的步骤*。
这边点进你想要安装的主题的详细信息页面，可以看到作者对主题的描述等，下面还有作者推荐的图标主题(icon theme)，这里点击【file】进入下载主题文件的页面
![mojave-infos](https://cos14.flyky.org/blog8/mojave-infos.webp)
这里有很多文件，**不需要全部下载**，下载需要的或者想要的就可以了。我下载了【Mojave-dark.tar.xz】和【Mojave-light.tar.xz】也就是暗色和亮色主题。这里的solid指的是非透明主题，至于alt是什么意思我也不清楚。。。而我在前言里的图片就是它的暗色主题啦。
![mojave-files](https://cos14.flyky.org/blog8/mojave-files.webp)       

### 图标主题资源
那到这里桌面主题就下载完成了，现在还需要去下载图标主题资源
像McMojave这个主题在详情页推荐了它对应的图标主题，我们直接点进去，在【file】里下载就可以了，同样有很多文件，我们只下载想要的就可以了。
那如果有的主题没有推荐的图标或者并不想用对应主题的推荐图标，我们也可以在Gnome-look网站左侧栏点击【Icon Themes】,找到自己心仪的图标主题，过程和之前描述的GTK3主题如出一辙，我就不多赘述了    

### 其他主题资源
那如果你需要下载其他的主题资源，譬如grub主题，Plymouth开机画面主题、GDM (GNOME Display Manager,用户登录界面，显示管理器)主题、Cursors鼠标主题等，下载过程也是跟上面描述的GTK3桌面主题一样


# 桌面系统美化
## 主题资源安装（解压）
将资源文件解压到对应的目录中
其中桌面主题解压到`/usr/share/themes/`目录下
图标主题或鼠标主题解压到`/usr/share/icons/`目录下
桌面/锁屏背景图放到`/usr/share/backgrounds/`目录下

{% note warning %}
这里要注意主题是以目录形式放在`themes`或者`icons`目录下的，不是直接散在那两个目录下的，解压完之后要注意ls看一下
{% endnote %}

## 主题配置工具安装
主题配置需要「gnome-tweak-tool」
{% codeblock lang:Bash %}
sudo apt-get install gnome-tweak-tool
sudo apt-get install gnome-shell-extensions
{% endcodeblock %}

## shell拓展安装
这里推荐用火狐浏览器插件安装拓展
1. 打开你的**Firefox火狐浏览器**，打开[https://extensions.gnome.org/](https://extensions.gnome.org/)
2. 网页提示需要完成安装浏览器插件和本地应用两步
直接点click here to install brower extension安装浏览器插件
3. 然后终端输入命令安装本地软件
{% codeblock lang:Bash %}
sudo apt install chrome-gnome-shell
{% endcodeblock %}
1. 安装完后打开[https://extensions.gnome.org/extension/19/user-themes/](https://extensions.gnome.org/extension/19/user-themes/)安装user-themes拓展

这边安装拓展的方式很简单，在user-themes的右边可以看到一个按钮。不需要在下面选择版本，直接点击按钮拨到「on」，过一会儿会弹出一个弹窗提示你是否安装，点安装即可。
![extension-user-themes](https://cos14.flyky.org/blog8/extension-user-themes.webp)

在这个网站里你还可以通过这种方式安装其他的拓展，建议安装
「Dash to Dock」、「Hide Top Bar」、「Simple net speed」这几个拓展

当然除了通过火狐浏览器插件安装拓展以外，也可以通过软件包管理来安装，这里不做描述了。

## 桌面美化过程
- 打开gnome-tweak-tool（优化），选择拓展，打开User themes （这里上一条通过火狐浏览器安装拓展已经打开的话就可以忽略这条）
- 选择外观，进行设置  
  这里【应用程序】指的是安装的GTK3的主题，光标指的是cursor鼠标主题，图标即icon主题，shell选择主题对应的即可；至于下面锁屏和背景的图像，选择自己复制在background目录下的图片即可  
  *如果shell上有感叹号，关闭tweak，按Alt+F2，输入r，执行后重新打开tweak再设置shell选项。*
![外观设置](https://cos14.flyky.org/blog8/外观设置.webp)
- 调整标题栏按钮位置   
选择【窗口】，滑到最下面，将【放置】改为左
- 调整Dock，使用Dash to Dock拓展   
选择【拓展】，开启【Dash to Dock】，点击设置图标(小齿轮)，然后按下图设置
![Dash_to_Dock_config](https://cos14.flyky.org/blog8/Dash_to_Dock_config.webp)

# 开关机启动动画美化
- 首先还是下载启动动画主题  
  还是在[gnome-look网站](https://www.gnome-look.org/)里，左侧栏选择【Plymouth Themes】找到你想要的主题  
  那我这里选择的是[Paw Ubuntu/Mint Floral](https://www.pling.com/p/1156215/)  
*这位[MARKOSPOKO](https://www.pling.com/u/markospoko/)还有不少不错的ubuntu Plymouth开关机主题，可以看一看选择一下*
- 将下载下来的主题压缩包解压，将解压下来的主题目录复制到`/usr/share/plymouth/themes/`目录
- 备份原来的plymouth配置文件并编辑修改
  {% codeblock lang:Bash %}
sudo cp /etc/alternatives/default.plymouth /etc/alternatives/default.plymouth.bak
sudo vim /etc/alternatives/default.plymouth
{% endcodeblock %}
- 最后将`default.plymouth`文件的最后两行按下图方式修改，注意修改的主题目录和自己下载复制的匹配，最后的script文件要自己去主题目录下ls看一下script文件名是什么。  
  ![plymouth-config](https://cos14.flyky.org/blog8/plymouth-config.webp)  

# 登录界面GDM（显示管理器）主题美化
- 还是在[gnome-look网站](https://www.gnome-look.org/)里，左侧栏选择【GDM Themes】找到你想要的主题 下载
 我这里演示的是仿Mac的[High Ubunterra](https://www.pling.com/s/Gnome/p/1207015/)，我也比较推荐这个。  
 打开High Ubunterra的详细描述页面，下面很详细的介绍了注意事项和安装方式，包括不同版本的Ubuntu应该下载不同的版本，还分为有密码和无密码两种  
 那我这里是Ubuntu18.04，于是我下载的是【High_Ubunterra_2.3(Pass).tar.xz】
- 将下载下来的压缩文件解压，按照主题的详细页面所描述的，使用install.sh脚本直接安装即可  
  ![high_ubunterra_install](https://cos14.flyky.org/blog8/high_ubunterra_install.webp) 
  然后根据终端提示的，按Alt+F2，输入r，执行后再按Win+L进入锁屏查看效果  
> **emm......本来应该是这样就可以了，但是实际上进入锁屏界面却发现情况有点不对，界面的背景图片没有了**  
> 于是我们查看  `/etc/alternatives/gdm3.css`文件里的lockDialogGroup类，发现背景图片指向`/usr/share/backgrounds/gdmlock.jpg `  
>  ![lockDialogGroup](https://cos14.flyky.org/blog8/lockDialogGroup.webp)  
>  但是在/usr/share/backgrounds/目录下ls并未发现gdmlock.jpg图片文件
>  于是问题找到了，也好解决
>  **只需要将背景图复制到`/usr/share/backgrounds/gdmlock.jpg` 即可，或者修改`/etc/alternatives/gdm3.css`文件，但更推荐复制背景图**

# grub 美化
- 还是在[gnome-look网站](https://www.gnome-look.org/)里，左侧栏选择【GRUB Themes】找到你想要的主题 下载
 我这里演示的是[Vimix](https://www.gnome-look.org/p/1009236/)主题  
- 这个主题的安装方式也是很简单粗暴，解压后在里面打开终端，使用`install.sh`脚本安装即可
 {% codeblock lang:Bash %}
sudo ./install.sh
{% endcodeblock %}    


# 终端美化
## gnome-terminal的配置
terminal的美化过程很简单，Ctrl+Alt+T打开终端  
- 选择【编辑】-> 【首选项】-> 【常规】，将第一项【默认在新终端中显示菜单栏】的勾去掉
-  然后点击下面的配置文件进行修改，将【使用系统主题的透明度】的勾去掉，再把【使用透明背景】勾上，在右边调整透明度  
  透明度不要太高，太透明就不好看清字了，稍微透明一点就可以了
## shell(zsh)
**推荐放弃bash，使用zsh配合[oh-my-zsh](https://ohmyz.sh/)起飞**  
### 安装zsh与oh-my-zsh
- 首先安装「zsh」 「git 」「curl  」
 {% codeblock lang:Bash %}
sudo apt-get install zsh  git  curl
{% endcodeblock %}
- 然后根据[oh-my-zsh](https://ohmyz.sh/)官网的安装脚本进行安装
{% tabs Tab标签列表 %}
  <!-- tab 通过curl安装 -->
    `sh -c "$(curl -fsSL https://raw.github.com/robbyrussell/oh-my-zsh/master/tools/install.sh)"`
  <!-- endtab -->
  <!-- tab 通过wget安装 -->
    `sh -c "$(wget https://raw.github.com/robbyrussell/oh-my-zsh/master/tools/install.sh -O -)"`
  <!-- endtab -->
{% endtabs %}

> 安装过程中会询问是否切换默认shell为zsh，记得选“Y”

>当然如果不能通过脚本直接安装oh-my-zsh的情况下，官方文档里也有手动安装的过程教程[https://github.com/robbyrussell/oh-my-zsh#manual-installation](https://github.com/robbyrussell/oh-my-zsh#manual-installation)

### 更改oh-my-zsh主题
更改oh-my-zsh主题需要修改zsh的配置
{% codeblock lang:Bash %}
vim ~/.zshrc
{% endcodeblock %}
在11行左右会有「ZSH_THEME」变量，也就是oh-my-zsh主题了，默认是`robbyrussell`，你可以去搜一搜有哪些好看的自带主题，修改这一变量的值为主题名即可  
我这里提几个我常用的，「ys」「agnoster」「amuse」「candy」
当然你也可以写「random」，每次打开终端都会随机选择一个主题呈现，也挺有趣的  
当然也可以选择几个固定的主题，每次在这些主题中随机，你只需要将`ZSH_THEME`变量修改`random`之后，将下面的`ZSH_THEME_RANDOM_CANDIDATES`变量的#注释去掉，并像这样修改即可
{% codeblock lang:Bash %}
ZSH_THEME_RANDOM_CANDIDATES=(
  "robbyrussell"
  "agnoster"
   "ys"
)
{% endcodeblock %}   

### powerline字体安装
oh-my-zsh的许多主题需要[powerline字体](https://github.com/powerline/fonts)支持
ubuntu可以直接通过apt安装  
{% codeblock lang:Bash %}
sudo apt-get install fonts-powerline
{% endcodeblock %}   
这时重启终端，像`agnoster`这样的主题应该就不会乱码了
