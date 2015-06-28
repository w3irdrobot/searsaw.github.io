---
id: 386
title: Iterating Backwards Through A Ruby Array
author: Alex Sears
layout: post
guid: http://alexsears.com/?p=386
permalink: /article/iterating-backwards-ruby-array
dsq_thread_id:
  - 2754345485
categories:
  - Article
tags:
  - Ruby
---
This will be a quick one. I have seen alot of posts on StackOverflow about iterating backwards through a Ruby array. Ruby, in all its beauty, gives quite a few ways to iterate through an array.

<!--more-->

Some of them are listed below:

{% highlight ruby %}
{% raw %}
disney = %w(Mickey Minnie Donald Goofy)

# using Array#each
disney.each { |val| puts val }

# using Enumerable#each_with_index
disney.each_with_index { |val, ind| puts "#{ind}: #{val}" }

# using Integer#times
disney.length.times { |ind| puts "#{ind}: #{disney[ind]}" }
{% endraw %}
{% endhighlight %}

These are just a few of the many ways. In other languages, iterating in the opposite direction can be kind of a pain in the&#8230;well you know. However, Ruby makes it a cinch. You can use the `Array#reverse` method to reverse the order of the elements in the array before iterating over them.

{% highlight ruby %}
{% raw %}
disney = %w(Mickey Minnie Donald Goofy)

# using Array#each
disney.reverse.each { |val| puts val }

# using Enumerable#each_with_index
disney.reverse.each_with_index { |val, ind| puts "#{ind}: #{val}" }
{% endraw %}
{% endhighlight %}

Boom! Yeah, it&#8217;s that easy.

## Further Reading

  * [Array#reverse &#8211; Ruby docs][1]
  * [Array#length &#8211; Ruby docs][2]
  * [Array#each &#8211; Ruby docs][3]
  * [Enumerable#each\_with\_index &#8211; Ruby docs][4]
  * [Integer#times &#8211; Ruby docs][5]

 [1]: http://ruby-doc.org/core-2.0/Array.html#method-i-reverse
 [2]: http://ruby-doc.org/core-2.0/Array.html#method-i-length
 [3]: http://ruby-doc.org/core-2.0/Array.html#method-i-each
 [4]: http://ruby-doc.org/core-2.0/Enumerable.html#method-i-each_with_index
 [5]: http://ruby-doc.org/core-2.0.0/Integer.html#method-i-times