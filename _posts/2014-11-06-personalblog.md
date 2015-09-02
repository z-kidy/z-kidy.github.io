---
layout: post
title: 使用github/gitcafe+jekyll搭建个人博客
category: git
---


一些程序员开始在[github](https://github.com/)网站上搭建blog，也就要介绍的[Github Pages](http://pages.github.com/)。

他们既拥有绝对管理权，又享受github带来的便利----不管何时何地，只要向主机提交commit，就能发布新文章。更妙的是，这一切还是免费的，github提供无限流量，世界各地都有理想的访问速度。

如何在github上搭建Blog，你可以从中掌握github的Pages功能，以及Jekyll软件的基本用法。

##**什么是git，github/gitcafe？**##
![github](/img/github.png)

github，号称程序员的Facebook，有着极高的人气，许多重要的项目都托管在上面。

gitcafe，为解决国内IT教育领域的诸多问题...可以理解为国内的类似github的平台

<!-- more -->

##**什么是github-page？**##
github Pages可以被认为是用户编写的、托管在github上的静态网页。

github提供模板，允许站内生成网页，但也允许用户自己编写网页，然后上传。有意思的是，这种上传并不是单纯的上传，而是会经过Jekyll程序的再处理。



##**Jekyll**##
[jekyll](http://jekyllrb.com/)是一个简单的免费的Blog生成工具，一个生成静态网页的工具，不需要数据库支持。它提供了模板、变量、插件等功能,配合第三方服务,例如[disqus](http://disqus.com/)。最关键的是jekyll可以免费部署在Github上，而且可以绑定自己的域名。

![Jekyll](http://jekyllrb.com/img/logo-2x.png)

**你先在本地编写符合Jekyll规范的网站源码，然后上传到github，由github生成并托管整个网站。**



##**一个使用Github Pages/Gitcafe Pages的实例**##
运行环境：

+ ubuntu14.04(个人目前使用)，其他linux发行版，mac类似。假如你使用的是windows...
+ git，[Git 中文教程](http://progit.org/book/zh/)在linux下安装，使用非常方便，windows下，有安装，使用的说明。

与GitHub建立好链接之后，就可以方便的使用它提供的Pages服务，GitHub Pages分两种:

+ 一种是你的GitHub用户名建立的**username**.github.io这样的用户&组织页（站）
+ 另一种是依附项目的pages

因为我们要搭建个人博客，这里我们选择第一种方式，形如**z-kidy**.github.io这样的可访问的站，每个用户名下面只能建一个。

###**一、登入github，创建**username**.github.io项目仓库。**###
这里我新创建了一个用户名为**ridy**的github账号。

![github项目创建](/img/githubpage.png)

创建好**ridy**.github.io项目之后，

    $cd workplace
    $git clone https://github.com/ridy/ridy.github.io.git
    $cd ridy.github.io
    
提交一个index.html文件，然后push到GitHub的master分支（也就是普通意义上的主干）。第一次页面生效根据内容大小需要一些时间，简单的hello world页面大概2分钟左右。

###**二、第一次提交**###
    $git init
    $vim index.html  /*创建和写index.html的内容*,比如写入hello world*/ 
    $git add .
    $git commit -m "first commit"
    $git remote add origin https://github.com/ridy/ridy.github.io.git
    $git push -u origin master

上面指令是对托管在github上项目仓库的远程操作
[git远程操作详解](http://www.ruanyifeng.com/blog/2014/06/git_remote.html)

然后稍等片刻，登入 [ridy.github.io](http://ridy.github.io/)即可看到自己的站点。

###**三、jekyll目录结构**###
[我为什么要写博客](http://beiyuu.com/why-blog/)

![blogging](/img/blogging.jpg)

之前的2步只是实现了简单的站点，成功生成一个可访问的站点。但谁也不愿意自己的站点就是一行hello world吧！如何实现博客系统，最关键思路是罗列你的文章，这个时候就要用到jekyll

在项目文件夹下，我们要生成这样基本目录的结构，然后增加文件，最后使用jekyll发行生成静态网页发行

    /ridy
        |-- index.html
        |-- _config.yml
        |-- _includes
        |-- _layouts
        |-- _posts
        |-- _site
        |-- 其他文件夹
    
1. **_config.yml**

这个文件是为了保存配置的。

2. **_includes**

可以用来存放一些小的可复用的模块，方便通过&#123;% include filename %&#125;灵活的调用。这条命令会调用_includes/filename文件。

![include](/img/include.png)

3.**_layouts**

这是模板文件存放的位置。模板需要通过YAML front matter来定义，后面会讲到。

4.**_posts**

你的动态内容，一般来说就是你的博客正文存放的文件夹。他的命名有严格的规定，必须是2012-02-22-artical-title.MARKUP这样的形式，MARKUP是你所使用标记语言的文件后缀名,可以根据你的文件名灵活调整。

5.**_site**

这个是Jekyll生成的最终的文档，没有发布站点或本地没有生成测试前不会生成，没有不用去关心。

6.**其他文件夹**

你可以创建任何的文件夹，在根目录下面也可以创建任何文件，假设你创建了project文件夹，下面有一个github-pages.md的文件，那么你就可以通过yoursite.com/project/github-pages访问的到。其中我最常用的会有**res**(里面放js，css)    **img**(放要用到的图片)

以上只是简单的描述，具体可见官方文档[jekyll]()

###**四、创建模板文件**###
进入 _layouts目录，创建default.html文件，这里我们暂且作为默认模板，以后具体我们可以创建别的模板使用

写入以下内容：
<pre><code>&lt;!DOCTYPE html&gt;
&lt;html&gt;
&lt;head&gt;
&lt;meta http-equiv="content-type" content="text/html; charset=utf-8" /&gt;
&lt;title&gt;&#123;&#123; page.title &#125;&#125;&lt;/title&gt;
&lt;/head&gt;
&lt;body&gt;
&#123;&#123; content &#125;&#125;
&lt;/body&gt;
&lt;/html&gt;
</code></pre>

Jekyll使用[Liquid模板语言](https://github.com/shopify/liquid/wiki/liquid-for-designers)，&#123;&#123; page.title &#125;&#125;表示文章标题，&#123;&#123; content &#125;&#125;表示文章内容，更多模板变量请参考[官方文档](https://github.com/mojombo/jekyll/wiki/Template-Data)。

###**五、创建文章**###
进入 _posts 目录，建第一篇文章。文章就是普通的文本文件，文件名假定为2014-11-06-hello-world.html。(注意，文件名必须为"年-月-日-文章标题.后缀名"的格式。如果网页代码采用html格式，后缀名为html；如果采用markdown格式，后缀名为md

在该文件中，填入以下内容：（注意，行首不能有空格)
<pre><code>---
layout: default
title: 你好，世界
---

　　&lt;h2&gt;&#123;&#123; page.title &#125;&#125;&lt;/h2&gt;
　　&lt;p&gt;我的第一篇文章&lt;/p&gt;
　　&lt;p&gt;&#123;&#123; page.date | date_to_string &#125;&#125;&lt;/p&gt;
</code></pre>

每篇文章的头部，必须有一个yaml文件头，用来设置一些元数据。它用三根短划线"---"，标记开始和结束，里面每一行设置一种元数据。"layout:default"，表示该文章的模板使用_layouts目录下的default.html文件；"title: 你好，世界"，表示该文章的标题是"你好，世界"，如果不设置这个值，默认使用嵌入文件名的标题，即"hello world"。

在yaml文件头后面，就是文章的正式内容，就相当于在模板default里面的&#123;&#123; content &#125;&#125;,里面可以使用模板变量。&#123;&#123; page.title &#125;&#125;就是文件头中设置的"你好，世界"，&#123;&#123; page.date &#125;&#125;则是嵌入文件名的日期（也可以在文件头重新定义date变量），  "&#124; date_to_string"表示将page.date变量转化成人类可读的格式。


###**六、更改刚才我们创建的index.html首页文件**###
把我们刚才创建的简单地显示hello world的首页改为可以列出你在_posts文件夹里所有你写的文件的页面

填入以下内容：
<pre><code>---
layout: default
title: 我的Blog
---
　&lt;h2&gt;&#123;&#123; page.title &#125;&#125;&lt;/h2&gt;
　&lt;p&gt;最新文章&lt;/p&gt;
　&lt;ul&gt;
　　　　&#123;% for post in site.posts %&#125;
　　　　　　&lt;li&gt;&#123;&#123; post.date | date_to_string &#125;&#125; &lt;a href="&#123;&#123; post.url &#125;&#125;"&gt;&#123;&#123; post.title &#125;&#125;&lt;/a&gt;&lt;/li&gt;
　　　　&#123;% endfor %&#125;
　&lt;/ul&gt;
</code></pre>

然后，首页使用了&#123;% for post in site.posts %&#125;，表示对所有帖子进行一个遍历。这里要注意的是，Liquid模板语言规定，输出内容使用两层大括号，单纯的命令使用一层大括号。

然后项目目录结构变成了这样：

    /ridy
        |--　index.html
        |--　_config.yml
        |--　_layouts
        |　　　|--　default.html 
        |--　_posts
        |　　　|--　2014-11-06-hello-world.html

###**七、搭建本地jekyll环境，以测试**###
经过上述步骤不出意外的话，可以直接跳到下一步上传到github/gitcafe上，等一会儿就能看到内容。

但是假如项目做大，加上css，js，加上各类第三方插件，完善丰富站点后，测试debug就显得很重要。

首先安装jekyll环境，参考官方的[jekyll安装](http://jekyllrb.com/docs/installation/)，安装成功后，

    $cd ridy.github.io
    $jekyll serve /*目前只能到这个指令*/

然后能看到测试成功的提示

![jekyll](/img/jekyll.png)

然后访问0.0.0.0:4000


这里我遇到过一个问题，发现本地测试时，出现乱码。反而上传到github上正常，理论上不应该这样。

后经了解，github解析你的网站会指定字符集，某种神秘的加成！

所以是本地的问题，发现是自己的浏览器的编码选择问题，[chrome浏览器编码修改图文教程](http://jingyan.baidu.com/article/d169e186a4e826436611d81e.html)

改为utf-8，一生受用！

###**八、发布到github**###
本地测试无误，激动人心的时刻终于要到了。
    
    $cd ridy.github.io
    $git add .
    $git commit -m "second post"
    $git push origin master

成功后不用等10分钟，就能看到最基本的博客功能。

![blog](/img/blog.png)

![article](/img/article.png)
----------------------------------------------------------------------

##**gitcafe**##
据说[gitcafe](https://gitcafe.com/)同样支持jekyll，而且速度更快(等下我们做个测试)，赶紧上去看看

[gitcafe-pages官方文档](https://gitcafe.com/GitCafe/Help/wiki/Pages-%E7%9B%B8%E5%85%B3%E5%B8%AE%E5%8A%A9)

基本搭建步骤跟上面的实例是相同的，只是在创建项目仓库的时候，项目名==**usernanme** (这一点要比github更简洁一些)

![gitcafe项目创建](/img/gitcafepages.png)

注意到上面，输入项目名后，gitcafe自动识别你想使用page服务，项目描述和项目主页地址都自动填充了。

可以把刚刚创建的github项目里的文件全部cp到现在创建的项目仓库下

根据官方文档，还有一点差异是创建一个gitcafe-pages的分支，并切换到该分支

所以在push的时候：
    
    $git checkout -b gitcafe-pages
    $git push origin gitcafe-pages 

这样就成功上传了。访问主页 [ridy.gitcafe.com](http://ridy.gitcafe.com)


##**继续丰富！**##

购买独立域名！

加入评论功能!

加入代码高亮!

加入首页文章概述!

sidebar,css,js,footer,share,balabalabala...

上参考资料   -->

--------------------------------------------------

##**参考资料**##

+ [使用Github Pages建独立博客](http://beiyuu.com/github-pages/)
+ [搭建一个免费的，无限流量的Blog----github Pages和Jekyll入门](http://www.ruanyifeng.com/blog/2012/08/blogging_with_jekyll.html)
+ [Jekyll搭建的blog,github源码](https://github.com/mojombo/jekyll/wiki/Sites)