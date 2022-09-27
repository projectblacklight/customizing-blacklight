---
layout: page
title: Starting the Rails application
permalink: /v8.0.0.alpha/starting-rails/
blacklight_version: v8.0.0.alpha
---

A pre-requisite for this guide is that you have a working Rails application with Blacklight installed into it. If you don't have a Rails application, you can follow the [Rails Getting Started Guide](https://guides.rubyonrails.org/getting_started.html) to get started. Once you have create a Rails application, you can follow the [Blacklight Getting Started Guide](https://github.com/projectblacklight/blacklight/wiki/Quickstart) to install Blacklight into your application.

1. Append this line to your application's Gemfile:
{% highlight ruby %}
gem 'blacklight', ">= 8.0.0.alpha"
{% endhighlight%}

then, update the bundle:

{% highlight sh %}
$ bundle install
{% endhighlight %}

2. Run the Blacklight install generator:

{% highlight sh %}
$ rails generate blacklight:install --marc --solr_version=latest
{% endhighlight %}

3. Run the database migrations:

{% highlight sh %}
$ rails db:migrate
{% endhighlight %}

4. Start the Rails server:

{% highlight sh %}
$ rails server
{% endhighlight %}
