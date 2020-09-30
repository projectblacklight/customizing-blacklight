---
layout: page
title: Configuring Sort Fields
permalink: /v7.10.0/sort_fields/
blacklight_version: v7.10.0
---

Blacklight provides a UI element to choose a sort order for results when multiple sort options are configured.

<div class="image-well">
  <img src="/public/images/blacklight-7-sort-fields.png" alt="Blacklight sort field dropdown" />
  <div class="caption">Sort dropdown</div>
</div>

Sort options can be configured using the `add_sort_field` configuration method.  The following configuration won't display because there is only one sort option to choose from.

```ruby
configure_blacklight do |config|
  config.add_sort_field 'score desc', label: 'relevance'
end
```

Even though this will not display a sort option, it still controls the default sort behavior of search results.  It's often useful to supply sub sorts, even for a default score based relevancy search result as facet based results will all have the same score.  We can update the default sort of our results by updating the sort field configuration and adding pub date and title sub-sorts.  With the configuration below a facet only search (or simply an empty query) will be returned sorted.

```ruby
configure_blacklight do |config|
  config.add_sort_field 'score desc, pub_date_si desc, title_si asc', label: 'relevance'
end
```

Adding additional sort fields is relatively straight forward. As above with the relevancy sort, it is most likely a good idea to provide sub sorts so when documents have the same relevancy score they are sorted using other criteria.

```ruby
configure_blacklight do |config|
  config.add_sort_field 'score desc, pub_date_si desc, title_si asc', label: 'relevance'
  config.add_sort_field 'pub_date_si desc, title_si asc', label: 'year'
end
```

The first value passed to `add_sort_field` will be both the sort value sent to solr as well as the parameter sent through the application url. It's possible to obscure the solr fields and their sort direction from the url by passing a key as the first parameter and passing the sort configuration explicitly as the `sort` key.

```ruby
configure_blacklight do |config|
  config.add_sort_field 'relevance', sort: 'score desc, pub_date_si desc, title_si asc', label: 'relevance'
  config.add_sort_field 'year', sort: 'pub_date_si desc, title_si asc', label: 'year'
end
```

Using this configuration the sort url parameter will be `relevance` and `year` respectively and would allow the sort configuration to change the fields to be sorted without breaking existing urls/bookmarks, etc.
