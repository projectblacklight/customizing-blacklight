---
layout: page
title: Catalog helper_method usage
permalink: /v7.11.1/catalog-helper-method-usage/
blacklight_version: v7.11.1
---

We previously saw how we could display and configure field values in the `CatalogController`. What if we want to go further and change how the values are displayed? In this section, we'll look at two ways to control how document values are rendered.

## `helper_method`

In this example, we would like to add a search link for the title to an independent bookstore purchase page. Unlike the `accessor` or `values` parameters we saw earlier, this is less about how the data is mapped and more about how it is presented to the end user.

One quick way to influence the display of values is to use the `helper_method` parameter. The `helper_method` configuration option allows us to customize how values will be displayed by using a Rails helper.

In our `catalog_controller.rb` file, we can add the configuration to use a helper method for the `add_show_field` configuration for `title_tsim`.

{% highlight ruby %}
# app/controllers/catalog_controller.rb
config.add_show_field 'title_tsim', label: 'Title', helper_method: :bookstore_search
{% endhighlight %}

Now we need to create this method. For convenience, we can do this in `ApplicationHelper`:

{% highlight ruby %}
# app/helpers/application_helper.rb
module ApplicationHelper
  def bookstore_search document:, field:, config:, value:
    puts value.inspect
  end
end
{% endhighlight %}

To return a link to a bookstore search, lets update this code to include a link for each value.

{% highlight ruby %}
# app/helpers/application_helper.rb
module ApplicationHelper
  def bookstore_search value:
    safe_join(Array(value).map do |value|
      link_to(value, "https://bookshop.org/books?keywords=#{value}")
    end, ',')
  end
end
{% endhighlight %}

We now have each title on the show page linked to a search for Bookshop.org.

<div class="alert alert-primary">
  For more information about the <code>helper_method</code> configuration, <a href="https://github.com/projectblacklight/blacklight/wiki/Blacklight-configuration#using-a-helper-method-to-render-the-value">checkout the Blacklight Wiki</a>.
</div>

## Rendering pipeline

With the `helper_method` configuration, you get a lot of flexibility to render the field, but you also take on a lot of responsibility and it's easy to get out-of-sync with the rest of the fields. An alternative approach is to tap into Blacklight's "rendering pipeline" to add or modify the behavior. First, let's convert the helper method above.

We need a new rendering "step". For now, let's add a simple step in `app/models/bookstore_search.rb` to see the wiring work:

```
class BookstoreSearch < Blacklight::Rendering::AbstractStep
  def render
    next_step(['boo!'])
  end
end
```

and wire it up quickly as a `before_action` in our `CatalogController`:

```
before_action do
  Blacklight::Rendering::Pipeline.operations = [
    BookstoreSearch,
    # from the default pipeline:
    Blacklight::Rendering::HelperMethod,
    Blacklight::Rendering::LinkToFacet,
    Blacklight::Rendering::Microdata,
    Blacklight::Rendering::Join
  ]
end
```

When you do a search, you should see all the field values are replaced with 'boo!'.

Now, let's wire up the bookstore search behavior:

```
def render
  if config.bookstore_search
    next_step(values.map { |value| context.link_to(value, "https://bookshop.org/books?keywords=#{value}")  })
  else
    next_step(values)
  end
end
```

and then use it when we render our field:

```diff
- config.add_show_field 'title_tsim', label: 'Title', helper_method: :bookstore_search
+ config.add_show_field 'title_tsim', label: 'Title', bookstore_search: true
```

The rendering pipeline is a powerful tool to control how field values are displayed. In this simple example, the complexity is about the same either way, but for some real-world uses (say, wrapping multivalued fields in an HTML list, applying some HTML formatting to fields, etc) it can be convenient to separate the rendering logic this way.
