---
layout: page
title: Updating the home page
permalink: /v8.0.0.alpha/first_change/
blacklight_version: v8.0.0.alpha
---

One of the first things that that you will want to customize in your Blacklight installation is the home page. The home page that ships with Blacklight is not meant for end users, but is intended help orient a new developer who is create a new Blacklight site.

<div class='image-well'>
  <img src='/public/images/blacklight7-homepage.png' alt='Blacklight Homepage' />
  <div class='caption'>Default blacklight homepage</div>
</div>

One of the most basic methods of customizing any Rails engine, including Blacklight, is to override the engine-provided files with our own.  If we create a file with the same name under the same directory structure in our application then we have `overriden` that file and the one from our application will be served up instead.

In order to customize the home page text for our new site we will `override` the Blacklight-provided partial. We can find the content of the home page text in the Blacklight codebase at `app/views/catalog/_home_text.html.erb` ([GitHub](https://github.com/projectblacklight/blacklight/blob/master/app/views/catalog/_home_text.html.erb)).

If we add the following content to our newly created file and refresh the browser we can see that our file is served up as the home page text instead of the file that shipped with Blacklight.

{% highlight html %}
<h2>Welcome to my new Blacklight app!</h2>

<p>Please contact us if you have any questions.</p>
{% endhighlight %}

<div class='image-well'>
  <img src='/public/images/blacklight7-updated-homepage.png' alt='Updated Homepage' />
  <div class='caption'>Updated homepage</div>
</div>

## Note

It's important to note that overriding files is a very powerful tool, but it can also be dangerous and a challenge to maintain when updating Blacklight. If you override a partial, we'd strongly suggest also writing an integration test to exercise your customization. When you upgrade Blacklight, you should review any upstream changes to determine whether you need to manually update your file with the upstream modifications. If you don't do this, you could miss out on any bug fixes or new features that were added to the file.

 **We cannot stress enough that you only want to override the smallest piece possible. DO NOT copy all of the Blacklight views into your application or you're going to have a bad time.**
