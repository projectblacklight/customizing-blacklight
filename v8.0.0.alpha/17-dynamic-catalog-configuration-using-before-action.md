---
layout: page
title: Dynamic catalog configuration using before_actions
permalink: /v8.0.0.alpha/dynamic-catalog-configuration-using-before_actions/
blacklight_version: v8.0.0.alpha
---

We previously saw [how to configure the `CatalogController`](/v8.0.0.alpha/metadata_fields/) using the Blacklight config DSL. These configuration patterns work for many different scenarios. However, you may want to provide different a different configuration based on dynamic conditions, such as the route being visited, or the status of a user.

Using [Ruby on Rails `before_action`](https://guides.rubyonrails.org/action_controller_overview.html#filters) as a block in the `catalog_controller.rb` can enable you to add these customizations.


{% highlight ruby %}
# app/controllers/catalog_controller.rb
class CatalogController < ApplicationController

  include Blacklight::Catalog
  include Blacklight::Marc::Catalog

  before_action do
    if current_user&.admin?
      blacklight_config.add_facet_field :admin_only_facet
    end
  end

  configure_blacklight do |config|
{% endhighlight %}
