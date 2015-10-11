---
id: 421
title: Create a POS System in Vue.js - Part 3
author: Alex Sears
layout: post
guid: http://alexsears.com/?p=421
permalink: /article/create-pos-system-in-vuejs-part-3
seo_description: Learn how to move your Vue.js components into their own directories and use Gulp and Browserify to pull it all together.
categories:
  - Article
tags:
  - JavaScript
  - Vue.js
---

This is the third part of the Vue.js POS tutorial series.  If you haven't read the previous posts, below are the links for each part.

<!--more-->

- [Create a POS System in Vue.js - Introduction]({% post_url 2015-10-08-create-pos-system-in-vuejs-intro %})
- [Create a POS System in Vue.js - Part 1]({% post_url 2015-10-09-create-pos-system-in-vuejs-part-1 %})
- [Create a POS System in Vue.js - Part 2]({% post_url 2015-10-10-create-pos-system-in-vuejs-part-2 %})
- Create a POS System in Vue.js - Part 3

All the code can be found in [the Github repo for this series](https://github.com/searsaw/pos-system-vuejs-blog).

In the previous post, we reorganized our code into components.  This allows us to reuse code only pieces of our code.  However, our `index.html` and `app.js` files had a bunch of extra code in them that would require us to sort through the next time we wanted to use the code in another project.  There has to be a better way to organize this code, right?  Well of course there is, or this would be the lamest blog post ever.

The solution is to move our code into subdirectories, each containing a component.  Then we can use Browserify to bring it all together.  My preferred method is to use Gulp with Browserify.  So I will show you how to set it up.

If you are working with the code from the Github repo, checkout the `part-3-start` tag to get to a good starting point.

{% highlight bash %}
git checkout -f part-3-start
{% endhighlight %}

Alright, let's do this!

## The Gulpfile

First we need to pull in the dependencies we will need and then create a Gulpfile.  The Gulpfile tells Gulp what tasks we have and how to do them.  That way, when we run Gulp, it reads the Gulpfile and then does what we told it to do.

To bring in the dependencies, we will use npm like we have been using so far.  Run the following command from the command line:

{% highlight javascript %}
npm install --save-dev gulp browserify partialify vinyl-source-stream
{% endhighlight %}

This will install Gulp, Browserify, Partialify, and Vinyl-Source-Stream.  Gulp is a task runner.  Browserify allows us to use node syntax to bring in dependencies by `require`ing them.  It will then compile it all into one file for us.  Partialify is a [transform](https://github.com/substack/node-browserify/wiki/list-of-transforms) for Browserify that will let us put our templates in their own directories.  Lastly, Vinyl-Source-Stream will transform the output of Browserify into a stream that Gulp can understand.

Create a file in the root of the project and call it `gulpfile.js`.  Put the following code in it.

{% highlight javascript %}
var gulp = require('gulp');
var browserify = require('browserify');
var partialify = require('partialify');
var source = require('vinyl-source-stream');

gulp.task('js', function() {
    browserify('js/app.js')
        .transform(partialify)
        .bundle()
        .pipe(source('bundle.js'))
        .pipe(gulp.dest('js'))
});

gulp.task('default', ['js']);
{% endhighlight %}

This is pretty simple node code.  It `require`s our dependencies we downloaded.  Then it creates a task called `js` that runs browserify on our `app.js` file, using Partialify to transform it first.  We then bundle it all up and transform that bundle into a stream Gulp can understand.  Lastly, we ouput it to the `js` folder.  Because we gave it the name `bundle.js` in the `source()` call, it will keep that name when we output it to the `js` folder.  This means the file we use in `index.html` needs to be updated.  We can update our script tags at the bottom of our `index.html` to only be one line.

{% highlight html %}
<script src="/js/bundle.js"></script>
{% endhighlight %}

## The Item Buttons

Pretty much all of this will be a copy-and-paste job.  First, let's clean up the requires in `app.js`.  Go to that file and remove the first and last lines to take the code out of the closure.  Then add the below lines to the top of the file:

{% highlight javascript %}
var Vue = require('vue');
var $ = require('jQuery');
{% endhighlight %}

This will bring in jQuery and Vue using node style requires.  Browserify will change this for us later.

Next, cut the `itemList` object out of `app.js` (just the object, not the property name) and paste it into a file at `js/components/item-list/index.js`.  Then make sure this object is exported from this file.  It should look like this:

{% highlight javascript %}
module.exports = {
    template: require('./template.html'),
    props: ['items', 'add'],
    methods: {
        itemClicked: function(item) {
            this.add(item);
        }
    }
};
{% endhighlight %}

I have gone ahead and changed the `template` declaration.  As you can see, it is bringing in the template from a file in the same directory called `template.html`.  Cut the template code for the item list out of `index.html` and move it into a new file at `js/components/item-list/template.html`.  The require will bring in the HTML from the file as part of the partialify process.

The last thing we need to do is require this component from our `app.js`.  The `itemList` declaration in `app.js` should now look like this:

{% highlight javascript %}
itemList: require('./components/item-list')
{% endhighlight %}

We don't need to specify a file because it will load the `index.js` file by default.  Yay for standards!

Now run `gulp` from the command line.  It should output some stuff saying it's running some tasks.  Once it's finished, view the POS system in the browser.  Holy crap!  It still works.  That was easy.

## The Transaction Information

Moving the transaction component into its own directory is the exact same process.  However, let's walk through it together to make sure everyone in the class understands.

First, cut the transaction object out of `app.js` and paste that code into a file at `js/components/transaction/index.js`.  Make sure that object is exported!  Also, don't forget to go ahead and reqquire the template file.  After all is said and done, the file should look like this:

{% highlight javascript %}
module.exports = {
    template: require('./template.html'),
    props: ['items', 'edit', 'remove'],
    computed: {
        subtotal: function() {
            var subtotal = 0;

            this.items.forEach(function(item) {
                subtotal += item.item.price * item.numberOfItems;
            });

            return subtotal;
        },
        tax: function() {
            return this.subtotal * 0.065;
        },
        total: function() {
            return this.subtotal + this.tax;
        }
    },
    methods: {
        toggleEdit: function(item) {
            this.edit(item);
        },
        removeItem: function(item) {
            this.remove(item);
        }
    }
};
{% endhighlight %}

Next, cut the template out of `index.html` and paste it into it's own file at `js/components/transaction/template.html`.  Then change the transaction component definition in `app.js` to look like the following:

{% highlight javascript %}
transaction: require('./components/transaction')
{% endhighlight %}

Now, let's run `gulp` one more time so it compiles the new changes.  BOOM!  All done.  Wicked quick when you know what you're doing, huh?

## Wrap-up

Well that's it, my friend.  We started with a non-functional mock-up and turned it into a well-organized piece of code.  Now when changes are required in how the transaction information is calculated, it's super easy to find.  Also, we created a simple Gulp pipeline that could easily be extended to include JavaScript minification and uglification.  We could compile SASS and minify it.  We could even minify our HTML!  Sky's the limit with Gulp!

I hope you have enjoyed this series.  Per ushe, let me know how you're feeling in the comments and don't forget to say, "Hi!"

### Read the rest of the series

- [Create a POS System in Vue.js - Introduction]({% post_url 2015-10-08-create-pos-system-in-vuejs-intro %})
- [Create a POS System in Vue.js - Part 1]({% post_url 2015-10-09-create-pos-system-in-vuejs-part-1 %})
- [Create a POS System in Vue.js - Part 2]({% post_url 2015-10-10-create-pos-system-in-vuejs-part-2 %})
- Create a POS System in Vue.js - Part 3