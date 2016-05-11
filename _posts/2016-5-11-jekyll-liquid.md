---
layout: post
title: jekyll搭建博客解决模板语言冲突
category: jekyll
---

发现越来越多的人使用github page搭建博客

然而我的使用的时候发现一个问题

{% raw %}
因为经常要写的博文涉及{{ xxx }}这样的模板语言，会与Jekyll/Liquid模板语法冲突，找了一会儿，找到了解决方案


	临时禁止执行 Jekyll Tag 命令，在生成的内容里存在冲突的语法片段的情况下很有用。

	{% raw % }
	  In Handlebars, {{ this }} will be HTML-escaped, but {{{ that }}} will not.
	{% endraw % }

{% endraw %}
[https://alfred-sun.github.io/blog/2015/01/10/jekyll-liquid-syntax-documentation/](https://alfred-sun.github.io/blog/2015/01/10/jekyll-liquid-syntax-documentation/)