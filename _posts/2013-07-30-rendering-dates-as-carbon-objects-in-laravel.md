---
id: 229
title: Rendering Dates as Carbon objects in Laravel
author: Alex Sears
layout: post
guid: http://alexsears.com/?p=229
permalink: /article/rendering-dates-as-carbon-objects-in-laravel
dsq_thread_id:
  - 1562453261
categories:
  - Article
tags:
  - Carbon
  - Laravel
  - MVC
  - PHP
---
Working with dates in any language can be difficult. PHP is not any different. Though the `DateTime` class has tons of built in functionality, it still leaves some features to be desired. Thankfully, [Brian Nesbitt][1] has created the [Carbon class][2]. This is an extension to the basic PHP `DateTime` class.

<!--more-->

Laravel uses this Carbon class in its framework. By default, the timestamps that are automatically added to any table you make are retrieved from the database as Carbon objects. However, what if you have another `date` column on there and you want it returned as a Carbon object as well.

After some digging, I found that the Eloquent base model has a method for controlling what dates are returned as Carbon objects. The method is below.

{% highlight php linenos %}
<?php
// vendor/laravel/framework/src/illuminate/Database/Eloquent/Model.php

/**
 * Get the attributes that should be converted to dates.
 *
 * @return array
 */
public function getDates()
{
    return array(static::CREATED_AT, static::UPDATED_AT, static::DELETED_AT);
}
{% endhighlight %}

This means that you can overwrite this method in your model classes and define which dates are returned as Carbon objects. If you just want to add a date column to this method, implement it something like the below code.

{% highlight php linenos %}
<?php
/**
 * Get the attributes that should be converted to dates.
 *
 * @return array
 */
public function getDates()
{
    return array_merge(parent::getDates(), array('purchase_date'));
}
{% endhighlight %}

Now whenever you retrieve the `purchase_date` from the database, you will be able to use the awesome methods that come from using the Carbon object. That was easy!

## Update (September 26, 2013)

As of September 6, 2013, [Taylor has merged][3] some code into the Laravel framework that makes working with dates even easier! Instead of overriding the `getDates()` function, any extra date properties can now be added to a `$dates` array on the Eloquent model. These date properties will be automagically transformed into Carbon objects. Here is an example:

{% highlight php linenos %}
<?php

class User extends Eloquent {

    protected $dates = array('purchase_date');

}
{% endhighlight %}

Now the *purchase_date* will be a Carbon object, which can then be manipulated easily!

## Further Reading

  * [GitHub laravel/framework issue #1325][4]

 [1]: https://github.com/briannesbitt
 [2]: https://github.com/briannesbitt/Carbon
 [3]: https://github.com/laravel/framework/commit/4265d03fda4741b9363c7465480a94094b8a944f#diff-d8e24a5815a26f7211e66db787be647f
 [4]: https://github.com/laravel/framework/issues/1325