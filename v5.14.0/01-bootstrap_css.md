---
layout: page
title: Bootstrap CSS
permalink: /v5.14.0/bootstrap_css/
redirect_from: /bootstrap_css/
blacklight_version: v5.14.0
---

Blacklight uses the [Bootstrap](http://getbootstrap.com/) framework for much of its user interface. This layout and UI integration is done using the [bootstrap-sass](https://github.com/twbs/bootstrap-sass) gem. Using bootstrap-sass allows Blacklight adopters to easily style and enhance their applications, using DRY principles and with minimal version upgrade pains.

One of the first things to do when customizing the styling of a Blacklight application is removing the `application.css` and creating a clean `application.scss` file. This will allow you to take advantage of the powerful features of SASS, and rather than implicitly requiring all files in the `app/assets/stylesheets` directly, start explicitly importing ones that you need.

{% highlight bash %}
$ rm app/assets/stylesheets/application.css
$ touch app/assets/stylesheets/application.scss
{% endhighlight %}

Next, import the `blacklight.css.scss` into your `application.scss` file.

{% highlight scss %}
// application.scss
@import 'blacklight';
{% endhighlight %}

Your application should still be styled with the Blacklight defaults.

<div class='image-well'>
  <img src='/public/images/default-styles.jpg' alt='Blacklight with default styling' />
  <div class='caption'>Blacklight with default styling</div>
</div>

The Bootstrap framework allows you to update your application's styling using variables, rather than overriding CSS. This helps for easier customization and longterm sustainability. Variable names that are customizable can be found on [Bootstrap's "Customize" page](http://getbootstrap.com/customize/).

Let's start with customizing the navbar size and color. In your `application.scss` file define the following variable before the Blacklight import:

{% highlight scss %}
// application.scss
$navbar-height: 80px;
$navbar-inverse-bg: #003268;

@import 'blacklight';

{% endhighlight %}

After saving and reloading your homepage, you should see that the navbar color and size has changed.

<div class='image-well'>
  <img src='/public/images/updated-navbar.jpg' alt='Blacklight with updated navbar' />
  <div class='caption'>Blacklight with updated navbar</div>
</div>

This technique for customizing your application works on many different parts of the UI including: buttons, links, responsive breakpoints, and fonts. After many customizations the `application.scss` file can become crowded, so we usually split out our Bootstrap variable customizations into a new file, and just import that back into our `application.scss`.

{% highlight scss %}
// bootstrap-variables.scss
$navbar-height: 80px;
$navbar-inverse-bg: #003268;
{% endhighlight %}

{% highlight scss %}
// application.scss
@import 'bootstrap-variables';
@import 'blacklight';
{% endhighlight %}

Using this approach should facilitate maintainability of your application's stylesheets. Modularity and SASS mixins are your friends :)
