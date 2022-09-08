---
layout: page
title: Updating the home page
permalink: /v8.0.0.alpha/first_change/
blacklight_version: v8.0.0.alpha
---

One of the first things that that you will want to customize in your Blacklight installation is the home page. The home page that ships with Blacklight is not meant for end uses and is instead intended to introduce the developer to the concepts of beginning a new Blacklight site.

<div class='image-well'>
  <img src='/public/images/blacklight7-homepage.png' alt='Blacklight Homepage' />
  <div class='caption'>Default blacklight homepage</div>
</div>

In order to customize the home page text for our new site we will use `override` Blacklight's rails partial for the home page text.  This is one of the most basic methods of customization.

We can find the content of the home page text in the Blacklight codebase at `app/views/catalog/_home_text.html.erb` ([GitHub](https://github.com/projectblacklight/blacklight/blob/master/app/views/catalog/_home_text.html.erb)). If we create a file with the same name under the same directory structure in our application then we have `overriden` that file and the one from our application will be served up instead.  **We cannot stress enough that you only want to override the smallest piece possible. DO NOT copy all of the Blacklight views into your application or you're going to have a bad time.**

If we add the following content to our newly created file and refresh the browser we can see that our file is served up as the home page text instead of the file that shipped with Blacklight.

{% highlight html %}
<h2>Welcome to my new Blacklight app!</h2>

<p>Please contact us if you have any questions.</p>
{% endhighlight %}

<div class='image-well'>
  <img src='/public/images/blacklight7-updated-homepage.png' alt='Updated Homepage' />
  <div class='caption'>Updated homepage</div>
</div>

You have now used one of the most basic customization techniques to `override` one of Blacklight's view partials to deliver your own custom content on the home page.
