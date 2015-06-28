---
id: 181
title: Responsive Images
author: Alex Sears
layout: post
guid: http://alexsears.com/?p=181
permalink: /article/responsive-images
dsq_thread_id:
  - 1482045290
categories:
  - Article
tags:
  - CSS
  - Responsive Design
---
Recently, I had to make a small landing page for a client [at my job][1]. I had never done anything with responsive design. So, this became a chance for me to learn something new and fun! A big problem I was having was getting the height to scale with the width.

<!--more-->

After some googling, I discovered the answer was one small line of code. Check it out below and, as always, let me know if this helped!

{% highlight css %}
img {
    width: 100%;
    height: auto;
}
{% endhighlight %}

# Further Reading

  * [Why We Need Responsive Images][2]
  * [Which responsive images solution should you use?][3]

 [1]: http://infotrustllc.com/
 [2]: http://timkadlec.com/2013/06/why-we-need-responsive-images/
 [3]: http://css-tricks.com/which-responsive-images-solution-should-you-use/