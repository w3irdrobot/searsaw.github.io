---
id: 420
title: Create a POS System in Vue.js - Part 2
author: Alex Sears
layout: post
guid: http://alexsears.com/?p=420
permalink: /article/create-pos-system-in-vuejs-part-2
categories:
  - Article
tags:
  - JavaScript
  - Vue.js
---

This is the second part of the Vue.js POS tutorial series.  If you haven't read the previous posts or want to skip ahead, below are the links for each part.

<!--more-->

- [Create a POS System in Vue.js - Introduction]({% post_url 2015-10-08-create-pos-system-in-vuejs-intro %})
- [Create a POS System in Vue.js - Part 1]({% post_url 2015-10-09-create-pos-system-in-vuejs-part-1 %})
- Create a POS System in Vue.js - Part 2
- [Create a POS System in Vue.js - Part 3]({% post_url 2015-10-10-create-pos-system-in-vuejs-part-3 %})

All the code can be found in [the Github repo for this series](https://github.com/searsaw/pos-system-vuejs-blog).

In the previous post, we created a simple Point of Sale system.  However, the code only uses one Vue instance and no compnents.  There is no sense of encapsulation in the code.  The main Vue instance is responsible for doing everything.  We should move some of these pieces into their own components so that each one can be responsible for itself and use the parent to share state between child components instead.  Also, since this is already a working "product," this should require alot of copy-and-pasting with some extra additions.

If you are working with the code from the Github repo, checkout the `part-2-start` tag to get to a good starting point.

{% highlight bash %}
git checkout -f part-2-start
{% endhighlight %}

Let's get to it!

## The Item Buttons

We are going to move the item list first since it is the smallest piece of HTML code to move.  First, we will move the template into it's own set of script tags and designate it as a template.  Cut the code for the item list out and put it in `index.html` as follows:

{% highlight html %}
<script id="item-list-template" type="text/x-template">
    <div class="list-group">
        <button class="list-group-item item" v-repeat="item: items" v-on="click: itemClicked(item)">
            <strong>{{ "{{ item.name " }}}}</strong> - {{ "{{ item.price " }}}}
        </button>
    </div>
</script>
{% endhighlight %}

Here we have simply moved that code in between `script` tags.  We designate the type as `text/x-template`.  This could be whatever you want, but this seems to be one of the standards the community uses.  So let's stick with that.  If you don't change the type, then the browser will try to execute it as JavaScript, and that would be "Bad News Bears."  Lastly, we give it an ID so we can easily grab it from our JavaScript.

This new component won't just put itself in the DOM though.  We still need to add it!  In the place you cut the HTML out before, input the following in its place:

