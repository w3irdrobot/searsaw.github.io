---
id: 422
title: Persisting Guest Users in Rails with Devise
author: Alex Sears
layout: post
guid: http://alexsears.com/?p=422
permalink: /article/persisting-guest-users-rails-devise
dsq_thread_id:
  - 2940402187
categories:
  - Article
tags:
  - Devise
  - Ruby
---
Some sites allow guest users to still interact with it. [Kandid.ly][1] allows you to like a photo, for example. [CodePen][2] and [JSFiddle][3] allow you to create pieces of code without having an account. Devise by default doesn&#8217;t have anything in it for having guest users on your site. Adding this functionality is not a difficult thing to do.

<!--more-->

In fact, [the Devise wiki][4] has an article on how to do this exact thing! However, that article talks about how to persist that user only for the current session. This session goes away when the user closes their browser. What if we want to persist those sessions for longer than the current session? We use a cookie!

## The Code

{% highlight ruby %}
module AuthorizationHelper

  # if user is logged in, return current_user, else return guest_user
  def current_or_guest_user
    if current_user
      if cookies.signed[:guest_user_email]
        logging_in
        guest_user.delete
        cookies.delete :guest_user_email
      end
      current_user
    else
      guest_user
    end
  end

  # find guest_user object associated with the current session,
  # creating one as needed
  def guest_user
    # Cache the value the first time it's gotten.
    @cached_guest_user ||=
      User.find_by!(email: (cookies.permanent.signed[:guest_user_email] ||= create_guest_user.email))

  # if cookies.signed[:guest_user_email] invalid
  rescue ActiveRecord::RecordNotFound #
    cookies.delete :guest_user_email
    guest_user
  end

  private

  # called (once) when the user logs in, insert any code your application needs
  # to hand off from guest_user to current_user.
  def logging_in
    # put all your processing for transferring
    # from a guest user to a registered user
    # i.e. update votes, update comments, etc.
  end

  # creates guest user by adding a record to the DB
  # with a guest name and email
  def create_guest_user
    u = User.create(:first_name => "guest", :email => "guest_#{Time.now.to_i}#{rand(99)}@example.com")
    u.skip_confirmation!
    u.save!(:validate => false)
    u
  end

end
{% endhighlight %}

If you put this in the `app/helpers`, the method `current_or_guest_user` will be available in your views. If you want to use it in your controller, you need to include the module in your application controller like the code below.

{% highlight ruby %}
class ApplicationController < ActionController::Base
  include AuthorizationHelper

  # ...the rest of your code
{% endhighlight %}

I tried to comment the code to explain it a bit. Here&#8217;s a quick explanation of whats going on. `current_or_guest_user` will check if `current_user` exists. If not, it fires the `guest_user` method. This method checks if the user has been cached on the controller yet. If so, it returns that. If not, it gets the user email from a signed cookie. If the cookie doesn&#8217;t exist, then we create a new user using the `create_guest_user` and save their email in the cookie. Then we cache that user in case we try to get the guest user again in this request.

If the user is signed in, then we check if the cookie exists. If not, we just return the current user. If the cookie does exist, it fires the `logging_in` method, deletes the guest user entry in the database, deletes the cookie, and then returns the current user. The `logging_in` method is an important one. This is where you write your logic to transfer all of the guest user values over to the currently logged in user. For example, if the guest user had liked a bunch of photos on your site, you would want to move all those likes to the current user. It&#8217;s just that easy!

## Wrapping Up

Adding guest users can be very simple using the Devise gem for Rails. The code Devise gives on its wiki is good, but their implementation only allows for same session persistence. Sometimes we need a bit more! Let me know what you think in the comments!

 [1]: https://kandid.ly
 [2]: http://codepen.io/
 [3]: http://jsfiddle.net/
 [4]: https://github.com/plataformatec/devise/wiki/How-To:-Create-a-guest-user