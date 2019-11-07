---
title: 我的博客尝试
date: 2017-09-22 01:38:50
tags: 
    - memo
    - hexo
categories:
	- Tech
	- hexo
---
Welcome to my blog space~
嘛一直就有做个博客的打算，之前站点只上线了简陋的『实验站』，本来打算自己来搭建博客框架，不过果然没有这个闲心，还是用别人成熟的开源框架吧。用了hexo纯粹是因为想用node
**这边就记一记写hexo博客的一些tips吧**

<!-- more -->

# 手动截断文章（设置「阅读全文」）
直接在需要截断的位置添加 `<!-- more -->`标签即可

# 加密博文
## 安装配置加密插件
- 首先安装加密插件  
  {% codeblock lang:Bash %}
npm install hexo-blog-encrypt
{% endcodeblock %}
- 然后在项目根目录下的 `_config.yml`文件里添加下面配置  
    {% codeblock lang:yaml %}
# Security
##
encrypt:
    enable: true
{% endcodeblock %}  
 这样就可以使用加密插件了
## 使用
在需要加密的文章头部写入password
  {% codeblock lang:markdown %}
---
title: ~ frp内网穿透配置 ~
date: 2018-12-06 19:07:02
password: 0123456789
abstract: Welcome to my blog, enter password to read.
message: 该博文暂未完成，完成后将开放.
---
{% endcodeblock %}  
*这样就需要密码访问了*

# 压缩博文静态资源
## 使用hexo-neat插件
 > [使用hexo-neat插件压缩页面静态资源](https://blog.csdn.net/lewky_liu/article/details/82432003)
## 使用gulp
- 首先在项目根目录执行`npm install gulp -g` 安装gulp  
  安装好后执行`gulp -v`看一下gulp工具版本，当前版本是4，（因为gulp3和4在配置文件里的写法有写不同，可能会踩坑）
- 接下来在项目里安装4个模块，根目录执行`npm install gulp-htmlclean gulp-htmlmin gulp-minify-css gulp-uglify --save`
  ```
  gulp-imagemin@6.1.1
  gulp-htmlmin@5.0.1
  gulp-minify-css@1.2.4
  gulp-htmlclean@2.7.22
  gulp-uglify@3.0.2```
 
- 然后在项目根目录添加`gulpfile.js`文件，内容为
  {% codeblock lang:js %}
  var gulp = require('gulp');
var minifycss = require('gulp-minify-css');
var uglify = require('gulp-uglify');
var htmlmin = require('gulp-htmlmin');
var htmlclean = require('gulp-htmlclean');
var imagemin = require('gulp-imagemin');
 
// 压缩html
gulp.task('minify-html', function() {
    return gulp.src('./public/**/*.html')
        .pipe(htmlclean())
        .pipe(htmlmin({
            removeComments: true,
            minifyJS: true,
            minifyCSS: true,
            minifyURLs: true,
        }))
        .pipe(gulp.dest('./public'))
});
// 压缩css
gulp.task('minify-css', function() {
    return gulp.src('./public/**/*.css')
        .pipe(minifycss({
            compatibility: 'ie8'
        }))
        .pipe(gulp.dest('./public'));
});
// 压缩js
gulp.task('minify-js', function() {
    return gulp.src('./public/js/**/*.js')
        .pipe(uglify())
        .pipe(gulp.dest('./public'));
});

//4.0以前的写法 
//gulp.task('default', [
  //  'minify-html', 'minify-css', 'minify-js'
//]);
//4.0以后的写法
// 执行 gulp 命令时执行的任务
gulp.task('default',gulp.series(gulp.parallel('minify-html','minify-css','minify-js')));
  {% endcodeblock %}

- 之后每次生成部署的时候直接`hexo clean`，`hexo g && gulp`， `hexo d`三部即可

# 相关博客教程
懒得写了，直接上一些不错的链接吧
- [http://yearito.cn/categories/技术/博客/](http://yearito.cn/categories/%E6%8A%80%E6%9C%AF/%E5%8D%9A%E5%AE%A2/)
-  [hexo搭建个人免费博客——自定义页面样式一](http://www.cduyzh.com/hexo-settings-3/)