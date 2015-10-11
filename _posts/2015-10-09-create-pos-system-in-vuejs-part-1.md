---
id: 419
title: Create a POS System in Vue.js - Part 1
author: Alex Sears
layout: post
guid: http://alexsears.com/?p=419
permalink: /article/create-pos-system-in-vuejs-part-1
seo_description: Together, we will create a POS system using Vue.js, starting with a mockup and moving to a fully-functional application.
categories:
  - Article
tags:
  - JavaScript
  - Vue.js
---

This is the first part of the Vue.js POS tutorial series.  If you haven't read the introduction or want to skip ahead, below are the links for each part.

- [Create a POS System in Vue.js - Introduction]({% post_url 2015-10-08-create-pos-system-in-vuejs-intro %})
- Create a POS System in Vue.js - Part 1
- [Create a POS System in Vue.js - Part 2]({% post_url 2015-10-10-create-pos-system-in-vuejs-part-2 %})
- [Create a POS System in Vue.js - Part 3]({% post_url 2015-10-10-create-pos-system-in-vuejs-part-3 %})

All the code can be found in [the Github repo for this series](https://github.com/searsaw/pos-system-vuejs-blog).

So let's get started.  First, pull down the code from the repo for this series.  Then we will check out the beginning code.  Lastly, let's bring in all the dependencies.  I have gone ahead and provided a `package.json` file that has some of the things we need.

{% highlight bash %}
git clone https://github.com/searsaw/pos-system-vuejs-blog.git
cd pos-system-vuejs-blog
git checkout -f part-1-start
npm install
{% endhighlight %}

I recommend running a local server while going through these tutorials.  There are a few options.  You can run a local python server using `python -m SimpleHTTPServer`.  My preferred method is to use the http-server npm package.  You can install and run the server using the following commands:

{% highlight bash %}
npm install -g http-server
http-server -o -p 8000
{% endhighlight %}

Once the server is running, view the page in the browser at `http://localhost:8000`.  This is just the basic UI we will be working with.  The items on the right are the items that can be added to a transaction.  The transaction information is on the left.  It shows the items already added to the transaction, their amounts and totals, and then the subtotal, tax, and total of the whole transaction.  Let's start making this thing function like a real POS system.

## The Item Buttons

We need to create an instance of Vue and initialize our "state."  We will need something to hold all the items that a customer can buy.  We will call this `items`.  We will also need to hold onto the items that have been added to the transaction.  We will call these `lineItems`.  These can both be represented as arrays.  It is best to tell Vue about any pieces of data we will want it to track right from the beginning.  Open the file `js/app.js` and put the following code into it.

{% highlight javascript %}
(function($, Vue) {
    'use strict';

    new Vue({
        el: '#pos',
        data: {
            items: [],
            lineItems: []
        },
    });
})(jQuery, Vue);
{% endhighlight %}

This creates a new Vue instance, binds it to the HTML element with an ID of `pos`, and initializes the data that Vue is watching to two empty arrays.  We now need to populate the items array with real items.  To simulate getting this data from an API of some sort, create a file in the root directory of this project called `items.json` and put the following into it:

{% highlight json %}
[
    {
        "name": "Apple",
        "price": 2.99
    },
    {
        "name": "Orange",
        "price": 0.99
    },
    {
        "name": "Banana",
        "price": 5.99
    },
    {
        "name": "TV",
        "price": 199.99
    },
    {
        "name": "X-Box One",
        "price": 299.99
    },
    {
        "name": "iPhone 6 Plus",
        "price": 299.99
    },
    {
        "name": "Cup",
        "price": 3.99
    },
    {
        "name": "Yogurt",
        "price": 0.49
    },
    {
        "name": "Hat",
        "price": 9.99
    }
]
{% endhighlight %}

This is just a JSON array that contains a bunch of objects with a name and price.  Now let's have our Vue instance pull this data in using jQuery's AJAX functionality.  To do this, we can hook into the Vue lifecycle and have a function called when the instance is created.  Add the following to `app.js`.

{% highlight javascript %}
created: function() {
    $.get('/items.json', function(items) {
        this.items = items;
    }.bind(this), 'json');
},
{% endhighlight %}

This uses jQuery's `.get` method to make a GET request to `localhost:8000/items.json` and returns that data to the callback.  Since it is just an array, we can simply set the items on our Vue instance to the items we received from the GET request.  One thing you may notice is my use of `.bind(this)` at the end of the callback function.  Since the keyword `this` refers to the function once inside the callback function, I decided to use the `bind` call to override that and make the Vue instance equal `this` inside the callback.  There are other solutions, but this is the one I personally prefer.  For more information, [check out this article from Smashing Magazine](http://www.smashingmagazine.com/2014/01/understanding-javascript-function-prototype-bind/).

Next, we need to use these items in our UI.  Let's switch over to `index.html`.  We need to replace the hardcoded list of items with a dynamic list.  This is how it will look.

{% highlight html %}
<div class="list-group">
    <button class="list-group-item item" v-repeat="item: items" v-on="click: onItemClick(item)">
        <strong>{{ "{{ item.name " }}}}</strong> - {{ "{{ item.price " }}}}
    </button>
