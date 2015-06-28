---
id: 303
title: Using Entrust to Add Roles and Permissions to Laravel 4
author: Alex Sears
layout: post
guid: http://alexsears.com/?p=303
permalink: /article/using-entrust-to-add-roles-and-permissions-to-laravel-4
dsq_thread_id:
  - 1943444115
categories:
  - Article
tags:
  - Entrust
  - Laravel
  - Permissions
  - PHP
  - Roles
---
Not long ago, I wrote a post about [Adding Roles to Laravel Users][1]. I like my custom solution, but I figured there had to be a better solution out there. Honestly, I should have done some looking for information before I built it. Anyways, I stumbled upon a package called [Entrust][2] written by [Zizaco][3].

<!--more-->

This package is exactly what I was looking for! It can automatically add roles and permissions to your project. This post will walk you through how to set it up in your project and how to use some of its features.

## Installing Entrust

Thanks to [Composer][4], we can easily add this Entrust to our project. Using the terminal, navigate to the root directory of your project. Run the following commands:

{% highlight bash %}
composer require zizaco/entrust:dev-master --no-update
composer update zizaco/entrust
{% endhighlight %}

This will add the package to your project and then update it (bring the files into your vendor folder).

Next, we have to add the service provider and alias to our app.php file. Let&#8217;s do that now!

{% highlight php %}
<?php
'providers' => array(

    'Illuminate\Foundation\Providers\ArtisanServiceProvider',
    'Illuminate\Auth\AuthServiceProvider',
    ...
    'Zizaco\Entrust\EntrustServiceProvider',

),
...
'aliases' => array(

    'App'        => 'Illuminate\Support\Facades\App',
    'Artisan'    => 'Illuminate\Support\Facades\Artisan',
    ...
    'Entrust'    => 'Zizaco\Entrust\EntrustFacade',

),
{% endhighlight %}

## Migrating the database

Entrust ships with migrations for the four tables in the database it uses to keep track of roles and permissions attached to users. We can easily export it using a single command. Execute the following command in the terminal.

{% highlight bash %}
php artisan entrust:migration
{% endhighlight %}

This will create a new migration in the migrations directory. If you open this up, you can change anything about the database you want before you migrate it. All the defaults are okay. One thing I will recommend to do is to delete the permissions column on the roles table. It&#8217;s possible it is gone now. They recently removed it. It showed up on one project of mine but not on the other. Just a warning. To migrate the tables, use the migrate command.

{% highlight bash %}
php artisan migrate
{% endhighlight %}

Now we are ready to setup our models!

## Setting up the models

To utilize the power of Entrust, we also need to setup our User, Role, and Permission models.

Create the following two models and put them in the /app/models directory.

{% highlight php %}
<?php

use Zizaco\Entrust\EntrustRole;

class Role extends EntrustRole {}
{% endhighlight %}

{% highlight php %}
<?php

use Zizaco\Entrust\EntrustPermission;

class Permission extends EntrustPermission {}
{% endhighlight %}

These are the two models that represent our roles and permissions that a user can have. By extending the ones shipped with Entrust, we can bring all the functionality of the default EntrustRole and EntrustPermission into our project. This also gives us a place to extend the functionality of each if we choose to do so. These models extend the Ardent class which in turn extend the Eloquent class. This gives us all the great features of the default Laravel model too.

The last thing we need to do is add more functionality to our User model. Laravel ships with a default User model. We will reuse this model and add the [trait][5] `HasRole` to the User model too.

{% highlight php %}
<?php

use Illuminate\Auth\UserInterface;
use Illuminate\Auth\Reminders\RemindableInterface;
use Zizaco\Entrust\HasRole;

