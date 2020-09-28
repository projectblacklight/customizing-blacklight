---
layout: page
title: Search Fields
permalink: /v5.14.0/search_fields/
redirect_from: /search_fields/
blacklight_version: v5.14.0
---

Blacklight supports fielded search out of the box.  There is a dropdown next to the search field that allows the user to choose if they want to search all or a limited set of fields (e.g. Title, Author, etc). The ability to customize this is of particular interest to those in the Hydra-sphere as it is configured for Blacklight's out-of-the-box defaults which aren't necessarily compatible with what you'll probably be using in your Hydra application.

<div class='image-well'>
  <img src='/public/images/fielded-search-dropdown.png' alt='Blacklight search box' />
  <div class='caption'>Default fielded search options</div>
</div>

There is a lot of good information in the comments of the auto-generated Blacklight configuration about configuring the search fields, but we'll cover some of the basics.

## Labels

You can easily configure the labels of the search fields to meet the needs of your application.

{% highlight ruby %}
# app/controllers/catalog_controller.rb
config.add_search_field 'all_fields', label: 'Everything'
{% endhighlight %}

<div class='image-well'>
  <img src='/public/images/updated-search-dropdown.png' alt='Blacklight search box' />
  <div class='caption'>Updated fielded search options</div>
</div>

## Search Field URL Parameter

The first parameter sent to the configuration is the key that will be passed as the `search_field` url parameter. This is very useful because it allows you to update the search logic underneath a particular fielded search without breaking existing/bookmarked urls.

## Solr Local Parameters

A common way of configuring the fielded search is to use solr local parameters. These are references to pf and qf configurations stored in solr so the actual configuration ends up being on the solr side instead.  This is how Blacklight configures its search fields out-of-the-box.

{% highlight ruby %}
# app/controllers/catalog_controller.rb
config.add_search_field('author') do |field|
  field.solr_local_parameters = {
    qf: '$author_qf',
    pf: '$author_pf'
  }
end
{% endhighlight %}

{% highlight xml %}
<!-- solrconfig.xml -->
<requestHandler>
  ...
  <str name="author_qf">
    author_unstem_search^200
    ...
  </str>
  <str name="author_pf">
    author_unstem_search^2000
    ...
  </str>
  ...
</requestHandler>
{% endhighlight %}

## Solr Parameters

You can explicitly define the solr parameters to send in your search field configuration by passing them in the `solr_parameters` configuration. In the configuration below `qf` and `pf` parameters are being directly sent to solr and boosting title and author fields to return only hits when the search term is found in those fields (and sorts title hits higher).

{% highlight ruby %}
config.add_search_field('title_author') do |field|
  field.label = 'Title + Author'
  field.solr_parameters = {
    qf: ['title_unstem_search^50000 author_unstem_search^200'],
    pf: ['title_unstem_search^500000 author_unstem_search^2000']
  }
end
{% endhighlight %}

## Removing From Dropdown

In some instances you may not want to include a particular search in the drop down but allow it to be linked to from metadata in the search results. This allows the breadcrumbs in the search results to use your configured label but doesn't pollute the dropdown with superfluous options.  This can be accomplished by adding setting `field.if = false` in your search field configuration.

{% highlight ruby %}
config.add_search_field('title_author') do |field|
  field.label = 'Title + Author'
  field.if = false
end
{% endhighlight %}

This item will not show up in the dropdown but passing `search_field=title_author` in the url will display the configured label in the breadcrumb and send the configured solr parameters with your request.

You have now configured the search field dropdown to use solr local parameters, directly passed solr parameters, and hidden them from user in cases where you don't want to pollute the dropdown.
