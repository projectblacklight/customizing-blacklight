---
layout: page
title: Catalog helper_method usage
permalink: /v7.10.0/catalog-helper-method-usage/
blacklight_version: v7.10.0
---

We previously saw how powerful the configuration options are in the `CatalogController`. Now we will learn about one more configuration option: `helper_method`.

The `helper_method` configuration option allows us to customize how values will be displayed.

For instance, let's say we would like to add a search link for the title to an independent bookstore purchase page. We could do this using the `helper_method` configuration option.

In our `catalog_controller.rb` file lets add the configuration to use a helper method for the `add_show_field` configuration for `title_tsim`.

{% highlight ruby %}
# app/controllers/catalog_controller.rb
config.add_show_field 'title_tsim', label: 'Title', helper_method: :bookstore_search
{% endhighlight %}

Now we need to create this method. For convenience let's do this in `ApplicationHelper` but this could be organized differently.

`catalog_helper` methods receives a hash argument that contains the following keys: `[:document, :field, :config, :value]`.


{% highlight ruby %}
# app/helpers/application_helper.rb
module ApplicationHelper
  def bookstore_search args
    puts args.keys.inspect
    # [:document, :field, :config, :value]
  end
end
{% endhighlight %}

To return a link to a bookstore search, lets update this code to include a link for each value.

{% highlight ruby %}
# app/helpers/application_helper.rb
module ApplicationHelper
  def bookstore_search args
    safe_join(args[:value].map do |value|
      link_to(value, "https://bookshop.org/books?keywords=#{value}")
    end, ',')
  end
end
{% endhighlight %}

We now have each title on the show page linked to a search for Bookshop.org.

<div class="alert alert-primary">
  For more information about the <code>helper_method</code> configuration, <a href="https://github.com/projectblacklight/blacklight/wiki/Blacklight-configuration#using-a-helper-method-to-render-the-value">checkout the Blacklight Wiki</a>.
</div>
