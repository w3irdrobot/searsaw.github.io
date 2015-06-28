---
id: 256
title: Laravel 4 Filters in Controllers
author: Alex Sears
layout: post
guid: http://alexsears.com/?p=256
permalink: /article/laravel-4-filters-controllers
dsq_thread_id:
  - 1774670350
categories:
  - Article
tags:
  - Controllers
  - Filters
  - Laravel
  - MVC
  - PHP
---
[Filtering in Laravel 4][1] is extremely easy. In case you don&#8217;t know what I&#8217;m talking about, a filter in Laravel is used on a particular route or set of routes to prevent certain people or actions from happening on a particular route. Laravel ships with a few simple but powerful filters to get you started.

<!--more-->

These include a filter to make sure a user is logged in and a one for protecting against [CSRF attacks][2].

Typically, you would add these filters in your `routes.php` or `filters.php`. However, I feel that separating this code into another file away from the controller that it is protecting is cumbersome. I would rather put these filters in my controller so I can see at a glance what filters I have on certain routes. This can easily be done through the constructor of the controller. Let&#8217;s check it out.

Let&#8217;s pretend we have a [resource controller][3] that has a store method for storing a post into the database. We want to make sure this route is protected from CSRF attacks and should only be accessed by AJAX. This means we will need a filter for CSRF and for AJAX. The CSRF one comes with Laravel. Let&#8217;s build one for AJAX. Insert the following code into `routes.php`:

{% highlight php %}
<?php
Route::filter('ajax', function()
{
    if (!Request::ajax()) App::abort(404);
});
{% endhighlight %}

Then we need to add these two filters to our controller through its constructor.

{% highlight php %}
<?php
class PostController extends BaseController {

    public function __construct()
    {
        // Exit if not ajax
        $this->beforeFilter('ajax', array('only' => 'store'));
        // Exit if not a valid _token
        $this->beforeFilter('csrf', array('only' => 'store'));
    }

    public function store()
    {
        // Store like a boss
    }

}
{% endhighlight %}

This will now execute those two filters every time the `store()` method is called. If you wanted to make sure these filters were used on every call to this controller, then just omit the array passed as the second parameter to the `beforeFilter()` method.

That&#8217;s it! As you can see, putting your filters in your controllers instead of in different files allows you to keep all logic in your controllers.

## Further Reading

  * [How to add filter parameters to controllers in Laravel?][4]

 [1]: http://laravel.com/docs/routing#route-filters
 [2]: http://en.wikipedia.org/wiki/Cross-site_request_forgery
 [3]: http://laravel.com/docs/controllers#resource-controllers
 [4]: http://stackoverflow.com/questions/13188040/how-to-add-filter-parameters-to-controllers-in-laravel