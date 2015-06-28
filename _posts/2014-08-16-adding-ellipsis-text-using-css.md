---
id: 417
title: Adding Ellipsis to Text Using CSS
author: Alex Sears
layout: post
guid: http://alexsears.com/?p=417
permalink: /article/adding-ellipsis-text-using-css
dsq_thread_id:
  - 2935022068
categories:
  - Article
tags:
  - CSS
  - Ellipsis
  - SASS
  - SCSS
---
In almost every blog these days, there is a listing page of blog posts with &#8220;Read more&#8221; or ellipsis (&#8230;) to show there is more content to be viewed. Many people will use backend languages, such as PHP and Ruby, to truncate the text before it gets to the browser. That is an effective technique that works well. However, if you want to be more responsive and allow the browser to be able to change how much text is shown depending on the screen width, you should use CSS to create these ellipsis.

<!--more-->

It&#8217;s an easy technique that I will show below.

You can view the code for this post on [CodePen][1]. I used Slim framework for the HTML on the CodePen, just to forewarn you. There isn&#8217;t much HTML to this example so you should still be able to read it if you have never used Slim.

## The HTML

{% highlight html %}
<!doctype html>
<html>
  <head>
    <title>Ellipsis</title>
  </head>
  <body>
    <div id="block">This is a really, really long phrase.</div>
  </body>
</html>
{% endhighlight %}

Not much to this part. It&#8217;s a simple document with just a `div` in it. This `div` is given an ID of `"block"`.

## The CSS

{% highlight css %}
<style>
#block {
  display: inline-block;
  width: 100px;
  height: 200px;
  margin: 5px 10%;
  padding: 5px;
  color: #fff;
  background-color: #000;
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
}
</style>
{% endhighlight %}

This code is also just vanilla CSS. However, the beauty lies in the highlighted lines above. We tell the `div` to not wrap the text in it. This will display the text all in one line. We then say to hide the overflow. If you look at it now, it is just cut off at the edge of the `div`. The magic happens with the last line. It says that for all text overflow, we want to input an ellipsis instead of just cutting it off. This gives our users the nice feedback that there is more to be read.

This is nice because if the width of the div changes, the ellipsis moves to match the width. So if it got bigger, it will display more of the phrase. If it can fit the entire phrase in, it will output the entire thing and not have an ellipsis at all. Seeing is believing though, right? So let&#8217;s see this in action!

## Some JavaScript

Let me preface that this JavaScript is not necessary to make this technique work. It is already working! I just want you to be able to see how it changes as the width of the `div` changes.

{% highlight javascript %}
function checkWidth() {
  var width = parseInt(window.getComputedStyle(this).width, 10);
  width += 100;
  if (width >= 600) {
    width = 100;
  }
  this.style.width = width + "px";
}

var ele = document.getElementById('block');
ele.addEventListener('click', checkWidth, false);
{% endhighlight %}

If you put this JavaScript in your document and then click on the div, you will see its size grow by 100 pixels each time until it gets to 600 pixels. Then it will shrink back to 100 pixels. Notice as it changes, you can see more and more of the phrase until the whole phrase is revealed and the ellipsis is gone.

## Wrapping Up

There is some pretty sweet stuff you can do in CSS if you explore some of the lesser known properties. This is just one of many cool things. As the CSS3 specification gets further along, even more cool things are coming out. Hopefully soon we won&#8217;t use floats for layouts anymore (hello [Flexbox][2] :) ). Let me know if this has helped at all in the comments!

 [1]: http://codepen.io/searsaw/pen/yAsih
 [2]: http://css-tricks.com/snippets/css/a-guide-to-flexbox/