
---
title: HEXO+ICARUS主题配置安装
date: 2017-05-26 12:12:57
categories: 
- web前端

tags:
- jQuery
- 表格
- 表单验证
---

#### 1 前言
曾经折腾过Hexo+Next主题，但是很长时间都没有文章产出，因为习惯了使用印象笔记记录东西，现在想着换个主题，重新开始。


#### 2 安装Git和Node.JS
* 2.1下载Git和Node.js
`Git： https://git-scm.com/download/win/`
`Node.js：https://nodejs.org/zh-cn/download/`

安装全部默认设置，一路Next即可。

#### 3 安装Hexo
* 3.1下载Hexo
创建好一个文件夹（不要在桌面），右键点击Git Bash Here,输入以下命令
`npm install -g hexo-cli`

    > 这个过程没有提示反馈，等待即可，大约1-2分钟即可，如果太久没有反应，【Ctrl+C】停止下载，可以将npm的源更改为淘宝镜像，输入以下命令，再尝试下载Hexo
    ```
    npm config set registry "https://registry.npm.taobao.org/"
    npm config set electron_mirror "https://npm.taobao.org/mirrors/electron/"
    ```
    下载完成后初始化Hexo
`hexo init`
完成后在初始化的路径下会有如下文件夹，后面标注对应作用
![](images/screenshot_1586754477758.png)

* 3.2运行Hexo
使用以下命令生成博客并启动本地浏览
    ```
    hexo g
    hexo s
    ```
    打开 http://localhost:4000 浏览，注意复制链接时不要使用【Ctrl+C】，因为这会停止本地浏览服务

* 3.3配置Hexo
打开_config.yml，一般需要配置以下参数

    ```
    title:           #网站标题
    author:          #作者
    language:        #语言（英语en，中文zh-CN）
    
    url:             #网站url（域名）
    permalink:       #文章链接（例article/:urlname.html）
    
    # Extensions
    theme:     #主题
    
    # Deployment
    deploy:
      type: 
      repo:        #仓库SSH地址
      branch: 
    ```
    permalink设置尽量短一些不含中文，并且可以设置一个urlname参数加html后缀，方便以后在文章头部设置该属性，实现自定义文章的url

* 3.4下载主题
这里使用的icarus主题
`https://github.com/ppoffice/hexo-theme-icarus`
根据说明页，使用命令下载

    `git clone https://github.com/ppoffice/hexo-theme-icarus.git themes/icarus`
或直接下载ZIP包
    
    将下载的文件解压，复制到/theme/中,主题文件夹过长可以重命名为icarus，然后修改Hexo配置文件_config.yml中的themes参数

    theme: icarus    #主题
重新使用命令即可本地预览效果
    ```
    hexo g
    hexo s
    ```
    其他主题可以在GitHub上搜索hexo-theme
    `https://github.com/search?q=hexo-theme`

#### 4 配置GitHub
* 4.1创建仓库
登录GitHub账号后，打开 https://github.com/new页面创建仓库，参数默认即可，`注意仓库名称一定要符合 用户名.github.io 的格式


* 4.2创建SSH key
输入命令（注意有引号），敲三下回车即可

    `ssh-keygen -t rsa -C "邮箱地址"`
之后在电脑用户目录如 C:\Users\Millet\.ssh下生成了id_rsa和id_rsa.pub两个文件
将id_rsa.pub文件中的内容复制，打开GitHub个人设置 SSH and GPG keys，点击New SSH key，粘贴到Key里面，标题随便取

    输入命令，绑定GitHub账户
    ```
    git config --global user.email "邮箱地址"
    git config --global user.name "用户名"
     
    测试连接
    
    ssh -T git@github.com
    yes
     
    提示 You’ve successfully authenticated ，表明已经成功连接
    ```
* 4.3部署Hexo到GitHub
安装部署插件
`npm install hexo-deployer-git --save`
修改Hexo配置文件_config.yml
    ```
    deploy:
      type: git
      repo: git@github.com:用户名/用户名.github.io.git     #仓库SSH地址
      branch: master
     
    开始部署
    
    hexo g
    hexo d
    ```
    等待一会，打开 https://用户名.github.io 即可看到效果

* 4.4绑定域名
1.在自己域名管理面板中添加CNAME记录，主机头为：www，值为：用户名.github.io
2.在Hexo的\source\目录下创建一个CNAME.txt,输入你的域名，然后将txt后缀删除
3.使用hexo g及hexo d部署到GitHub
4.在GitHub仓库设置中,输入域名，开启SSL证书(注意必须要二级域名才能开启，不带主机头顶级域名无法开启)

