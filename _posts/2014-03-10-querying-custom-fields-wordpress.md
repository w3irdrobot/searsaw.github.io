---
id: 336
title: Querying by Custom Fields in WordPress
author: Alex Sears
layout: post
guid: http://alexsears.com/?p=336
permalink: /article/querying-custom-fields-wordpress
dsq_thread_id:
  - 2402731743
categories:
  - Article
tags:
  - PHP
  - Wordpress
---
Sometimes, we want to query the WordPress database for all of a post type based on some custom fields we have set. WordPress makes it easy to do. However, the syntax can be kind of cryptic due to its usage of multidimensional arrays. Hopefully, I can help clear it up for you a bit.

<!--more-->

## The Issue

Let&#8217;s say we have a &#8220;Show&#8221; custom post type with a name of &#8220;show.&#8221; However, we only want to display upcoming events, not ones that have already passed. This post type has a custom field called &#8220;start\_date\_time.&#8221; This means we should only query the database for shows that have a start date and time that are greater than the current time. Let&#8217;s go!

## The Solution

{% highlight php %}
<?php
$now = new DateTime(current_time('mysql'));
$args = array(
    'post_type' => 'show',
    'meta_query' => array(
        array(
            'key' => 'start_date_time',
            'value' => $now->format('Y-m-d H:i:s'),
            'compare' => '>=',
            'type' => 'DATETIME'
        )
    )
);
$query = new WP_Query($args);

if ($query->have_posts()) : while ($query->have_posts()) : $query->the_post(); ?>

...Do Some Crazy Coding...

<?php endwhile; endif; ?>
{% endhighlight %}

The code above will only loop through our future events. That was easy! Let&#8217;s walk through the code.

First, we create a [DateTime object][1] based on the current time. Using the [`current_time()`][2] function we ensure that we get the correct current time based on the timezone setting in the WordPress admin menu. Next, we set up our arguments for our query. We say we want to search through all the shows. The meta query is how we query based on custom fields; it is a multidimensional array of queries. There is a new array for each constraint on the result set. In the above code, we say we want to restrict the shows returned to only those in the future. We do that by setting the key to the field that holds the start date, the value to test it against to be the current date formatted into the MySQL form of the date and time, the way to compare them ( we want the ones whose keys are greater than or equal to the curent date ), and the type of field we are comparing to ( the start\_date\_time field is a DATETIME field ). Lastly, we create the query and then loop through it using the typical WordPress loop.

## Get More Complicated

Let&#8217;s say we add an &#8220;ongoing&#8221; field to our Show post type. This will designate if it&#8217;s an ongoing show and therefore should always show up on the page. We now need to get all the shows in the future or ones that are ongoing. Piece of cake!

{% highlight php %}
<?php
$now = new DateTime(current_time('mysql'));
$args = array(
    'post_type' => 'show',
    'meta_query' => array(
        'relation' => 'OR',
        array(
            'key' => 'start_date_time',
            'value' => $now->format('Y-m-d H:i:s'),
            'compare' => '>=',
            'type' => 'DATETIME'
        ),
        array(
            'key' => 'ongoing',
            'value' => true,
            'compare' => '=',
            'type' => 'BOOLEAN'
        )
    )
);
$query = new WP_Query($args);

if ($query->have_posts()) : while ($query->have_posts()) : $query->the_post(); ?>

...Do Some Crazy Coding...

<?php endwhile; endif; ?>
{% endhighlight %}

To make our query return the right results, we had to add a few lines to our arguments array. As you can see, we add a new query to the meta query array. This query tests for shows that have an &#8220;ongoing&#8221; field that is equal to true. The importaant part we added, though, is the relation key in the meta query array. This tells WordPress what kind of relation our meta queries have to each other. We set it equal to &#8220;OR&#8221; because we want all shows in the future *OR* shows that are ongoing. If we left it as &#8220;AND&#8221;, then we would only get shows that are ongoing in the future, which makes no sense.

## Wrapping Up

Querying in WordPress can sometime get confusing. Hopefully, I have helped clear up some of that confusion. Leave a comment with any suggestions or if you just want to say &#8220;Hi!&#8221;

## Further Reading

  * [Class Reference/WP Query &#8211; Custom Field Parameters][3]

 [1]: http://www.php.net/manual/en/class.datetime.php
 [2]: https://codex.wordpress.org/Function_Reference/current_time
 [3]: http://codex.wordpress.org/Class_Reference/WP_Query#Custom_Field_Parameters