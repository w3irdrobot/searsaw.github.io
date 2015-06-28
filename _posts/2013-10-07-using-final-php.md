---
id: 277
title: Using final in PHP
author: Alex Sears
layout: post
guid: http://alexsears.com/?p=277
permalink: /article/using-final-php
dsq_thread_id:
  - 1842815840
categories:
  - Article
tags:
  - PHP
---
Sometimes when creating a class others will use and extend, you may want to make sure some methods are not overridden. If it needs to be able to work with other APIs, making sure the interface of the class stays the same becomes very important. One way to do this is through the use of the `final` keyword in PHP.

<!--more-->

[The PHP documentation describes the `final` keyword][1] as the following:

> PHP 5 introduces the final keyword, which prevents child classes from overriding a method by prefixing the definition with final. If the class itself is being defined final then it cannot be extended.

PHP will not let a method be overridden by a child class if that method is declared as `final`. Let&#8217;s explore this.

{% highlight php %}
<?php
class Musician {

    protected $name;

    public function __construct($name)
    {
        $this->name = $name;
    }

    public function getName()
    {
        return $this->name . "<br>";
    }

    final public function ballin()
    {
        return $this->name . " is the greatest ever!<br>";
    }

}

class Guitarist extends Musician {

    public function getName()
    {
        return "My name is " . $this->name . "<br>";
    }

}

$musician = new Musician('Slash');
$guitarist = new Guitarist('Stevie Ray Vaughan');
echo $musician->getName();
echo $musician->ballin();
echo $guitarist->getName();
echo $guitarist->ballin();
{% endhighlight %}

The following code creates a `Musician` class that takes in a name. It has a `getName()` method to return the name of the person and method to reveal how baller the person is! Notice, the `ballin()()` function is prefaced with `final`. Then we create a `Guitarist` class that extends the `Musician` class and overrides it&#8217;s `getName()` function. Lastly, we echo out some of the results of the functions to make sure classes are working correctly. The results should look like below:

{% highlight bash %}
Slash
Slash is the greatest ever!
My name is Stevie Ray Vaughan
Stevie Ray Vaughan is the greatest ever!
{% endhighlight %}

Now say we wanted to override the `Musician` class&#8217; `ballin()` function in the `Guitarist` class. That code may make the `Guitarist` look something like the following:

{% highlight php %}
<?php
class Guitarist extends Musician {

    public function getName()
    {
        return "My name is " . $this->name . "<br>";
    }

    public function ballin()
    {
        return "This won't work!";
    }

}
{% endhighlight %}

If you run the new code, you will get a fatal PHP error sayings you &#8220;Cannot override final method Musician::ballin()&#8221;.

## Wrapping Up

As you can see, the `final` keyword has an interesting effect on our PHP code. It has the ability to stop methods in child classes from being overridden. This could be used in a class whose methods&#8217; implementation cannot change. The sky&#8217;s the limit!

## Further Reading

  * [The `final` keyword &#8211; PHP documentation][1]
  * [Why We Use `final`][2]

 [1]: http://php.net/manual/en/language.oop5.final.php
 [2]: http://stackoverflow.com/questions/9434899/why-we-use-final-could-you-give-me-an-example-in-real-world