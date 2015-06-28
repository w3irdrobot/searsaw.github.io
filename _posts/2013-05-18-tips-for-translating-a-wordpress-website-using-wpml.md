---
id: 16
title: Tips for Translating a WordPress Website, Part One
author: Alex Sears
layout: post
guid: http://alexsears.com/?p=16
permalink: /article/tips-for-translating-a-wordpress-website-using-wpml
dsq_thread_id:
  - 1297400371
categories:
  - Article
tags:
  - PHP
  - Translation
  - Wordpress
  - WPML
---
Recently, I was given the task of translating a WordPress website into Chinese. I knew there were some great plugins out there for accomplishing this. Luckily, I was told that I would be using the one I consider to be the best out there-the WordPress Multilingual Plugin.

<!--more-->

Called WPML for short, this plugin can scan your website for strings that need to be translated and present them in an easy to read table to be manually translated or quickly sent off to a translator to do the work for you. It also enables a user to create a post and then have it sent off to a translator to be translated.

This transition to a new language turned out to be more difficult than I anticipated, however, since the theme I was using was not optimized for a translation service such as this one. Most strings were not wrapped in the localization functions that WordPress natively provides, and many links in the theme were hardcoded into the scripts since this theme was made specifically for that company.

In this series, I will discuss some tips and tricks to overcome these issues and more. These are things to keep in mind when you are translating a site using WPML or creating a new theme that may be translated in the future. This first issue will discuss some simple things to keep in mind with a new installation of WordPress.

## Finding the ID of a page

This isn&#8217;t a technique that is specific to translating a website in WordPress. It has uses outside of translation. However, it will be very important to know how to do for some of the tips later in this article. I can&#8217;t take credit for this tip, since it was actually pointed out to me by a colleague, Allen Gingrich.

To easily find the ID of a page, go to the page screen in your WordPress admin. If you find the page you are looking for and hover over it, the browser will display the URL of that page in the bottom left hand corner of your screen. In that URL is a place that looks like `post=5`. This means the id of the page is 5. It&#8217;s that easy!

{% include image_caption.html imageurl="/assets/wp-content/uploads/2013/05/GettingIDofPage-1024x560.png" description="Get the ID from the bottom left of the page." title="Getting ID of Page" %}

## All Database Tables must be UTF-8

This is pretty self-explanatory. When you create a new WordPress site, make sure that the character set of your databases is UTF-8. UTF-8 can store characters that are not in the latin alphabet, such as Chinese and Japanese characters. Therefore, if it is not UTF-8 and you try to save a translation of a post into Chinese, it will not store the characters. It will give you a bunch of funky output.

If you&#8217;re reading this post and want to know how to change all your original WordPress databases into the UTF-8 character set, then below you will find some code that you can copy and paste into the terminal or into the SQL screen in phpMyAdmin. Keep in mind this code expects your database names to be the default names that WordPress gives them. If you changed the names for some reason, then you will have to modify the code slightly to get it to work.

{% highlight sql %}
ALTER TABLE wp_commentmeta CONVERT TO CHARACTER SET utf8;
ALTER TABLE wp_comments CONVERT TO CHARACTER SET utf8;
ALTER TABLE wp_links CONVERT TO CHARACTER SET utf8;
ALTER TABLE wp_options CONVERT TO CHARACTER SET utf8;
ALTER TABLE wp_postmeta CONVERT TO CHARACTER SET utf8;
ALTER TABLE wp_posts CONVERT TO CHARACTER SET utf8;
ALTER TABLE wp_terms CONVERT TO CHARACTER SET utf8;
ALTER TABLE wp_term_relationships CONVERT TO CHARACTER SET utf8;
ALTER TABLE wp_term_taxonomy CONVERT TO CHARACTER SET utf8;
ALTER TABLE wp_usermeta CONVERT TO CHARACTER SET utf8;
ALTER TABLE wp_users CONVERT TO CHARACTER SET utf8;
{% endhighlight %}

## Wrapping Up

As you can see, there are some things that need to be considered when translating a webpage. Keeping these in mind as you create a theme will also save you and other users lots of time when translating the website becomes necessary. More tips are in store. So stay tuned!

## Further Reading

  * [Ideas and Pixels &#8211; Blog][1]
  * [ALTER TABLE Syntax &#8211; MySQL][2]

 [1]: http://ideasandpixels.com/blog
 [2]: http://dev.mysql.com/doc/refman/5.1/en/alter-table.html