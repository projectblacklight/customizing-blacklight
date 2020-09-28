---
layout: page
title: Model, View, Controller, and Rails Engines.
permalink: /v7.10.0/mvc-routing-engines
blacklight_version: v7.10.0
---

Blacklight is a [Ruby on Rails engine](https://guides.rubyonrails.org/engines.html). This means it can be added and used within an existing Ruby on Rails application, or be used to generate applications that are standalone.

Blacklight "plugins" like GeoBlacklight, Blacklight Range Limit, and Spotlight are also Rails engines and use the engine pattern to add additional functionality to Blacklight.

For more information on the relationship between Blacklight and Ruby on Rails, the Blacklight Wiki has page dedicated to how these interact, [Understanding Rails and Blacklight](https://github.com/projectblacklight/blacklight/wiki/Understanding-Rails-and-Blacklight).

## Let's add a new controller

Because Ruby on Rails is a model, view, controller framework, and Blacklight is a self-contained Rails engine, lets extend our application to add some custom functionality that is outside the scope of Blacklight.

First, let's add a new controller which can serve static pages for us.

```sh
bin/rails g controller StaticPages home --no-helper --no-assets -t=rspec
```

Restart your Rails application (CTRL+C and then rerun using up arrow). You should be able to see this new page at [http://127.0.0.1:3000/static_pages/home](http://127.0.0.1:3000/static_pages/home). You will notice that the Blacklight search and navigation bars are also present on this page. This is because our new controller uses the generated Blacklight layout. This is configurable and you could change it, but we will leave it for now.

Now, let's change how the Blacklight engine is mounted so that the url is located for this page at `/home`.

Edit your `config/routes.rb` file to make these changes.

```diff
- root to: "catalog#index"
+ root to: "static_pages#home"
```

When you navigate to [http://127.0.0.1:3000](http://127.0.0.1:3000) you should see your StaticPages#home homepage. 

<div class='image-well'>
  <img src='{{ site.baseurl }}/public/images/custom-home-page.jpg' alt='Custom Blacklight Homepage' />
  <div class='caption'>Custom Blacklight homepage</div>
</div>
