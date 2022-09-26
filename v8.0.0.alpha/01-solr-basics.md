---
layout: page
title: Solr basics
permalink: /v8.0.0.alpha/solr/
blacklight_version: v8.0.0.alpha
---

Blacklight uses Apache Solr as its data index [^1], but does not care how you run or index data into your instance. For this workshop, we're starting with the same set of configuration files that Blacklight uses for its own testing. In this section, we'll take a look at some commonly used tools and approaches.


## `solr_wrapper`

`solr_wrapper` is a tool that can be used to download, run, and configure a Solr instance, and is often used as part of an application's test suite to ensure a clean slate.

In a separate terminal window within the application's directory, run:
```sh
% bundle exec solr_wrapper
```

Solr should now be running on [http://localhost:8983/](http://localhost:8983/). If we take a look at the solr admin, we can see there's an empty `blacklight-core` collection with our preferred solr configuration.

### What did solr_wrapper do?

`solr_wrapper` is a handy tool for development because it ensures the application starts from a clean slate. It's configured in the application's `solr_wrapper.yml` file.

```yaml
# Place any default configuration for solr_wrapper here
# port: 8983
collection:
  dir: solr/conf/
  name: blacklight-core
```

The `collection` section tells `solr_wrapper` to use the files in the `solr/conf` directory as the configuration directory for the `blacklight-core` collection.

## Indexing fixture data

Now that we have Solr up and running, we can index some data. Blacklight provides some out-of-the-box fixture objects that allow us to quickly index data:

```sh
% bin/rake blacklight:index:seed
```

If we send a query using the solr admin UI, we should see 30 freshly indexed documents.

## Using traject

The Blacklight web application is not strongly opinionated about how data are indexed into Solr. Many library-adjacent applications use a tool called [traject](https://github.com/traject/traject/), which we have available as part of the blacklight-marc gem dependencies.

In this example, we've pre-configured a traject indexer that handles MARC records in `app/models/marc_indexer.rb` [^2].

The `blacklight-marc` gem also includes some example MARC data (coincidentally, this data is also the source of the fixture data above).

```sh
% find $( bundle show blacklight-marc ) -name "*.mrc"
/path/to/gems/blacklight-marc-8.0.0/test_support/data/test_data.utf8.mrc
```

```sh
% bin/rake solr:marc:index MARC_FILE=/path/to/blacklight-marc-8.0.0/test_support/data/test_data.utf8.mrc
```

We can use this tool to index any MARC data we have available.

## Querying solr

Now that we have some data in Solr, we can query solr and get results back. Try some of these:

- [...?q=politics](http://127.0.0.1:8983/solr/blacklight-core/select?q=politics)
- [...?facet=true&facet.field=lc_1letter_ssim](http://127.0.0.1:8983/solr/blacklight-core/select?facet=true&facet.field=lc_1letter_ssim)

These query parameters, and more, are documented in the [Solr Reference Guide](https://lucene.apache.org/solr/guide/latest/overview-of-searching-in-solr.html). Blacklight, for the most part, is just presenting a framework and a basic user interface for querying solr, so it is important to get to know how to manipulate Solr for indexing and querying to provide a good search experience.

<hr />

[^1]: Although some institutions have had luck creating adapters to work with ElasticSearch or bespoke search APIs, covering these use cases is outside the scope of this workshop.
[^2]: The details about the MARC format, traject, and this indexer are outside the scope of this tutorial; for now, it's enough to know we have a way to get some data into the index.
