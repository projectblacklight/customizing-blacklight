---
layout: page
title: Sort Fields
permalink: /sort_fields/
---

Blacklight provides a dropdown UI element for changing the sort order of search results. The sort options will show up in the dropdown in the order they're configured, and the first one is the default sort if no sort key is present in the url.

<div class='image-well'>
  <img src='{{ site.baseurl }}/public/images/sort-dropdown.png' alt='Sort dropdown' />
  <div class='caption'>Default sort dropdown</div>
</div>

I would recommend explicitly adding a key (`year` in the example below) so that the sort fields are changeable without breaking existing/bookmarked urls.

{% highlight ruby %}
# app/controllers/catalog_controller.rb
config.add_sort_field('year') do |field|
  field.sort = 'pub_date_sort desc, title_sort asc'
  field.label = 'pub. date'
end
{% endhighlight%}

<div class='image-well'>
  <img src='{{ site.baseurl }}/public/images/updated-sort-dropdown.png' alt='Sort dropdown' />
  <div class='caption'>Updated sort dropdown</div>
</div>

The `pub. date` sort option will pass `sort=year` in the url and the logic of what a year sort means is held in the sort field configuration (and can be changed without affecting the url parameters).

You can now customize your sort options as well as mask the solr fields that are being sorted on in order to prevent breaking bookmarked urls if the fields change.
