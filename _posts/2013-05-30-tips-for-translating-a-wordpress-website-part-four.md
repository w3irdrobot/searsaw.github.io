---
id: 84
title: Tips for Translating a WordPress Website, Part Four
author: Alex Sears
layout: post
guid: http://alexsears.com/?p=84
permalink: /article/tips-for-translating-a-wordpress-website-part-four
dsq_thread_id:
  - 1340972724
categories:
  - Article
tags:
  - PHP
  - Translation
  - Wordpress
  - WPML
---
Finally, we have come to the last post in this series&#8230;for now at least. We will talk about displaying the WPML language select list in any place your heart desires. Then we will talk about how to personalize this select list for your website.

<!--more-->

## Displaying the language select list

The language list can easily be displayed in the footer and the sidebar simply by changing the settings of the WPML in your WordPress admin. What if we wanted it in the header or maybe in the content of certain pages? We must edit the code to get this effect. Lucky for us, WPML comes with an action we can hook onto to add into our code to generate all the markup for the list. We barely have to do anything! This action is `icl_language_selector`.

If we wanted to add it into our header, for example, it would look something like the code snippet below.

{% highlight html %}
<div id="container">

    <div id="header">
        <h1 id="site-title"><a href="<?php echo esc_url( home_url( '/' ) ); ?>" title="<?php echo esc_attr( get_bloginfo( 'name', 'display' ) ); ?>" rel="home"><?php bloginfo( 'name' ); ?></a></h1>
        <h2 id="site-description"><?php bloginfo( 'description' ); ?></h2>
        <?php do_action('icl_language_selector'); ?>
    </div>

    <div id="menu">
{% endhighlight %}

That&#8217;s all there is to it. The select list will appear in my header on all my pages.

## Personalizing the Select List

There are a few ways to do this, some more difficult than others. The one I want to discuss is very basic. Anyone with a cursory knowledge of CSS should be able to accomplish this. Simply add CSS styles for each of the parts of the select list you want to alter. You may be wondering how you are supposed to find out what the IDs and classes are that need to be targeted. Since the markup is dynamically created, it may not be immediately obvious how to get this information. I use Chrome&#8217;s Developer Tools, which are built right into the browser. Simply bring this up, and scan the markup that was created for the necessary information. Below is the markup I used on my particular project to change the look of my select list.

{% highlight css %}
/* Custom Language Styles */
#lang_sel_list {
	display: inline-block !important;
	position: absolute !important;
	left: 300px !important;
	top: 26px !important;
	width: 150px !important;
}

#lang_sel_list ul {
	width: 100% !important;
	border: none !important;
}

#lang_sel_list ul li {
	width: 75px !important;
	display: inline-block !important;
}

#lang_sel_list ul li a {
	padding-right: 5px;
	border: none !important;
}
{% endhighlight %}

These are only some of the parts of the list that I targeted. There are many more to make your list as customized as you want. In case you&#8217;re wondering, the styles above will result in a list like the one in the image below.

{% include image_caption.html imageurl="/assets/wp-content/uploads/2013/05/PartFourSelectList.jpg" title="Part Four Select List" %}

## Wrapping Up

I hope these past couple of blog posts have helped you by not only giving you some tricks to make translating a site easier when using WPML but also helping you wrap your head around how WordPress handles localization. Leave a comment if this helped you!

## Further Reading

  * [Adding and Customizing Language Switchers][1]
  * [Language Setup][2]

 [1]: http://wpml.org/tutorials/adding-and-customizing-language-switchers/
 [2]: http://wpml.org/documentation/getting-started-guide/language-setup/