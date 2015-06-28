---
id: 291
title: Testing Redirect::back() in Laravel
author: Alex Sears
layout: post
guid: http://alexsears.com/?p=291
permalink: /article/testing-redirectback-laravel
dsq_thread_id:
  - 1873240849
categories:
  - Article
tags:
  - Laravel
  - PHP
  - PHPUnit
  - Testing
---
For some redirect functions in Laravel, such as `Redirect::back()`, a URL must be passed in your headers so the framework knows what URL to redirect back to. When testing routes that only redirect back to the previous URL, this can be confusing since there is no previous URL. It must be sent manually.<!--more-->

When using the `$this->call()` function in your tests, you can supply more arguments than just the method and URL. [The Laravel docs explain this function in some detail.][1] We must send an array of headers where the docs designate the `$server` parameter. To send a URL, you must pass an `HTTP_REFERER` like so:

{% highlight php %}
<?php
$this->call('GET', '/event/rsvp?event_id=1', [], [], ['HTTP_REFERER' => 'http://alexsears.com']);
{% endhighlight %}

This will hit the URL at `/event/rsvp?event_id=1`. In that route, I use `Redirect::back()` to go back to the previous URL, which I have sent to the route as my fifth parameter. It will redirect back to my blog. It&#8217;s simple!

## Further Reading

  * [Calling Routes from Tests &#8211; Laravel Docs][1]

 [1]: http://laravel.com/docs/testing#calling-routes-from-tests