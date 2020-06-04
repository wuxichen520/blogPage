---
title: hexo
tags:
- hexo
categories:
- hexo            
---

## 初始化
```
hexo init
```

### 本地操作
站点_config.yml文件
``` bash
deploy:
  type: git
  repo: https://github.com/wuxichen520/blog.git
  branch: master


 # URL 
## If your site is put in a subdirectory, set url as 'http://yoursite.com/child' and root as '/child/'
url: http://yoursite.com
root: /blog/    //js路径更改

//发布代码
npm install hexo-deployer-git --save
```
执行 `hexo deploy`或者`hexo d`  部署到github

在`C:/users`中的个人文件夹下面新建一个名为`_netrc`的文件，编辑内容为
``` bash
machine github.com
login username
password userpassword
```
其中`username`和`userpassword`是Github的用户名和密码，这样就不必再重复输入用户名和密码了，但使用明文存

在浏览器中访问username.github.io,我们发现之前运行在本地的博客已经成功发布到网站上面去了

### 修改Hexo主题 （ NexT 主题）

下载 NexT 主题

``` bash
$ cd <博客存放的目录>
$ git clone https://github.com/iissnan/hexo-theme-next themes/next
```

设置站点配置文件_config.yml：
```
theme: next
```
#### 选择scheme
打开主题`_config.yml`文件,查找`scheme`，可以看到如下四种不同的风格方案：
``` text
scheme: Muse
#scheme: Mist
#scheme: Pisces
#scheme: Gemini
```
#### 设置语言
站点`_config.yml`，查找`language`，设置成`language: zh-Hans`或`language: zh-CN`，即显示简体中文


#### 设置菜单及对应页面（跳转路由存在%20的情况）
打开主题`_config.yml`文件查找`menu`
```
menu:
  home: / || home   ===>/|| home   路由存在%20的情况下
  #about: /about/ || user   ===>/about/|| user
  tags: /tags/ || tags
  categories: /categories/ || th
  archives: /archives/ || archive
  #schedule: /schedule/ || calendar
  #sitemap: /sitemap.xml || sitemap
  #commonweal: /404/ || heartbeat
```
新添加的菜单需要翻译对应的中文，打开/theme/next/languages/zh-CN.yml，在menu下自定义，如：
```
menu:
  resources: 资源
```
------------------------------------------------------------------------
hexo new page "categories"
时在根目录的source文件夹下会生成一个categories文件，文件中有一个index.md文件，修改内容如下
```
---
title: 分类
date: 2017-12-14 13:05:38
type: "categories"
comments: false
---
```
注：如果有启用评论，默认页面带有评论。需要关闭的话，添加字段comments并将值设置为false

------------------------------------------------------------------------

#### 设定站点建立时间

打开**themes/next/下的_config.yml**，查找since
```
footer:
  # Specify the date when the site was setup.
  # If not defined, current year will be used.
  #since: 2015
```

#### 美化

#####  设置头像

**themes/next/下的_config.yml**，查找avatar
```
# Sidebar Avatar
# in theme directory(source/images): /images/avatar.gif
# in site  directory(source/uploads): /uploads/avatar.gif
avatar: /images/avatar/1.jpeg
```

