---
id: 294
title: Recompiling PHP on OS X 10.9 Mavericks
author: Alex Sears
layout: post
guid: http://alexsears.com/?p=294
permalink: /article/recompiling-php-os-x-10-9-mavericks
dsq_thread_id:
  - 1891045926
categories:
  - Article
tags:
  - Linux
  - PHP
---
Yesterday, I updated my computer to the new release of the amazing OS X operating system from Apple, code-named Mavericks. Everything went smooth, but then I realized it wiped my PHP installation away and gave me the default Mavericks installation, PHP 5.4.17. Being a web developer, I like to have the most recent version of PHP whenever I can. So naturally I decided to recompile it. I have done this many times before after upgrades, so this wasn&#8217;t anything new to me.

<!--more-->

I use the well-written article [Upgrading the Native PHP Installation on OS X Mountain Lion][1] by Bruno Skvorc. This explains the whole compiling process.

However, I started to run into a ton of issues when configuring the binary from [the PHP website][2]. Can&#8217;t find zlib? Can&#8217;t find openssl? Where is all my stuff?!?!

It turns out that Mavericks moved the command line tools out of XCode into somewhere else. Therefore, I no longer had a `/usr/include` file which contains many of the headers I needed to include these things. However, this raises a new issue. How do I install the CLTs? I found an article titled <a href"http://www.computersnyou.com/2025/2013/06/install-command-line-tools-in-osx-10-9-mavericks-how-to/">Install Command Line Tools In OSX 10.9 Mavericks [ How &#8211; To ]</a> that explains you must run a command in the terminal. So, open a terminal and run:

{% highlight bash %}
xcode-select --install
{% endhighlight %}

This will install the CLTs for you and create the `/usr/include` directory you need to compile correctly. **NOTE: I had to run this twice to get it to work right. It didn&#8217;t create the directory the first time.**

There was only one error left after I did this. It said I didn&#8217;t have PostgreSQL. I used [Homebrew](http://brew.sh/) to install that for me:

{% highlight bash %}
brew install postgresql
{% endhighlight %}

I hope this saves you the hours I wasted on this.

## Update

I also had issue when using `make test`. It couldn&#8217;t find my `iodbcext.h` file. I had to edit my configure statement to have `--with-unixODBC=/usr/local` and make sure I have unixODBC installed through Homebrew:

{% highlight bash %}
brew install unixodbc
{% endhighlight %}

 [1]: http://mac.tutsplus.com/tutorials/server/upgrading-the-native-php-installation-on-os-x-mountain-lion/
 [2]: http://www.php.net