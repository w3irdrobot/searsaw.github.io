---
id: 130
title: Easy Site Migration Using cURL
author: Alex Sears
layout: post
guid: http://alexsears.com/?p=130
permalink: /article/easy-site-migration-using-curl
dsq_thread_id:
  - 1300437858
categories:
  - Article
tags:
  - Command Line
  - Linux
---
Any web developer will tell you that moving a website from one server to another or creating a local development environment based on an existing site is not always an easy task. With many of these giant frameworks (WordPress, Drupal, Laravel, Symfony, etc.) gaining popularity, it will be necessary for a developer to know how to migrate a site quickly and effectively. Using FileZilla is effective but can take way too long.

<!--more-->

I have found the best way is by zipping all the files into a nice and compact compression file and then using cURL on the target machine to download the zip file. Then unzip it! Let&#8217;s go through each of these steps below.

Log onto the production server.

{% highlight bash %}
ssh username@production.com
{% endhighlight %}

Move into the site folder. The name of the folder may be different depending on your host.

{% highlight bash %}
cd www
{% endhighlight %}

Zip up all the files into a file called `transfer.zip`. To make sure you get all the files, even the hidden ones such as `.htaccess` files, make sure you use `.` as the directory to start at. Notice we are also going to recursively zip the files so that we get all the files in all the directories.

{% highlight bash %}
zip -r transfer.zip .
{% endhighlight %}

Now log onto the target server. If you are using a local environment, then you can disregard this step.

{% highlight bash %}
ssh username@development.com
{% endhighlight %}

Navigate to the folder that will be used for the development environment and download the `transfer.zip` from the production server using cURL.

{% highlight bash %}
cd public_html/example_dev.com
curl -o transfer.zip http://example.com/transfer.zip
{% endhighlight %}

This will download the `transfer.zip` to the development server. Now we just need to unzip it!

{% highlight bash %}
unzip transfer.zip
{% endhighlight %}

There you have it. All your files have been migrated to a new development environment for you to test new additions to your site.

## Wrapping Up

Site migration does not have to be a dreadful thing that takes hours. Using this simple method, development environments can be set up with ease, leaving you and your team with more time to spend on projects that make you money! And we all love money, right?

## Further Reading

  * [Linux `zip` Command][1]
  * [Linux `unzip` Command][2]
  * [`cURL` Man Page][3]
  * [Migrating Website Files With SSH and ZIP &#8211; Ideas and Pixels][4]

 [1]: http://linux.about.com/od/commands/l/blcmdl1_zip.htm
 [2]: http://linux.about.com/od/commands/l/blcmdl1_unzip.htm
 [3]: http://curl.haxx.se/docs/manpage.html
 [4]: http://ideasandpixels.com/migrating-website-files-with-ssh-and-zip