#### 5 主题配置
* 5.1主题参数配置
导航栏显示及个人信息控件修改
打开/themes/icarus/_config.yml，修改以下参数
    ```
    navbar:
        # Navigation bar menu links
        menu:  #菜单显示改为中文
            首页: /
            归档: /archives/
            分类: /categories/
            标签: /tags/
            关于: /about/
            
    widgets:
        -
            # Widget name
            type: profile
            position: left  #放置在左边栏
            author:         #作者
            author_title:   #作者标题
            location:       #位置信息
            
    article:  #文章显示
        # Code highlight settings
        highlight: 
            # 代码高亮主题
            # https://github.com/highlightjs/highlight.js/tree/master/src/styles
            theme: atom-one-light  
            # 显示复制按钮
            clipboard: true
            # 代码是否默认折叠. Can be "folded", "unfolded"
            fold: unfolded
        # 文章预览图
        thumbnail: true
        # 阅读时间显示
        readtime: true        
    文章显示修改
    
    article:  #文章显示
        # Code highlight settings
        highlight: 
            # 代码高亮主题
            # https://github.com/highlightjs/highlight.js/tree/master/src/styles
            theme: atom-one-light  
            # 显示复制按钮
            clipboard: true
            # 代码是否默认折叠. Can be "folded", "unfolded"
            fold: unfolded
        # 文章预览图
        thumbnail: true
        # 阅读时间显示
        readtime: true      
    控件位置修改
    配置文件中的控件代码先后顺序决定了网页中两栏控件的先后顺序
    
        -
            # Widget name
            type: toc
            position: left  #控件的位置
        -
    删除控件
    如果需要去掉某个控件，那么可以在控件代码前加#，注意分割的符号 - 也要注释
    当然也可以直接删除代码
    
    #    -
    #        # Widget name
    #        type: category
    #        # Where should the widget be placed, left or right
    #       position: left
    
    控件固定
    修改以下false为true可以让控件不随滚动条滚动
    
    sidebar:
        # left sidebar settings
        left:  #左边栏固定
            sticky: false
        # right sidebar settings
        right: #右边栏固定
            sticky: false
    ```
* 5.2主题美化
icarus这款主题为全局三栏显示，这样浏览文章时显得过于拥挤（宽屏显示器跳过），所以搜索了一下，如何让文章页拓宽一点，少显示些控件，最后找到了下面这篇帖子

    Icarus 主题自定义 https://www.alphalxy.com/2019/03/customize-icarus/

    按照文章中的步骤配置即可，但是我测试后发现PC端成功改成了两栏，移动端布局错乱了，后面还是用了这位大佬的现成配置文件,感谢大佬分享。

    现成配置文件 https://github.com/AlphaLxy/hexo-theme-icarus

    使用方法：下载ZIP包，解压到Hexo的theme文件即可，按照上文的方法修改为自己的参数
    
    底部访问量及运行时间修改
    修改\themes\icarus\layout\common\footer.ejs 文件，将原来只显示人数的span标签删除，添加以下代码
```
    <span id="busuanzi_container_site_uv"> 来访 <span id="busuanzi_value_site_uv"></span>人</span>
    <span id="busuanzi_container_site_pv">, 总访问 <span id="busuanzi_value_site_pv"></span>次</span>

<span id="timeDate">载入天数...</span><span id="times">载入时分秒...</span>
	<script>
		var now = new Date(); 
		function createtime() { 
			var grt= new Date("02/20/2020 00:00:00"); /*建站时间*/
			now.setTime(now.getTime()+250); 
			days = (now - grt ) / 1000 / 60 / 60 / 24; dnum = Math.floor(days); 
			hours = (now - grt ) / 1000 / 60 / 60 - (24 * dnum); hnum = Math.floor(hours); 
			if(String(hnum).length ==1 ){hnum = "0" + hnum;} minutes = (now - grt ) / 1000 /60 - (24 * 60 * dnum) - (60 * hnum); 
			mnum = Math.floor(minutes); if(String(mnum).length ==1 ){mnum = "0" + mnum;} 
			seconds = (now - grt ) / 1000 - (24 * 60 * 60 * dnum) - (60 * 60 * hnum) - (60 * mnum); 
			snum = Math.round(seconds); if(String(snum).length ==1 ){snum = "0" + snum;} 
			document.getElementById("timeDate").innerHTML = "本站已安全运行 "+dnum+" 天 "; 
			document.getElementById("times").innerHTML = hnum + " 小时 " + mnum + " 分 " + snum + " 秒"; 
		} 
	setInterval("createtime()",250);
	</script>
```
再修改icarus配置文件，打开busuanzi统计

   ` busuanzi: true`

* 5.3插件
暂时未使用太多插件，觉得博客简洁点好，不能花里胡哨，有空再弄吧 ╮(￣▽￣)╭

* 5.4评论及分享
参考icarus官方文章
`https://blog.zhangruipeng.me/hexo-theme-icarus/categories/Plugins/Comment/`
`https://blog.zhangruipeng.me/hexo-theme-icarus/categories/Plugins/Share/`

#### 6 常见问题
修改配置或文章后浏览或部署到GitHub却未改变：
1.尝试清理浏览器缓存
2.使用hexo clean，删除生成的缓存，再重新hexo g
3.删除部署到GitHub的缓存文件夹.deploy_git，重新生成及部署

7文章格式
文章头部

```
title: 
date: 
urlname:                  #自定义文章URL
categories:           
tags: 
    - 标签

thumbnail:                #文章预览图
comments: true            #评论系统
toc: true                 #目录
mathjax: true             #公式渲染
 ```
可以将文章头部保存到\scaffolds\post.md模板中，下次使用hexo n生成文章自带头部
thumbnail参数可以填网址，也可以自己在\source\下创建一个images文件夹，将图片01.jpg放入，之后将thumbnail设置为/images/01.jpg即可调用
————————————————
「一粒米:D」的原创文章
原文链接：https://blog.csdn.net/milletloks/java/article/details/104445557
