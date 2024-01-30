---
layout: page
title: Configuring Search Fields
permalink: /v8.0.0.alpha/search_fields/
blacklight_version: v8.0.0.alpha
---

Blacklight provides a UI element to choose a which "search fields" to execute the search against when multiple search field options are configured.  This is often used to provide a `Title`, `Author`, etc. search in addition to the default `All fields`.  You can think of these as different request handlers (`qt` parameter) in solr, however; it's not uncommon to use a single request handler and configure the solr_parameters directly in the search field configuration.

<div class="image-well">
  <img src="/public/images/blacklight-7-search-fields.png" alt="Blacklight search fields dropdown" />
  <div class="caption">Search fields dropdown</div>
</div>

If there aren't any search fields configured (or one default one like below) the `qt` parameter set in the Blacklight config's `default_solr_parameters` will be used and there will be no search field dropdown presented to the user.

```ruby
# app/controllers/catalog_controller.rb
configure_blacklight do |config|
  config.add_search_field 'all_fields', label: 'All Fields'
end
```

Adding an new search field to the dropdown can be accomplished by using the `add_search_field` configuration option.  By default, the key passed to `add_search_field` will be passed to solr as the `qt` parameter.  In the example below, this would use the `title` request handler by passing `qt=title` when querying solr (when the user chooses "Title" for the search field dropdown).

```ruby
# app/controllers/catalog_controller.rb
configure_blacklight do |config|
  config.add_search_field 'all_fields', label: 'All Fields'
  config.add_search_field 'title', label: 'Title'
end
```

It's possible to use a different request handler than the key passed to `add_search_field`.  This way the `search_field` parameter in the application URL will remain `title` while the solr request handler that maps to remains know to the application configuration only (allowing you to change the request handler without breaking existing urls/bookmarks).

```ruby
# app/controllers/catalog_controller.rb
configure_blacklight do |config|
  config.add_search_field 'all_fields', label: 'All Fields'
  config.add_search_field 'title', label: 'Title' do |field|
    field.qt = 'internal_title_request_handler_name'
  end
end
```

A common pattern is to configure `solr_parameters` directly in the `add_search_field` configuration (and use the default search results handler).  You will likely want to pass `qf` and `pf` values that are defined in your default request handler.

```ruby
# app/controllers/catalog_controller.rb
configure_blacklight do |config|
  config.add_search_field 'all_fields', label: 'All Fields'
  config.add_search_field 'title', label: 'Title' do |field|
    field.solr_parameters = {
      qf: 'title_tsim full_title_tsim short_title_tsim alternative_title_tsim',
      pf: ''
    }
  end
end
```

It's also possible to use `qf`/`pf`s that are configured in your request handler by using the name wrapped in `${}` (e.g. `qf: '${title_qf}'`).

```xml
<requestHandler name="search" class="solr.SearchHandler" default="true">
  <lst name="defaults">
    ...
    <str name="title_qf">
      title_tsim
      full_title_tsim
      short_title_tsim
      alternative_title_tsim
    </str>
    <str name="title_pf">
    </str>
    ...
  </lst>
  ...
</requestHandler>
```
