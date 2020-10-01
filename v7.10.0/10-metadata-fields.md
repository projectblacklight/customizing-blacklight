---
layout: page
title: Configuring Metadata Fields
permalink: /v7.10.0/config_patterns/
blacklight_version: v7.10.0
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

The configuration patterns outlined on [General Configuration Patterns](/v7.10.0/config_patterns/) apply to these fields so you can use any of those to customize the display of these fields.

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
