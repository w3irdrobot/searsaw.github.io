---
id: 222
title: Rendering HTML as PHP
author: Alex Sears
layout: post
guid: http://alexsears.com/?p=222
permalink: /article/rendering-html-as-php
dsq_thread_id:
  - 1673616094
categories:
  - Article
tags:
  - .htaccess
  - HTML
  - PHP
---
Recently, I worked on a project for a customer. They were using a PHP `include()` to render some PHP inside an HTML file. What the hell?!

After some dedicated Googling, I found out that it&#8217;s possible to run an HTML file through the PHP parser to make sure that any PHP in them in rendered. It requires a line added to an `.htaccess` file.

<!--more-->

{% highlight bash %}
AddType application/x-httpd-php .html
{% endhighlight %}

If this doesn&#8217;t work on your particular server, try the code below instead:

{% highlight bash %}
AddType application/x-httpd-php5 .html
{% endhighlight %}

This simple line will run all your HTML files through the PHP parser which will allow you to use any PHP code in them that you want. **HOWEVER**, I highly discourage the use of this! If you are going to use PHP in a file, give it a PHP extension not HTML. The only reason this should be used is when inheriting code from another developer, and it will cost too much to change all the extensions to PHP. It&#8217;s a quick and dirty but effective fix to a problem that shouldn&#8217;t exist.

Hopefully you will never have to use this trick. However, if you do, hopefully you are a good enough developer to realize why this is a bad practice.

## Further Reading

  * [Execute PHP from HTML][1]

 [1]: http://php.about.com/od/advancedphp/p/html_php.htm