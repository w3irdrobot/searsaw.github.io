---
id: 211
title: Explode Names in PHP
author: Alex Sears
layout: post
guid: http://alexsears.com/?p=211
permalink: /article/explode-names-in-php
dsq_thread_id:
  - 1509871586
categories:
  - Article
tags:
  - PHP
---
Sometimes, a poorly designed form will only have one text box for a user&#8217;s whole name (first and last). Normally, though, we want to store it as first name and last name in a database for search purposes. Here is a quick way to split the value of this text input up and to check that it was enter correctly at the same time.

<!--more-->

{% highlight php linenos %}
<?php
if (!strpos($_POST['full_name'], ' ') return false;

$names = explode(' ', $_POST['full_name'], 2);
$first_name = $names[0];
$last_name = $names[1];
?>
{% endhighlight %}

This `explode` function will essentially split some input on the first blank space. The array it returns holds the first name and then everything else. So if they entered the middle name with it, then the `$names[1]` will hold the middle and last name.

# Further Reading

  * [PHP&#8217;s `explode` function][1]

 [1]: http://php.net/manual/en/function.explode.php