</div>
{% endhighlight %}

The above snippet creates a Bootstrap `list-group` that contains a single button.  The magic comes in the special `v-repeat` directive.  We "pass" it a variable name followed by a colon and then the data item we want to iterate over.  The variable name will be the name we reference for each item in the iterated array.  Then inside the button, we spit out the item name and price.  We use the super sweet moustache-looking syntax to tell Vue to print out the value of the thing we pass in.  I will get to the `v-on` directive in a second.  Refresh the browser, and you should see our list of items displayed on the right-hand side.  Sweet!

Now, you may have seen on the previous snippet that there was a directive called `v-on` on the button as well.  This is how we can setup event listeners on DOM elements using Vue.  We pass it an event followed by a colon and then the name of the function we want to call.  This function needs to exist on the Vue instance.  In this case, when a button is clicked, we want to fire the `onItemClick` function and pass it the item.

Let's create this method.  Add the following to `app.js` inside the Vue instance we are creating.

{% highlight javascript %}
methods: {
    onItemClick: function(item) {
        var found = false;

        for (var i = 0; i < this.lineItems.length; i++) {
            if (this.lineItems[i].item === item) {
                this.lineItems[i].numberOfItems++;
                found = true;
                break;
            }
        }

        if (!found) {
            this.lineItems.push({ item: item, numberOfItems: 1, editing: false });
        }
    }
}
{% endhighlight %}

All methods we add to the Vue method that we want to expose need to be added to the `methods` object.  Here we are adding the `onItemClick` method.  It goes through each of the line items that are already part of the transaction.  If we find one that matches the one that was passed to the method, then we just increment the number of that particular item.  If it isn't found, then we create an object that contains the item, the number of that item that are in the transaction, and a flag of whether we are editing the item.  We will see this used later.  This object is then pushed onto the line items array.  Pretty simple stuff.  Luckily, Vue makes interacting with the state extremely easy!

Now if you reload the browser and click on the button, items are being added to the internal array, but nothing is happening in the UI!  Let's work on the transaction side now.

## The Transaction Information

The first thing we need to do is try to get the line items dynamically displaying in the UI so we can see it working.  Let's change up the table body to iterate over the line items and display the same way as the hardcoded ones do.  I think you already know where this is headed...yep, `v-repeat` to the rescue!

{% highlight html %}
<tbody>
    <tr v-repeat="item: lineItems">
        <td>{{ "{{ item.item.name " }}}}</td>
        <td>
            <span v-if="!item.editing" v-on="dblclick: toggleEdit(item)">{{ "{{ item.numberOfItems " }}}}</span>
            <input v-if="item.editing" v-on="blur: toggleEdit(item)" type="number" v-model="item.numberOfItems">
        </td>
        <td>{{ "{{ item.numberOfItems * item.item.price | currency " }}}}</td>
        <td><i class="fa fa-times" v-on="click: removeItem(item)"></i></td>
    </tr>
</tbody>
{% endhighlight %}

Here we are iterating through the line items, creating a new row in the table each time.  The first piece of data in each table row is the item name.  Because the item is stored in each line item object, we have to use `item.item.name` to get the name.  I could have named them better...but I digress.

