---
id: 44
title: Tips for Translating a WordPress Website, Part Two
author: Alex Sears
layout: post
guid: http://alexsears.com/?p=44
permalink: /article/tips-for-translating-a-wordpress-website-part-two
dsq_thread_id:
  - 1308130029
categories:
  - Article
tags:
  - PHP
  - Translation
  - Wordpress
  - WPML
---
Last time we saw how to get the ID of a page very easily. We also discovered that to store characters in languages such as Chinese and Russian, we need to use a character set of UTF-8 on our databases.

<!--more-->

If none of this sounds familiar, read the post previous to this one, [Tips for Translating a WordPress Website, Part One][1]. Today, we will talk about some more things that will be necessary to implement to successfully translate a website.

## Surround Strings with `__()` and `_e()`

This is something that is so easy to do, I don&#8217;t know why every theme developer doesn&#8217;t just make it a habit to do. These two functions essentially serve the same purpose with one subtle but very important difference. The `_e()` function echoes out the string it is given. If a translation is available for that string, then it will echo that instead. `__()`, however, returns the translated string. The key difference is in the words *echoes* and *returns*.

Let&#8217;s do an example to show the difference. Below is a code example. See if you can guess what the final output will be.

{% highlight php linenos %}
<?php
$english = ['one', 'two', 'three', 'four', 'five'];
$spanish = '';

_e('Good day!', 'Example');

foreach ($english as $word) {
    $spanish .= __($word, 'Example') . ', ';
}

echo $spanish;
?>
{% endhighlight %}

Any idea? Click below to see if you were right!

{% highlight text %}
buen día
uno, dos, tres, cuatro, cinco,
{% endhighlight %}

As you can see, the `_e('Good day!', 'Example')` just echoes out &#8220;Good day!&#8221; in Spanish. The next part cycles through an array and translates it into Spanish. However, instead of echoing out each word, it returns the word to the script. The script concatenates the translated word onto the string we are building, along with a comma and a space for readability.

As you can see, this is a really easy step to take while building a theme to make the site much easier to translate later using WPML, which finds these strings when it scans your site. This is how the plugin knows which strings need to be translated. If you are scanning your website, and you know you aren&#8217;t getting all your strings translated by the plugin, then you may have to manually go through your code and add these function calls to your code.

## Localizing Hardcoded Links

WPML will do its best to convert all links to point to the translated pages when a translated page is available and a user wants the translated page. It offers a few ways to accomplish this that an administrator can implement. However, if there are any links in your site that are hardcoded into the theme, WPML will not be able to change this link on the fly. The fix for this is relatively easy but can be tedious.

Allow me to introduce the `icl_object_id()`. This function returns the ID of the translated page if it exists. It takes three arguments: the ID of the post or page, the type, and whether we want to return the original. This last argument can be confusing so let me elaborate. If we put true as the third argument, it will return the original ID if the page does not have a translated page with an ID instead. It will return `NULL`

We will use this function along with the native WordPress function `get_permalink()` to get the URL of the page we want to link to on our site.

First, we need to find the IDs of the pages that we are linking to. Next, we must go into the code and change the hardcoded links to use the two functions we just discussed. Let&#8217;s look at what it would look like.

{% highlight php %}
<?php echo get_permalink(icl_object_id(18,'page', true)); ?>
{% endhighlight %}

This code will find out what language we are using on our site (English, Spanish, German, etc.). Then, it will look for a page that is the translated version of the page whose ID we passed the `icl_object_id()` function. If it can&#8217;t find it, the function will just return the initial ID. This ID is passed to the `get_permalink()` function, which gets the URL for the page with that ID. Since each translated page has its own ID, this is an effective way to get the URLs of any page, translated or not.

This code would be put in the `href` of an anchor tag. And BOOM!


## Wrapping Up

Sometimes, to get the results we need, it may be necessary to think outside of the box and use functions together in interesting ways. However, this is what makes coding fun. There is always more than one way to solve the problem. Stay tuned for more tips coming soon!

## Further Reading

  * [Language Dependent IDs &#8211; WPML][2]
  * [`_e()` Function Reference &#8211; WordPress][3]
  * [`__()` Function Reference &#8211; WordPress][4]

 [1]: http://alexsears.com/articles/tips-for-translating-a-wordpress-website-using-wpml
 [2]: http://wpml.org/documentation/support/creating-multilingual-wordpress-themes/language-dependent-ids/
 [3]: http://codex.wordpress.org/Function_Reference/_e
 [4]: http://codex.wordpress.org/Function_Reference/_2