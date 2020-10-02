---
layout: page
title: Extending the CatalogController
permalink: /v7.11.1/extending-catalog-controller/
blacklight_version: v7.11.1
---

In (Model, View, Controller, and Rails Engines)[/v7.11.1/mvc-routing-engines], we created a separate home page controller that displays only static text. Now we'd like to put a selection of facet fields on that page.

First, we need to pull in the Blacklight configuration from the `CatalogController`, query Solr, and render the facets. The most expedient way to do that, perhaps, is to change the `StaticPagesController` to have it inherit from `CatalogController` (pulling in the Blacklight configuration automatically, and, through Rails magic, setting everything up so we can easily render the same partials):

```diff
- class StaticPagesController < ApplicationController
+ class StaticPagesController < CatalogController
```

If we try to look at the home page now, we'll see a routing error (`ActionController::UrlGenerationError in StaticPages#home`). We'll also need to tell the `StaticPagesController` we want to use the catalog routes from `CatalogController` [^1]. By convention, Blacklight uses the `search_action_url` method to route to the correct place. We can override that in the controller:

```diff
class StaticPagesController < CatalogController
+  alias_method :search_action_url, :search_catalog_url
```

Next, we need to update the controller to make a solr query and store the result (again, by convention) in the instance variable `@response`. We can copy the way Blacklight does this for the [`CatalogController#index` action](https://github.com/projectblacklight/blacklight/blob/master/app/controllers/concerns/blacklight/catalog.rb#L27) [^2]:

```diff
class StaticPagesController < CatalogController
  ...
  def home
+   (@response, _) = search_service.search_results
  end
end
```

Then, we need to render some facets into the view. In `app/views/static_pages/home.html.erb`, we can have Rails render the normal `facets` partial:

```ruby
<% content_for(:sidebar) do %>
  <%= render 'facets' %>
<% end %>

<h1>StaticPages#home</h1>
<p>Find me in app/views/static_pages/home.html.erb</p>
```

Now that we have facets rendering, we might choose a subset of the full result (due to user experience research suggesting a couple common entry points, or for performance reasons). To do this, we can re-open the search configuration and re-arrange it however we want, say to limit the facets to just format and publication date:

```ruby
class StaticPagesController < CatalogController
  configure_blacklight do |config|
    config.facet_fields.slice!('format', 'pub_date_ssim')
  end
```

`config.facet_fields` is just an ordinary Ruby hash, so we can mutate it by adding, changing, or removing values.

<hr />

[^1]: We might want the opposite if we wanted our controller to be a specialization of `CatalogController` (perhaps providing a filtered "ebooks" view or an administration view with a specialized display and tooling). If we wanted to do that, we'd add search routing to the controller in `./config/routes.rb`.

[^2]: This index method does a little more than we need to do to add a few facets. Note, too, the `deprecated_document_list` argument coming back from the search service; in Blacklight 8, this method will return only the response.