##### 网站图标设置
图标素材网站：[iconfont](https://www.iconfont.cn/)
下载16x16以及32x32大小的PNG格式图标，置于`/themes/next/source/images/`下
打开`themes/next/`下的`_config.yml`，查找`favicon`,修改`small`和`medium`的路径为下载的图标路径
```
favicon:
  small: /images/heart-16.png
  medium: /images/heart-32.png
  apple_touch_icon: /images/apple-touch-icon-next.png
  safari_pinned_tab: /images/logo.svg
```

#####  背景动画

`theme/next`目录
``` bash
git clone https://github.com/theme-next/theme-next-three source/lib/three
```

将`themes/next/_config.yml`中查找`Canvas-nest`，将想要的效果改为true即可
```
# Canvas-nest
canvas_nest: true

# three_waves
three_waves: false

# canvas_lines
canvas_lines: false

# canvas_sphere
canvas_sphere: false

```

#####  背景图片/顶栏、底栏图片（背景色）、侧栏背景及内部文字颜色   （未确认）

`theme/next/source/css/_custom/custom.styl`添加以下代码

```
//背景图片
body{   
        background:url(图片链接);
        background-size:cover;
        background-repeat:no-repeat;
        background-attachment:fixed;
        background-position:center;
}
//顶栏图片
.header {
          background:url(图片链接) none repeat scroll !important;
}
//底栏背景色
.footer {
          background:rgba(颜色rgb,透明度) none repeat scroll !important;
}
//侧栏图片及内部文字颜色修改
#sidebar {
            background:url(图片链接);
            background-size: cover;
            background-position:center;
            background-repeat:no-repeat;
            p,span,a {color: 颜色代码;}
}

```

#####  侧栏置于左侧，修改控制按钮样式
```
`themes/next/`下的`_config.yml`
sidebar:
  # Sidebar Position, available value: left | right (only for Pisces | Gemini).
  position: left
  #position: right
```

#####  文章底部标签显示的优化  （无反应）

`/themes/next/layout/_macro/post.swig`，搜索 `rel="tag">#`，将 `#` 换成 `<i class="fa fa-tag"></i>`

#####  文章添加阴影、透明效果

打开`theme/next/source/css/_custom/custom.styl`，添加以下代码
``` css
// 主页文章添加阴影效果
.post {
   margin-top: 60px;
   margin-bottom: 60px;
   padding: 25px;
   background:rgba(255,255,255,0.9) none repeat scroll !important;
   -webkit-box-shadow: 0 0 5px rgba(202, 203, 203, .5);
   -moz-box-shadow: 0 0 5px rgba(202, 203, 204, .5);
}
```

#####  点击侧栏头像回到首页

修改`/themes/next/layout/_macro/sidebar.swig`，找到如下代码：
```
<img class="site-author-image" itemprop="image"
    src="{{ url_for( theme.avatar | default(theme.images + '/avatar.gif') ) }}"
    alt="{{ theme.author }}" />
```
在其前后加上`<a href="/"></a>`即可，如下：
```
<a href="/">
   <img class="site-author-image" itemprop="image"
       src="{{ url_for( theme.avatar | default(theme.images + '/avatar.gif') ) }}"
       alt="{{ theme.author }}" />
</a>
```

######  修改中文字体 (未确认)
打开`themes/next/`下的`_config.yml`，查找font


#####  新建文章时，在相同目录下创建同名文件夹（便于图片管理） (未确认)

打开站点配置文件_config.yml，搜索post_asset_folder字段，设置其值为true
安装hexo-asset-image：npm install hexo-asset-image --save
此时hexo new "fileName"会在/source/_posts目录下创建同名的文件夹


#####  首页显示文章摘要（阅读全文）及配图 (未确认)

#####  给页面、侧边栏添加背景图片 (未确认)
 
#####  图片可点击放大查看，放大后可关闭 (未确认)
打开站点配置文件_config.yml，搜索fancybox字段，设置其值为true
进入到theme/text/文件夹下，打开git bash
git clone https://github.com/theme-next/theme-next-fancybox3 source/lib/fancybox

#####  博客总访问量统计 (未确认)

#####  文章末尾添加版权声明 (未确认)

#####  本地搜索

#####  文章置顶


#####  鼠标点击红心特效

`/themes/next/source/js/src`下新建文件 `clicklove.js` ，接着把下面的代码拷贝粘贴到 clicklove.js 文件中：
``` js
!function(e,t,a){function n(){c(".heart{width: 10px;height: 10px;position: fixed;background: #f00;transform: rotate(45deg);-webkit-transform: rotate(45deg);-moz-transform: rotate(45deg);}.heart:after,.heart:before{content: '';width: inherit;height: inherit;background: inherit;border-radius: 50%;-webkit-border-radius: 50%;-moz-border-radius: 50%;position: fixed;}.heart:after{top: -5px;}.heart:before{left: -5px;}"),o(),r()}function r(){for(var e=0;e<d.length;e++)d[e].alpha<=0?(t.body.removeChild(d[e].el),d.splice(e,1)):(d[e].y--,d[e].scale+=.004,d[e].alpha-=.013,d[e].el.style.cssText="left:"+d[e].x+"px;top:"+d[e].y+"px;opacity:"+d[e].alpha+";transform:scale("+d[e].scale+","+d[e].scale+") rotate(45deg);background:"+d[e].color+";z-index:99999");requestAnimationFrame(r)}function o(){var t="function"==typeof e.onclick&&e.onclick;e.onclick=function(e){t&&t(),i(e)}}function i(e){var a=t.createElement("div");a.className="heart",d.push({el:a,x:e.clientX-5,y:e.clientY-5,scale:1,alpha:1,color:s()}),t.body.appendChild(a)}function c(e){var a=t.createElement("style");a.type="text/css";try{a.appendChild(t.createTextNode(e))}catch(t){a.styleSheet.cssText=e}t.getElementsByTagName("head")[0].appendChild(a)}function s(){return"rgb("+~~(255*Math.random())+","+~~(255*Math.random())+","+~~(255*Math.random())+")"}var d=[];e.requestAnimationFrame=function(){return e.requestAnimationFrame||e.webkitRequestAnimationFrame||e.mozRequestAnimationFrame||e.oRequestAnimationFrame||e.msRequestAnimationFrame||function(e){setTimeout(e,1e3/60)}}(),n()}(window,document);

```
在`\themes\next\layout\_layout.swig`文件末尾添加：
``` html
<script type="text/javascript" src="/blog/js/src/clicklove.js"></script>
```
#####  文章结束标志

在路径 `\themes\next\layout\_macro` 中新建 `passage-end-tag.swig` 文件,并添加以下内容：
```html
<div>
    {% if not is_index %}
        <div style="text-align:center;color: #ccc;font-size:14px;">-------------本文结束<i class="fa fa-paw"></i>感谢您的阅读-------------</div>
    {% endif %}
</div>

```
接着打开\themes\next\layout\_macro\post.swig文件， post-footer 之前添加如代码：
```html
<div>
  {% if not is_index %}
    {% include 'passage-end-tag.swig' %}
  {% endif %}
</div>
```

##### 侧边栏设置

主题配置文件，查找`social`  `链接名:链接地址 || 链接图标`
```
social:
  GitHub: https://github.com/wuxichen520 || github
```

##### 设置博客底部布局
```
footer:
  # Specify the date when the site was setup.
  # If not defined, current year will be used.
  since: 2019  #建站时间
  # Icon between year and copyright info.
  icon: user
  # If not defined, will be used `author` from Hexo main config.
  copyright: 吴惜晨
  powered: false
  
  theme:
    # Theme & scheme info link (Theme - NexT.scheme).
    enable: false  #是否显示主题信息
    # Version info of NexT after scheme info (vX.X.X).
    version: false #是否显示主题版本

```

##### 添加打赏
##### 增加阅读次数/时长和访客数



https://fontawesome.com/icons?from=io




 