class User extends Eloquent implements UserInterface, RemindableInterface {
    use HasRole;
...
{% endhighlight %}

Now our models are setup!

## Attaching roles and permissions

Entrust gives us some functions we can call on an instantiated User object to add roles to our database, add permissions to those roles, and then connect those roles to users. Here is some code that does just that.

{% highlight php %}
<?php
Route::get('/start', function()
{
    $subscriber = new Role();
    $subscriber->name = 'Subscriber';
    $subscriber->save();

    $author = new Role();
    $author->name = 'Author';
    $author->save();

    $read = new Permission();
    $read->name = 'can_read';
    $read->display_name = 'Can Read Posts';
    $read->save();

    $edit = new Permission();
    $edit->name = 'can_edit';
    $edit->display_name = 'Can Edit Posts';
    $edit->save();

    $subscriber->attachPermission($read);
    $author->attachPermission($read);
    $author->attachPermission($edit);

    $user1 = User::find(1);
    $user2 = User::find(2);

    $user1->attachRole($subscriber);
    $user2->attachRole($author);

    return 'Woohoo!';
});
{% endhighlight %}

Wow! That&#8217;s alot. I&#8217;ll quickly explain what&#8217;s going on. First, I create two roles and save them to the database. Next, I create two permissions and save them to the database too. Now here&#8217;s where Entrust really shines. We can use the `attachPermission` method to associate a permission with a role by passing in the Permission object. We can also do the same thing with roles. Above, we get two users out of the database and attach one of the roles we created to each by using the `attachRole` method.

## Using Entrust

Now let&#8217;s see how we can use Entrust to protect parts of our site.

Let&#8217;s say we have a super secret message we only want to display to authors. We can setup a route to display the message but only to people who are authorized. We can test for a specific role by using the `hasRole` method on a User object. Let&#8217;s test this out.

{% highlight php %}
<?php
Route::get('/secret', function()
{
    Auth::loginUsingId(1);

    $user = Auth::user();

    if ($user->hasRole('Author'))
    {
        return 'Redheads party the hardest!';
    }

    return 'Many people like to party.';
});
{% endhighlight %}

This code sets up a route that will automatically log the user in as the user with ID of one, which we setup earlier as the subscriber. Since it doesn&#8217;t have the author role, `hasRole` returns false. Therefore, we don&#8217;t get to see the &#8220;secret.&#8221; However, if we change the code above so that we are logged in as user number two, the secret will be displayed in the browser!

{% highlight php %}
<?php
Route::get('/secret', function()
{
    Auth::loginUsingId(2);

    $user = Auth::user();

    if ($user->hasRole('Author'))
    {
        return 'Redheads party the hardest!';
    }

    return 'Many people like to party.';
});
{% endhighlight %}

You can also test for permissions by using the `can` method. All these methods can be used in route closures, controllers, views, and filters. Let&#8217;s make a filter that makes sure the user is an author or has the ability to edit posts. If not, they should be sent back to the homepage. This is kind of a silly example given the roles and permissions we have now, but it could be extremely useful on a big site.

{% highlight php %}
<?php
Route::filter('editable', function()
{
    $user = Auth::user();

    if (!$user->ability(['Author'], ['can_edit']))
    {
        return Redirect::home();
    }
});
{% endhighlight %}

The `ability` method allows us to customize a check on a user. First, it takes an array of roles the user must have. Next, it takes an array of permissions that must match. The third parameter is an array of other options. I won&#8217;t go into that but more information can be found on GitHub. Here is some code you can use to test it. Use the route we created earlier to log in as different users.

{% highlight php %}
<?php
Route::get('/', ['as'=> 'home', function()
{
    return View::make('hello');
}]);

Route::get('/awesome', ['before' => ['auth', 'editable'], function()
{
    return 'You made it!';
}]);
{% endhighlight %}

Also, the filters we create can be used in our controllers to filter out users instead of putting that logic in `routes.php`. For more information on using filters in controllers, read my blog post [Laravel 4 Filters in Controllers][6].

As you can see, the possibilities are endless when using Entrust to help secure your site. I recommend playing around with the different functions, reading the source code, and visiting the GitHub page for the Entrust package, the link for which can be found below. Hopefully this helps you improve the security without tearing your hair out. Please comment below if you have any questions or know of a better way to do things I suggested. I&#8217;m always ready to learn more!

## Further Reading

  * [Entrust on GitHub][2]
  * [Group vs. Role &#8211; StackOverflow][7]

 [1]: http://alexsears.com/article/adding-roles-to-laravel-users
 [2]: https://github.com/Zizaco/entrust
 [3]: https://github.com/Zizaco
 [4]: http://getcomposer.org/
 [5]: http://php.net/manual/en/language.oop5.traits.php
 [6]: http://alexsears.com/article/laravel-4-filters-controllers
 [7]: http://stackoverflow.com/questions/7770728/group-vs-role-any-real-difference