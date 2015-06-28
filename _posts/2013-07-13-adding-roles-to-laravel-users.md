---
id: 186
title: Adding Roles to Laravel Users
author: Alex Sears
layout: post
guid: http://alexsears.com/?p=186
permalink: /article/adding-roles-to-laravel-users
dsq_thread_id:
  - 1498807373
categories:
  - Article
tags:
  - Laravel
  - MVC
  - PHP
  - Roles
---
The one thing I love about [Symfony2][1] over [Laravel][2] is the built in system Symfony has for creating roles. For those of you who don&#8217;t understand what I mean by roles, roles allow a developer to bar parts of an application off from users who don&#8217;t need access.

<!--more-->

A good example of this is [WordPress&#8217; Roles and Capabilities][3]. Adding roles to Laravel is a necessity for most applications. Below is my implementation for a project I worked on recently.

# The Database

First, we have to create the database tables to use. [Migrations to the rescue!][4] We need three tables: users, roles, and user\_roles. The user\_roles is a &#8220;pivot table&#8221; for accessing the many-to-many relationship between a user and its roles.

## Users Table

{% highlight php linenos %}
<?php

use Illuminate\Database\Schema\Blueprint;
use Illuminate\Database\Migrations\Migration;

class CreateUsersTable extends Migration
{

    /**
     * Run the migrations.
     *
     * @return void
     */
    public function up()
    {
        Schema::create(
            'users',
            function (Blueprint $table) {
                $table->increments('id');
                $table->string('email', 50);
                $table->string('password', 60);
                $table->string('first_name', 50);
                $table->string('last_name', 50);
                $table->string('city', 100)->nullable();
                $table->string('state', 2)->nullable();
                $table->string('phone', 12)->nullable();
                $table->integer('points')->nullable();
                $table->timestamps();
            }
        );
    }

    /**
     * Reverse the migrations.
     *
     * @return void
     */
    public function down()
    {
        Schema::drop('users');
    }

}
{% endhighlight %}

## Roles Table

{% highlight php linenos %}
<?php

use Illuminate\Database\Schema\Blueprint;
use Illuminate\Database\Migrations\Migration;

class CreateRolesTable extends Migration
{

    /**
     * Run the migrations.
     *
     * @return void
     */
    public function up()
    {
        Schema::create(
            'roles',
            function (Blueprint $table) {
                $table->increments('id');
                $table->string('name');
            }
        );
    }

    /**
     * Reverse the migrations.
     *
     * @return void
     */
    public function down()
    {
        Schema::drop('roles');
    }

}
{% endhighlight %}

## User_Roles Table

{% highlight php linenos %}
<?php

use Illuminate\Database\Schema\Blueprint;
use Illuminate\Database\Migrations\Migration;

class CreateUsersRolesTable extends Migration
{

    /**
     * Run the migrations.
     *
     * @return void
     */
    public function up()
    {
        Schema::create(
            'users_roles',
            function (Blueprint $table) {
                $table->integer('user_id');
                $table->integer('role_id');
            }
        );
    }

    /**
     * Reverse the migrations.
     *
     * @return void
     */
    public function down()
    {
        Schema::drop('users_roles');
    }

}
{% endhighlight %}

# The Models

Now we need to add some code in our models so Laravel understands the relationship between a user and its roles. We will also add a couple functions to make interacting with a user&#8217;s roles super easy!

## The User

{% highlight php linenos %}
<?php
use Illuminate\Auth\UserInterface;
use Illuminate\Auth\Reminders\RemindableInterface;

class User extends BaseModel implements UserInterface, RemindableInterface
{
    /**
     * Get the roles a user has
     */
    public function roles()
    {
        return $this->belongsToMany('Role', 'users_roles');
    }

    /**
     * Find out if User is an employee, based on if has any roles
     *
     * @return boolean
     */
    public function isEmployee()
    {
        $roles = $this->roles->toArray();
        return !empty($roles);
    }

    /**
     * Find out if user has a specific role
     *
     * $return boolean
     */
    public function hasRole($check)
    {
        return in_array($check, array_fetch($this->roles->toArray(), 'name'));
    }

    /**
     * Get key in array with corresponding value
     *
     * @return int
     */
    private function getIdInArray($array, $term)
    {
        foreach ($array as $key => $value) {
            if ($value == $term) {
                return $key;
            }
        }

        throw new UnexpectedValueException;
    }

    /**
     * Add roles to user to make them a concierge
     */
    public function makeEmployee($title)
    {
        $assigned_roles = array();

        $roles = array_fetch(Role::all()->toArray(), 'name');

        switch ($title) {
            case 'super_admin':
                $assigned_roles[] = $this->getIdInArray($roles, 'edit_customer');
                $assigned_roles[] = $this->getIdInArray($roles, 'delete_customer');
            case 'admin':
                $assigned_roles[] = $this->getIdInArray($roles, 'create_customer');
            case 'concierge':
                $assigned_roles[] = $this->getIdInArray($roles, 'add_points');
                $assigned_roles[] = $this->getIdInArray($roles, 'redeem_points');
                break;
            default:
                throw new \Exception("The employee status entered does not exist");
        }

        $this->roles()->attach($assigned_roles);
    }
}
{% endhighlight %}

First, we have a function that determines whether someone is an &#8220;employee&#8221; (the application this was made for determined the name). Next is a function that when supplied with a role, it will determine if the user has that particular capability (or role). Then we have a helper function that will return the id number of the value that is supplied. This is used in our last function. This function allows you to pass the &#8220;title&#8221; the person should have. Each title corresponds with a certain group of permissions, each building on each other. Therefore, the title with the most permissions is at the top of the switch statement. This function will need to be edited depending on the roles your application requires.

## The Role

{% highlight php linenos %}
<?php

class Role extends Eloquent
{
    /**
     * Set timestamps off
     */
    public $timestamps = false;

    /**
     * Get users with a certain role
     */
    public function users()
    {
        return $this->belongsToMany('User', 'users_roles');
    }
}
{% endhighlight %}

# How to Use Roles

If you wanted to check if a user should have access to a specific area, you can write something like this.

{% highlight php linenos %}
<?php if ( $user->hasRole('edit_customer') ) /* Do something epic! */; ?>
{% endhighlight %}

Say you wanted to create a new user and then give them admin access, you could write something like the following code.

{% highlight php linenos %}
<?php
$user = User::create(
            array(
                 'first_name' => 'Alex',
                 'last_name'  => 'Sears',
                 'password' => 'Cincinnati',
                 'email' => 'alexsears@gmail.com',
                 'city' => 'Cincinnati',
                 'state' => 'OH'
            ));
        $user->makeEmployee('admin');
?>
{% endhighlight %}

Lastly, we can check if a user is an employee (has some roles) by calling the `isEmployee` function on the user.

{% highlight php linenos %}
<?php if ( $user->isEmployee() ) /* Do something even more epic! /*; ?>
{% endhighlight %}

# Wrapping Up

I hope this helps anyone who is having troubles with roles in Laravel finds this helpful. I&#8217;m hoping to refine this technique and make it available as a package. As always, let me know what you think. Post any comments below!

# Further Reading

  * [Laravel&#8217;s Eloquent ORM][5]
  * [Laravel Helper Functions][6]

 [1]: http://symfony.com/
 [2]: http://laravel.com/
 [3]: http://codex.wordpress.org/Roles_and_Capabilities
 [4]: http://laravel.com/docs/migrations#creating-migrations
 [5]: http://laravel.com/docs/eloquent
 [6]: http://laravel.com/docs/helpers