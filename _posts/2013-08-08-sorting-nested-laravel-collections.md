---
id: 242
title: Sorting Nested Laravel Collections
author: Alex Sears
layout: post
guid: http://alexsears.com/?p=242
permalink: /article/sorting-nested-laravel-collections
dsq_thread_id:
  - 1585550700
categories:
  - Article
tags:
  - Collections
  - Laravel
  - MVC
  - PHP
---
Recently, I was working on a project using the [Laravel framework][1]. I wanted to retrieve a customer and then display their transactions on a page. However, I needed the transactions sorted from the most recent to the the earliest (descending order).

<!--more-->

I was using [Eloquent][2] to retrieve the customer and its transactions using the `with()` function. This returned a [collection][3] of transaction objects in the order they were put into the database. There is no definitive order in them. I wanted a way to make sure that they were always returned in descending order just in case something gets messed up and they end up in the database in some weird order. Here is the solution I found.

{% highlight php %}
<?php
$customer = User::with('transactions')->find($id);
$customer->transactions = $customer->transactions->sortBy(function($transaction)
        {
            return $transaction->created_at;
        })->reverse();
{% endhighlight %}

This will first sort the transactions collection in ascending order. Then it reverses the collection, putting them in descending order. Because `sortBy()` returns the sorted object, we can chain these methods to make our code more compact and readable. I hope this helps in your future project. Leave comments if it helps!

 [1]: http://laravel.com/
 [2]: http://laravel.com/docs/eloquent
 [3]: http://laravel.com/docs/eloquent#collections