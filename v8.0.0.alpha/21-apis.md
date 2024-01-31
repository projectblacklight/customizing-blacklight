---
layout: page
title: Blacklight APIs
permalink: /v8.0.0.alpha/apis/
blacklight_version: v8.0.0.alpha
---

Blacklight has out-of-the-box support for some APIs that make integrating it with other applications quick and easy.

## RSS / Atom

Blacklight provides ATOM + RSS feeds for search results. Just add `.atom` (or `.rss`) on to the end of the request. By default, the content of the feed will include the HTML output for the record.

By default, the Atom feed includes the output of the e.g. `catalog/_index.html.erb` partial as the "post" summary.

## JSON API

Blacklight now provides a JSON API -flavored response for search results and documents. This is good if you want to integrate Blacklight with frameworks that can consume this format. The response includes enough data to construct a reasonable facsimile of the out-of-the-box interface.

## Response formats

Blacklight can also provide access to non-HTML data formats for search results, documents, and Atom feeds. This can be useful for putting together a basic data API.

### Single documents

The document response can provide access to alternative presentations (like raw data). In this example, we're already exposing the original MARC records (thanks to the blacklight-marc gem).

- [http://127.0.0.1:3000/catalog/00282214.marc](http://127.0.0.1:3000/catalog/00282214.marc)
- [http://127.0.0.1:3000/catalog/00282214.marcxml](http://127.0.0.1:3000/catalog/00282214.marcxml)

We can add a JSON MARC flavor to this by registering a mime type and extension with rails and then telling Blacklight how to provide that format. First, we add a new MIME type in `config/initializers/mime_types.rb`:

```diff
+ Mime::Type.register "application/marc+json", :marcjson
```

Then, we tell Blacklight how to transform a document into marcjson in `app/models/solr_document.rb`:

```diff
class SolrDocument
  include Blacklight::Solr::Document
+
+   def initialize(*args)
+     super
+     will_export_as(:marcjson)
+   end
+
+   def export_as_marcjson
+     to_marc.as_json
+   end
end
```

Then, after restarting your Rails application server, you can get back the MARC record in a json format:
http://127.0.0.1:3000/catalog/00282214.marcjson

### Search results

You can also affect which search results response formats are available, in addition to the out-of-the-box HTML, JSON, Atom and RSS feeds. Take a look at, e.g.:

- [http://127.0.0.1:3000/catalog?search_field=all_fields&q=&format=marc](http://127.0.0.1:3000/catalog?search_field=all_fields&q=&format=marc)
- [http://127.0.0.1:3000/catalog?search_field=all_fields&q=&format=atom&content_format=marcxml](http://127.0.0.1:3000/catalog?search_field=all_fields&q=&format=atom&content_format=marcxml)

Note that this can be used in combination with any search query, facets, or sort parameters.

#### Custom response formats

To add a different API format, you can instruct Blacklight to provide alternative results. In `app/controllers/catalog_controller.rb`, add:

```diff
# app/controllers/catalog_controller.rb
 configure_blacklight do |config|
+   config.index.respond_to.csv = true
 end
```

Next, we need to provide a template to produce the CSV output. In `app/views/catalog/index.csv.erb`, add:

```erb
id
<% @response.documents.each do |document| %>
<%= document.id %>
<% end %>
```

Now you can request the CSV output of all the ids:
[http://127.0.0.1:3000/catalog.csv?search_field=all_fields&q=](http://127.0.0.1:3000/catalog.csv?search_field=all_fields&q=)