{% highlight html %}
<item-list items="{{ "{{ items " }}}}" add="{{ "{{ onItemClick " }}}}"></item-list>
{% endhighlight %}

`item-list`?  Dafuq?  That is what we will call our custom component!  Here, we are adding our component to the DOM.  We will tell Vue about it in a second.  Let me explain what's going here.

Each of the attributes on this component (`items`, `add`) are called "props."  They are how we can share things between the parent Vue instance and child component.  Here we are passing the `items` array on the parent instance to our component and making it available via the `items` property on the component.  The `add` attribute is being passed the `onItemClick` method from the parent.  This is how we can add things to the parent state from the child.  However, this also leaves them decoupled because the parent could be anything.  The child doesn't need to know what the parent does with it.

In `app.js`, we need to add a components object so that our Vue instance knows about the components we will use in it.  Add the following to your Vue instance:

{% highlight javascript %}
components: {
    itemList: {
        template: "#item-list-template",
        props: ['items', 'add'],
        methods: {
            itemClicked: function(item) {
                this.add(item);
            }
        }
    }
},
{% endhighlight %}

Here we are simply adding a components object which contains a single component &#8212; our item list.  Notice here we have used camelCase instead of lisp-case like we use in our HTML.  Since JavaScript doesn't allow hyphens in variable names, we have to use camelCase.  Don't worry though!  Vue will automatically make the conversion for us.

We have to designate a template for this component to use.  Since we have already created the template, we just pass it a string that is a CSS selector for the template.

Next, we tell the components what props it can accept.  It's just an array of strings that match the name of the props used in the HTML.

Lastly, we can give our component methods.  In this case, we have a method that is fired when an item is clicked on.  It simply calls the `add` method, which is passed in as a prop, and passes the item to it.  This will call the parent's method that adds the item to its internal `lineItems` array.

Go ahead and test it out.  It will function the same, but our code is a little more reusable.  Next, we tackle the transaction component.

## The Transaction Information

This process will be similar to the previous one.  Cut the transaction code out, and put it back in between script tags like so:

{% highlight html %}
<script id="transaction-template" type="text/x-template">
    <table class="table table-striped table-hover table-bordered table-responsive" v-if="items.length">
        <thead>
            <tr>
                <th>Name</th>
                <th>Number of Items</th>
                <th>Amount</th>
                <th></th>
            </tr>
        </thead>
        <tbody>
            <tr v-repeat="item: items">
                <td>{{ "{{ item.item.name " }}}}</td>
                <td>
                    <span v-if="!item.editing" v-on="dblclick: toggleEdit(item)">{{ "{{ item.numberOfItems " }}}}</span>
                    <input v-if="item.editing" v-on="blur: toggleEdit(item)" type="number" v-model="item.numberOfItems">
                </td>
                <td>{{ "{{ item.numberOfItems * item.item.price | currency " }}}}</td>
                <td><i class="fa fa-times" v-on="click: removeItem(item)"></i></td>
            </tr>
        </tbody>
    </table>
    <p v-if="!items.length">No items have been added.</p>

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
</script>
{% endhighlight %}

This code is very similar to what it was before.  Some variable names may have changed.  In place of where the code was previously we will input the following:

{% highlight html %}
<transaction items="{{ "{{ lineItems " }}}}" edit="{{ "{{ toggleEdit " }}}}" remove="{{ "{{ removeItem " }}}}"></transaction>
{% endhighlight %}

This simply creates a transaction.  It has a couple props also.  First we pass in the parent's `lineItems` array as `items`.  Then we pass in `toggleEdit` as `edit` and `removeItem` as `remove`.  Pretty simple.

The last thing we need to do is tell our Vue instance about this component too.  Add the following to the `components` object in `app.js`:

{% highlight javascript %}
transaction: {
    template: '#transaction-template',
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
}
{% endhighlight %}

Here we have just moved alot of the functionality from the parent into this component.  The template is the template we just created.  We have declared the props that can be passed in from the parent.  These all match the ones we had on our element in our HTML.  The `toggleEdit` calls the prop `edit` which is passed down from the parent.  `removeItem` works in a similar manner.

## Wrap-up

That's it!  Though the functionality hasn't changed, we have extracted out the code that doesn't need to depend on each other.  This way we can reuse parts in other sections of our application without duplcating code.

However, this setup isn't amazing.  Our `index.html` is still cluttered with templates and our component functionality is defined directly on the parent.  It would be better if we could encapsulate all this code into a single directory that could easily be copied to new projects as needed.  Not shockingly, there is a way!  [Check out the next and final piece of this series]({% post_url 2015-10-10-create-pos-system-in-vuejs-part-3 %}) to see how to use the task runner Gulp in conjunction with Browserify to make this possible!

As usual, let me know how you feel in the comments.  Saying, "Hi," is always appreciated also!

### Read the rest of the series

- [Create a POS System in Vue.js - Introduction]({% post_url 2015-10-08-create-pos-system-in-vuejs-intro %})
- Create a POS System in Vue.js - Part 1
- [Create a POS System in Vue.js - Part 2]({% post_url 2015-10-10-create-pos-system-in-vuejs-part-2 %})
- [Create a POS System in Vue.js - Part 3]({% post_url 2015-10-10-create-pos-system-in-vuejs-part-3 %})