---
id: 418
title: Create a POS System in Vue.js - Introduction
author: Alex Sears
layout: post
guid: http://alexsears.com/?p=418
permalink: /article/create-pos-system-in-vuejs-intro
seo_description: Vue.js is a view library that updates the physical DOM based on state managed in JavaScript. Together we will build a simple POS system that demonstrates its power.
categories:
  - Article
tags:
  - JavaScript
  - Vue.js
---

If you don't know what Vue.js is by now, then you have to check it out!  In case you haven't heard of it, here is how Vue.js describes itself on its website:

> Vue.js is a library for building modern web interfaces. It provides data-reactive components with a simple and flexible API.

That isn't very specific.  Vue.js is a view library that updates the physical DOM based on state managed in JavaScript.  You manipulate the state and the webpage will update to reflect the changes.  Pretty cool, huh?  It's similar to React and can be thought of as a stripped down version of AngularJS.

The thing people seem to dislike about libraries like Vue is that they don't have everything built in.  Angular has AJAX libraries, an awesome router, and other utilities that make it a "be-all and end-all" solution to one-page applications.  Sometimes, however, you don't need a giant framework.  Sometimes, you just want one page to act like that.  In these situations, you want something smaller.  One of the things I like about Vue the most is that it is bare.  I can use anything I want for AJAX interactions with a backend or any animation library I want because the library doesn't have one already.  It's very freeing.

Anyhow, I hope you're excited to explore Vue.  These next couple of posts will show you how to build a simple POS system (Point of Sale, not piece of s@%*).  There will be a list of items that when we click on them, it will add them to the current transaction and show the total as you go along.  You will also be able to change the amounts in the transaction and remove an item from the transaction.

Alright, let's do this!

- Create a POS System in Vue.js - Introduction
- [Create a POS System in Vue.js - Part 1]({% post_url 2015-10-09-create-pos-system-in-vuejs-part-1 %})
- [Create a POS System in Vue.js - Part 2]({% post_url 2015-10-10-create-pos-system-in-vuejs-part-2 %})
- [Create a POS System in Vue.js - Part 3]({% post_url 2015-10-10-create-pos-system-in-vuejs-part-3 %})