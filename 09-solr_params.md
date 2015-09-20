---
layout: page
title: Solr Parameters
permalink: /solr_params/
---

The `catalog_controller.rb` can also be used to send custom parameters to Solr. There are several ways to implement custom Solr queries, this tutorial will focus on a way using the `CatalogController`.

`CatalogController` is a Rails controller and thus can take advantage of [filters](http://guides.rubyonrails.org/action_controller_overview.html#filters) and other Rails controller behavior. "Filters are methods that are run before, after or "around" a controller action." We can use this functionality to add customizations to our Solr request.

For example, let's take a look at our "Language" facet. Solr is returning results from 12 different languages, but what if we want results limited to "Japanese"?

<div class='image-well'>
  <img src='{{ site.baseurl }}/public/images/default-language-facet.jpg' alt='Default language facet' />
  <div class='caption'>Default "Language" facet</div>
</div>

We can limit these results by using a filter in the `catalog_controller.rb` file. Here is how to do that:

Create a private method (or set of methods) that will tap in to the Solr search parameters.

{% highlight ruby %}
# app/controllers/catalog_controller.rb (LN 182)
...
  private
  def add_limit_language_behavior
    self.solr_search_params_logic << :limit_language
  end
  def limit_language(solr_params, user_params)
    solr_params[:fq] << '{!raw f=language_facet}Japanese'
  end
end
{% endhighlight %}

`add_limit_language_behavior` method adds the `limit_language` to the logic that processes the Solr search parameters. `limit_language` then adds a [filter query](https://wiki.apache.org/solr/CommonQueryParameters#fq) to limit results to documents that have "Japanese" in the `language_facet` field.

Next we need to activate that method as a "before action" in the controller, for the "index" action only.

{% highlight ruby %}
# app/controllers/catalog_controller.rb
# -*- encoding : utf-8 -*-
class CatalogController < ApplicationController

  include Blacklight::Catalog
  before_action :add_limit_language_behavior, only: :index
  ...
{% endhighlight %}

The language limiting query is now added to the Solr query, which in turn returns only results with "Japanese" in the `language_facet` field.

<div class='image-well'>
  <img src='{{ site.baseurl }}/public/images/custom-language-facet.jpg' alt='Custom language facet' />
  <div class='caption'>Custom "Language" facet</div>
</div>

Tapping into `solr_search_params_logic` can be used to customize search results from Solr programmatically.