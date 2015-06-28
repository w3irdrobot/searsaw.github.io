---
id: 324
title: Using Magic Methods in PHP
author: Alex Sears
layout: post
guid: http://alexsears.com/?p=324
permalink: /article/using-magic-methods-php
dsq_thread_id:
  - 2002112142
categories:
  - Article
tags:
  - Magic Methods
  - OOP
  - PHP
---
When creating a class, it is necessary to not only think about the functionality of the class but also how a developer will interact with that class. Making a class API easy to use is well worth the effort. Luckily, PHP ships with [quite a few magic methods][1] that can make creating a class API much easier to do. In this article, I will be talking about three of them &#8212; `__get`, `__set`, and `__toString`.

<!--more-->

## Setting up our class

Let&#8217;s setup a test class that we can play with to demonstrate these methods. It will be a simple Person class that has a name and age as attributes. Check out the code below!

{% highlight php %}
<?php

class Person {
    protected $name;
    protected $age;

    public function __construct($name, $age)
    {
        $this->name = $name;
        $this->age = $age;
    }

    public function getName()
    {
        return $this->name;
    }

    public function getAge()
    {
        return $this->age;
    }

    public function setName($name)
    {
        $this->name = $name;
    }

    public function setAge($age)
    {
        $this->age = $age;
    }
}

$person = new Person('Anakin Skywalker', 12);
echo 'My name is ' . $person->getName() . ', and I am ' . $person->getAge() . ' years old.<br>';
$person->setAge(25);
echo 'My name is ' . $person->getName() . ', and I am ' . $person->getAge() . ' years old.<br>';
$person->setName('Darth Vader');
echo 'My name is ' . $person->getName() . ', and I am ' . $person->getAge() . ' years old.<br>';
{% endhighlight %}

This sets up a person class with a constructor, and getters and setters for the attributes on the class. Then we write some sample code to make sure everything is working. There is nothing wrong with this, but calling getters and setters to access protected attributes is annoying. Let&#8217;s make it easier to get them.

## Getters on the fly

The [`__get` magic method][2] gets called whenever we call a variable without an assignment operator. If we override this on our class, we can allow users to access our protected attributes without calling the getter. Let&#8217;s see what this would look like. Add the following code to your class.

{% highlight php startinline %}
public function __get($key)
{
    $key = ucfirst($key);
    return $this->{'get' . $key}();
}
{% endhighlight %}

Let&#8217;s walk through this code. First, we declare our `__get` method. It will receive one parameter &#8212; the attribute we are trying to access. We take this key and call [`ucfirst`][3] on it to capitalize the first letter in the string. Then, we concatenate the string &#8216;get&#8217; onto the it and call the resulting function, which is our getter!

To demonstrate how to use this, let&#8217;s edit our sample code.

{% highlight php startinline %}
$person = new Person('Anakin Skywalker', 12);
echo 'My name is ' . $person->name . ', and I am ' . $person->age . ' years old.<br>';
$person->setAge(25);
echo 'My name is ' . $person->name . ', and I am ' . $person->age . ' years old.<br>';
$person->setName('Darth Vader');
echo 'My name is ' . $person->name . ', and I am ' . $person->age . ' years old.<br>';
{% endhighlight %}

## Setters on the fly

The [`__set` magic method][4] is similar to the `__get` method. It allows us to set attributes on a class very easily. Insert the below code into your class.

{% highlight php startinline %}
public function __set($key, $value)
{
    $this->{$key} = $value;
}
{% endhighlight %}

This code is even simpler than the pervious one we wrote. It simply sets the attribute with the passed in value. Let&#8217;s look at how we can utilize this new feature.

{% highlight php startinline %}
$person = new Person('Anakin Skywalker', 12);
echo 'My name is ' . $person->name . ', and I am ' . $person->age . ' years old.<br>';
$person->age = 25;
echo 'My name is ' . $person->name . ', and I am ' . $person->age . ' years old.<br>';
$person->name = 'Darth Vader';
echo 'My name is ' . $person->name . ', and I am ' . $person->age . ' years old.<br>';
{% endhighlight %}

## Print it out!

Lastly, echoing out the really on string is annoying. Let&#8217;s make it easier on ourselves by implementing the [`__toString` magic method][5]. This will let us define how to display an object as a string.

{% highlight php startinline %}
public function __toString()
{
    return 'My name is ' . $this->name . ', and I am ' . $this->age . ' years old.<br>';
}
{% endhighlight %}

Now if we `echo` out the object, it will display this beautiful message. One last time, change the sample code to the below code.

{% highlight php startinline %}
$person = new Person('Anakin Skywalker', 12);
echo $person;
$person->age = 25;
echo $person;
$person->name = 'Darth Vader';
echo $person;
{% endhighlight %}

## Wrapping Up

As you can see, PHP magic methods can make interacting with a class so much easier. I recommend being careful with these. There may be times that you don&#8217;t want the user to have access to an attribute. You must ensure the `__get` and `__set` methods don&#8217;t have access to these attributes. Hopefully this helps you on your next venture! Comment below if you have any thoughts.

## Further Reading

  * [PHP Magic Methods][6]

 [1]: http://www.php.net/manual/en/language.oop5.magic.php
 [2]: http://www.php.net/manual/en/language.oop5.overloading.php#object.get
 [3]: http://us1.php.net/ucfirst
 [4]: http://www.php.net/manual/en/language.oop5.overloading.php#object.set
 [5]: http://www.php.net/manual/en/language.oop5.magic.php#object.tostring
 [6]: http://php.net/manual/en/language.oop5.magic.php