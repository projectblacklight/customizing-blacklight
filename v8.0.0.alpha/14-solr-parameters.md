---
layout: page
title: Solr parameters
permalink: /v8.0.0.alpha/solr-in-blacklight/
blacklight_version: v8.0.0.alpha
---

When we search in the application, we can see the search results from Solr.

## config/blacklight.yml

First, let's look at how to wire up Blacklight to send queries to your solr index. In `./config/blacklight.yml`, there's a configuration file giving a Solr URL per environment:

```yaml
development:
  adapter: solr
  url: <%= ENV['SOLR_URL'] || "http://127.0.0.1:8983/solr/blacklight-core" %>
...
```

## Querying Solr from Blacklight

Blacklight will use the parameters from Solr's `requestHandler`, but also needs to send some parameters from the application. We can see those parameters from the Rails `development.log` when we do a search:

```
Started GET "/" for 127.0.0.1 at 2020-09-29 12:40:55 -0700
Processing by CatalogController#index as HTML
Solr query: get select {"qt"=>nil, "facet.field"=>["format", "pub_date_ssim", "subject_ssim"], "facet.query"=>[], "facet.pivot"=>[], "fq"=>[], "hl.fl"=>[], "rows"=>10, "facet"=>true, "sort"=>"score desc"}
Solr fetch (86.2ms)
  Rendering ....
```

Note that we can see the same query in the solr logs...


### Raw document API

In development, it can be handy to see the stored fields for a document. While you could search solr and get back results, Blacklight also has an API endpoint for viewing the complete Solr document:

[http://127.0.0.1:3000/catalog/00282214/raw](http://127.0.0.1:3000/catalog/00282214/raw)

Note that this API is disabled by default, both to discourage coupling applications to the underlying Solr document format and to avoid leaking non-public information present in the document.

```ruby
# app/controllers/catalog_controller.rb
## Should the raw solr document endpoint (e.g. /catalog/:id/raw) be enabled
config.raw_endpoint.enabled = true
```

### Changing behavior with the `CatalogController`

We can add or change the default parameters sent to Solr in the `CatalogController`. In `./app/controllers/catalog_controller.rb`, we can see the default configuration sets the number of items per search result:

```ruby
# app/controllers/catalog_controller.rb
## Default parameters to send to solr for all search-like requests. See also SearchBuilder#processed_parameters
config.default_solr_params = {
  rows: 10
}
```

Try changing the value to 20.

We can also override behavior from the requestHandler. Try adding a `qf` parameter and tweaking the fields + weights:

```ruby
# app/controllers/catalog_controller.rb
## Default parameters to send to solr for all search-like requests. See also SearchBuilder#processed_parameters
config.default_solr_params = {
  rows: 20,
  qf: 'id title_tsim^100 author_tsim^20 all_text_timv',
}
```

Note that, although handy for development, using static parameters like this in production increases the size of the query, adds noise to the logs, and means what you see in Blacklight may diverge significantly from what you see querying solr directly unless you remember to include these additional parameters.

## Search builders

To get from an application request to the Solr parameters, Blacklight uses a `SearchBuilder` to map the configuration, user-supplied parameters, and any other run-time information into parameters that Solr will understand. The default behavior provides the necessary query parameters to drive the Blacklight user experience without requiring much, if any, pre-configuration of Solr.

Let's start with an easy tweak to add a Solr parameter. Uncomment the example in `app/models/search_builder.rb`:

```ruby
self.default_processor_chain += [:add_custom_data_to_query]

def add_custom_data_to_query(solr_parameters)
  solr_parameters[:custom] = blacklight_params[:user_value]
end
```

After making a request, see how the blacklight paramater `user_value` gets directly mapped to the solr parameter `custom`. Search builders are often used to vary the search results based on some parameter from the url or an attribute of the user (e.g. for access control). Let's tweak the example to give us a quick "author" search:

```ruby
self.default_processor_chain += [:add_author_only_qf]

def add_author_only_qf(solr_parameters)
  solr_parameters[:qf] = 'author_tsim' if blacklight_params[:author]
end
```