The next piece of data in the table is the number of the item that is in the transaction.  You can see we are adding an event listener to a `span` that contains the number.  On a double click, it will fire the `toggleEdit` method, which we still have to create.  It is also using a new directive, `v-if`.  This directive evaluates the expression we pass it and will only display the element if it is truthy.  In this case, we want to show this element only if the item amount is not being "editted."

There is also a number input right after the span that is also using the `v-if` directive.  It's just the opposite though.  We want to display it if the item amount is being editted.  The input has an event listener attached to it that will again fire `toggleEdit` method when the blur event fires on it, which happens when it loses focus.  There is one more directive on it &#8212; `v-model`.  This binds the value of the input to the `numberOfItems` property on the line item.  When the value of the input is changed, the value of the property changes.  When the value of the property changes, the value of the input is changed.

Next, we have the total amount that line item is costing us.  This is calculated by multiplying the cost of the item by the number of the item that is in the transaction.  We are then piping this value to a filter.  Filters are simply methods that can change data for us.  They are very handy in the UI.  This particular one is built into Vue.js and simply formats the number passed to it into a currency string.

Lastly, we have an icon brought in by [Font Awesome](https://fortawesome.github.io/Font-Awesome/) of an `x`.  We are adding an event listener to fire when it is clicked.

Now if we refresh the page and click on the item buttons, you will see the line items added to the table and the amounts will increment if you click on an item more than once.

We need to make the item numbers editable and allow the items to be removed from the transaction.  We already are adding the event listeners.  We just need to create the methods.  Add the below code to the `methods` object in `app.js`.

{% highlight javascript %}
toggleEdit: function(lineItem) {
    lineItem.editing = !lineItem.editing;
},
removeItem: function(lineItem) {
    for (var i = 0; i < this.lineItems.length; i++) {
        if (this.lineItems[i] === lineItem) {
            this.lineItems.splice(i, 1);
            break;
        }
    }
}
{% endhighlight %}

`toggleEdit` simply sets the line item's `editing` property to the opposite of the current value of the property.  This is a neat trick if you just want to switch the value of a boolean property.  `removeItem` finds the index of the line item in the `lineItems` array and uses the array method `.splice` to remove it from the array.  That's it!  Vue.js handles updating the UI for us.  Go ahead and try it out.  We have one last piece to finish and that's updating our total amount for the transaction and such.

## Transaction Totals

The easiest way to get these totals is to utilize computed properties.  These are properties we tell Vue to calculate for us and then provide us with to use in our UI.  Add the below code to `app.js`.

{% highlight javascript %}
computed: {
    subtotal: function() {
        var subtotal = 0;

        this.lineItems.forEach(function(item) {
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
}
{% endhighlight %}

Using this, Vue will provide us with the variables `subtotal`, `tax`, and `total`.  The calculations here are pretty simple so I won't bother explaining them.  Let's update our UI to use them.

{% highlight html %}
<table class="table">
    <tbody>
        <tr>
            <td>Subtotal:</td>
            <td>{{ "{{ subtotal | currency " }}}}</td>
        </tr>
        <tr>
            <td>Tax:</td>
            <td>{{ "{{ tax | currency " }}}}</td>
        </tr>
        <tr>
            <td>Total:</td>
            <td>{{ "{{ total | currency " }}}}</td>
        </tr>
    </tbody>
</table>
{% endhighlight %}

Here we are simply outputting the values of those variables once filtered through the currency filter again.  Since these are computed properties, Vue will keep them up-to-date as things change throughout the UI.  It's that simple.

## Wrap-up

Well, we created a simple but functional POS system.  As you can see, Vue made that much easier than it would normally be using only jQuery.  Our code is pretty readable, but it could be better.  [Read the next post in this series]({% post_url 2015-10-10-create-pos-system-in-vuejs-part-2 %}) to see how we can split this up into templates and components to encapsulate some of this code.

Also, let me know how you feel in the comments.  If you want to just say, "Hi," that's awesome too!

### Read the rest of the series

- [Create a POS System in Vue.js - Introduction]({% post_url 2015-10-08-create-pos-system-in-vuejs-intro %})
- Create a POS System in Vue.js - Part 1
- [Create a POS System in Vue.js - Part 2]({% post_url 2015-10-10-create-pos-system-in-vuejs-part-2 %})
- [Create a POS System in Vue.js - Part 3]({% post_url 2015-10-10-create-pos-system-in-vuejs-part-3 %})