---
id: 285
title: Using View Composers in Laravel 4
author: Alex Sears
layout: post
guid: http://alexsears.com/?p=285
permalink: /article/using-view-composers-laravel-4
dsq_thread_id:
  - 1861645455
categories:
  - Article
tags:
  - Laravel
  - PHP
  - Views
---
Sometimes a view will need a variable every single time it is loaded. Perhaps we are developing a system for tracking users, and we want to display the number of users registered in our system next to a button to display the list of users. If we have a resource controller for our User model, we would have to pass a count of users on every method in that controller that displays a view, which is every single one! That isn&#8217;t very [DRY][1]!

<!--more-->

Luckily, Laravel ships with a way to include this variable in every call to a particular view. We do this through the use of [view composers][2].

Let&#8217;s see how we might implement the behavior we outlined above.

{% highlight php %}
<?php
View::composer('layouts.user', function($view)
{
    $view->with('num_of_users', User::getUserCount());
});
{% endhighlight %}

Whenever the &#8220;layouts.user&#8221; view is loaded, the `getUserCount()` function on the `User` model will be fired, and the result is stored in the `$num_of_users` variable. This variable will now be available in the &#8220;layouts.user&#8221; view. This lets us use the following code with confidence.

{% highlight php %}
link_to_route('user.index', 'All Users: ' . $num_of_users)
{% endhighlight %}

Damn, that was easy! Thanks, Laravel!

## Further Reading

  * [View Composers &#8211; Laravel documentation][2]
  * [Difference Between `View::share()` and `View::composer()`][3]
  * [Laravel 4 Filters and View Composers][4]

 [1]: http://en.wikipedia.org/wiki/Don't_repeat_yourself
 [2]: http://laravel.com/docs/responses#view-composers
 [3]: http://stackoverflow.com/questions/18315831/laravel-difference-between-viewshare-and-viewcomposer
 [4]: http://stackoverflow.com/questions/18069276/laravel-4-filters-and-view-composers