---
id: 63
title: Tips for Translating a WordPress Website, Part Three
author: Alex Sears
layout: post
guid: http://alexsears.com/?p=63
permalink: /article/tips-for-translating-a-wordpress-website-part-three
dsq_thread_id:
  - 1341303950
categories:
  - Article
tags:
  - PHP
  - Translation
  - Wordpress
  - WPML
---
Here we are again, translating websites in WordPress using the WPML plugin. This time around we will look at making sure conditionals execute correctly no matter what language is being displayed. We will also discuss how to display custom post types that we may not want or need to be translated. Let&#8217;s get to it!

<!--more-->

## Conditionals in Multiple Languages

Many times in WordPress we want to display certain information based on the page we are currently viewing. We would use a conditional such as a `for` loop to test what page we are on and then, based on the feedback, display page-specific information. A good way to test for a page in WordPress is by using the `is_page()` function. It takes an ID of a page. This function will return true if you are currently viewing that page, making it a powerful way to generate page-specific content.

To leverage this power, we need to use the function `icl_object_id()` along with `is_page()`. If you are not familiar with `icl_object_id()`, read the post previous to this one in this series, [Tips for Translating a WordPress Website, Part Two][1]. Let&#8217;s see an example of how this might look.

{% highlight php %}
<?php

if (is_page(icl_object_id(18,'page',true))) {
    echo "Yay! We are on the page with ID of 18 or the translated version of page 18.";
}

?>
{% endhighlight %}

## Displaying Custom Post Types in WPML

This specific topic came up recently when translating a site for a company with a ton of products. In this case, it was not necessary for us to translate each and every project. It would have taken way too long which would have driven up the cost of development. However, I was having trouble getting these products to display on the translated pages. It turns out the solution involves no coding! To get custom posts to display, follow the steps below. Note: This requires the use of the Translation Management Plugin, which is downloadable along with the WPML plugin.

Go into your admin area. Hover over the WPML label on the left-hand side, and click on &#8220;Translation Management.&#8221;

{% include image_caption.html imageurl="/assets/wp-content/uploads/2013/05/PartThreeStep1-1024x550.jpg" title="Part Three Step One" %}

Now click on &#8220;Multilingual Content Setup&#8221; at the top of the resulting page.

{% include image_caption.html imageurl="/assets/wp-content/uploads/2013/05/PartThreeStep2-1024x537.jpg" title="Part Three Step Two" %}

Scroll down to almost the end of the page, and you will see a section called &#8220;Custom posts.&#8221; Here is where it will display all your custom posts and give you the option to translate it or not.

{% include image_caption.html imageurl="/assets/wp-content/uploads/2013/05/PartThreeStep3-1024x531.jpg" title="Part Three Step Three" %}

Click the radio button where it says &#8220;Do nothing&#8221; next to the custom post type you do not want to be translated. Then click &#8220;Save.&#8221;

{% include image_caption.html imageurl="/assets/wp-content/uploads/2013/05/PartThreeStep4-1024x534.jpg" title="Part Three Step Four" %}

There you have it. That wasn&#8217;t so hard, was it?

## Wrapping Up

Not everything involving translation has to be difficult. As you saw, some things are built right into the plugin. Everything involving code just requires a small edit in a few places. However, we aren&#8217;t done yet. Stay tuned for one more post including a few more tricks that may make translating that site of yours just a bit easier.

## Further Reading

  * [Language Dependent IDs &#8211; WPML][2]
  * [`is_page()` Function Reference][3]
  * [WPML Core and Add-on Plugins][4]

 [1]: http://alexsears.com/articles/tips-for-translating-a-wordpress-website-part-two
 [2]: http://wpml.org/documentation/support/creating-multilingual-wordpress-themes/language-dependent-ids/
 [3]: http://codex.wordpress.org/Function_Reference/is_page
 [4]: http://wpml.org/documentation/wpml-core-and-add-on-plugins/