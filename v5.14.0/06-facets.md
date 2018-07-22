---
layout: page
title: Facets
permalink: /v5.14.0/facets/
redirect_from: /facets/
blacklight_version: v5.14.0
---

Blacklight allows you to customize your facet display in several ways out-of-the-box. Many of these techniques are covered in other parts of these tutorials but we'll cover the basics as they relate specifically to facets.

## Labels

Like other configurations in the `CatalogController` you can very easily modify the labels by updating the `label` key in the blacklight configuration.

{% highlight ruby %}
# app/controllers/catalog_controller.rb
config.add_facet_field 'format', label: 'Type'
{% endhighlight %}

## Limits

You can set the limit of the number of hits that will populate a facet (if not already explicitly set in solr). Once the limit is reached a `More` link will be rendered which will pop-up the rest of the hits paginated in a modal.

{% highlight ruby %}
# app/controllers/catalog_controller.rb
config.add_facet_field 'subject_topic_facet', label: 'Topic', limit: 20
{% endhighlight %}

## Helpers

You can pass the name of a method that is publicly available to your views/controllers as a symbol to the `helper_method`.  For the purpose of this tutorial we'll add the helper method to the `ApplicationHelper`.

{% highlight ruby %}
# app/controllers/catalog_controller.rb
config.add_facet_field 'language_facet', label: 'Language', helper_method: :render_language_facet_value
{% endhighlight %}

{% highlight ruby %}
# app/helpers/application_helper.rb
def render_language_facet_value(value)
  return value unless lookup_table.has_key?(value)
  lookup_table[value]
end
{% endhighlight %}

In this case there is a theoretical lookup table that holds a hash of possible translations for that value (e.g. identifiers, relator code, etc). The actual value in the solr field will be passed in url parameters but the value returned in your method will be presented to the user in the facet and the breadcrumb.

## Partial

One of the more powerful mechanisms for customizing facets is to configure a custom partial to handle rendering the facet.  This will allow the application to provide its own view to replace Blacklight's [facet_limit partial](https://github.com/projectblacklight/blacklight/blob/master/app/views/catalog/_facet_limit.html.erb).

{% highlight ruby %}
# app/controllers/catalog_controller.rb
config.add_facet_field 'format', label: 'Format', partial: 'custom_format_facet'
{% endhighlight %}

Now when you add a `_facet_limit.html.erb` partial at `app/views/catalog` this partial will be rendered for that facet in place of Blacklight's.  You'll want to make sure to retain the important parts of Blacklight's display logic in order to ensure the facet still works as intended. This will allow you to update the markup of the facet to add/remove classes and or add information.  In the example below there is a piece of text added to the top of the facet to display to the user.

{% highlight erb %}
# app/views/catalog/_facet_limit.html.erb
<div class='well'>
  Please select a format below
</div>

<ul class="facet-values list-unstyled">
  <% paginator = facet_paginator(facet_field, display_facet) %>
  <%= render_facet_limit_list paginator, field_name %>

  <% unless paginator.last_page? || params[:action] == "facet" %>
    <li class="more_facets_link">
      <%= link_to t("more_#{field_name}_html", scope: 'blacklight.search.facets', default: :more_html, field_name: facet_field.label),
        search_facet_url(id: field_name), class: "more_facets_link" %>
    </li>
  <% end %>
</ul>
{% endhighlight %}

The well you provided will now show up for the Format facet. You can use this technique to do some very heavy modification of your facets to provide various interesting interfaces.

<div class='image-well'>
  <img src='{{ site.baseurl }}/public/images/custom-facet.png' alt='Customized format facet' />
  <div class='caption'>Customized facet with text in a well</div>
</div>

You have now learned how to customize your facets in Blacklight in various ways. You can configure simple customizations as well as make advanced customizations by overriding the facet partials rendered by Blacklight.
