---
layout: page
title: Configuring Metadata Fields
permalink: /v7.11.1/configuring_metadata/
blacklight_version: v7.11.1
---

The metadata fields that are displayed in the index (search results) and show (record view) can be configured using the `add_index_field` and `add_show_field` configuration methods in the Blacklight config.

```ruby
configure_blacklight do |config|
  # Index / Search Results
  config.add_index_field 'author_tsim', label: 'Author'

  # Show / Record View
  config.add_show_field 'author_tsim', label: 'Author'
end
```

The configuration patterns outlined on [General Configuration Patterns](/v7.11.1/config_patterns/) apply to these fields so you can use any of those to customize the display of these fields.

## Labeling via Internationalization (i18n)

All the text in Blacklight's UI are configured via rails' i18n files. While you can apply a string label directly in the Blackklight config, you can also label these in your i18n config files (e.g. in `config/locales/blacklight.en.yml`).

If you wanted to change the label of all `author_tsim` fields to "Creator" you can do that by adding the label to your locale file.

```yaml
en:
  blacklight:
    search:
      fields:
        author_tsim: Creator
```

The context of where the fields are being rendered in the application will be passed though as part of the i18n key as well, so you can have different labels under different view contexts (e.g. `index` vs `show`) or have a default that is overridden for a particular view context.

```yaml
en:
  blacklight:
    search:
      fields:
        title_tsim: Title
        index:
          author_tsim: Author
        show:
          author_tsim: Creator
          title_tsim: Work
```

## Values

The field values can also be mapped using field configuration. There are two easy ways to transform values from the solr document into values to display to the end user; you can use a model method/accessor for data-based transformations, or a controller-level method for other transformations.

### Accessors

Accessors are handy when the transformation you want to make is based entirely on the data from the field (or document). One thing you might do is map names given as "last name, first name" into "first name last name" [^1]. The most basic accessor configuration is using the explicit accessor configuration:

```ruby
configure_blacklight do |config|
  # Index / Search Results
  config.add_index_field 'author_tsim', label: 'Author', accessor: :author_name
end
```

This will call the accessor method (`author_name`) on the `SolrDocument` instance for each result. In `app/models/solr_document.rb`, we need to implement that method:

```
class SolrDocument
  def author_name
    return unless has? 'author_tsim'

    # Don't really do this; find a way to do this wherever you have enough information
    # to do it without as many errors.
    Array(fetch('author_tsim')).map do |value|
      a, b, c = value.split(', ', 3)

      if a && b && c
        # the value had all 3 parts
        "#{b} #{a}, #{c}"
      elsif a && !b&.match?(/^\d/)
        # the value has just two parts, and the second part was probably not a year
        "#{b} #{a}"
      else
        value
      end
    end
  end
end
```

Alternatively, you can use the `values` parameter to provide document values [^2]. This is a good choice if the value varies based on the request (e.g. the values are internationalized using the user's profile, or an administrator sees additional data, etc):

```ruby
configure_blacklight do |config|
  # Index / Search Results
  config.add_index_field 'JSON', label: 'Solr Document', values: ->(field_config, document) {
    "<pre>#{document.as_json}</pre>".html_safe
  }
end
```

[^1]: This is a somewhat contrived example; you'd almost certainly be better off mapping the data much earlier.
[^2]: We're using an inline lambda here for expediency; you could (or even should) structure your code for maintainability using any number of approaches (including extracting it as a constant; extracting a method and using the `method` method to lambda-fy it; extracting a service class that responds to `#call`; etc)
