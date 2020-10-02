---
layout: page
title: General Configuration Patterns
permalink: /v7.11.1/config_patterns/
blacklight_version: v7.11.1
---

There are some configuration patterns that exist in multiple places around the Blacklight UI. You'll notice similar configuration APIs for various things link different kinds of metadata fields as well as other UI elements around the App.  You'll often see configuration keys like `label`, `partial`, `if`/`unless`, etc. being used for configuring different things (but they all are intended to work the same).

## Keys

The keys provided to many fields are intended to point either to a field in solr, to uniquely identify a particular element, and/or to provide a URL parameter to be used when constructing links, etc.

The following example will configure a facet with the field name `language_ssim` based on the key that was passed in.

```ruby
configure_blacklight do |config|
  config.add_facet_field 'language_ssim'
end
```

## Labels

There are a few ways to add labels to fields (as you'll notice if you're following along the example above got a default label), but one of the ways is to add a label key direction. Many of the configurations Blacklight provides take a label option.

```ruby
configure_blacklight do |config|
  config.add_facet_field 'language_ssim', label: 'Language'
end
```

## Controlling Display

You can control the display of elements by passing values to `if` and `unless`.  The most basic example is to pass a boolean representing if the field should display or not.


```ruby
configure_blacklight do |config|
  config.add_facet_field 'language_ssim', label: 'Language', if: false
end
```

This will cause this facet field to not display in the normal facet sidebar area, however the label would still be configured if you were to pass this value in the facet url parameters (for display in constraints, etc.)

You can also control display using the `if`/`unless` configurations by passing a proc/labmda.  The arguments sent to the lambda may depend on context, but in this case (and many others) we get the current controller context, the field configuration object, and the object representing the field data. Return a boolean value from the proc/lambda that represents if the facet/field should display.

In the example below, the language facet will only be displayed when a format of "Book" has been selected in the facets.

```ruby
configure_blacklight do |config|
  config.add_facet_field 'language_ssim', label: 'Language', if: -> (controller, _config, _field) do
    selected_formats = controller.params.dig('f', 'format') || []

    selected_formats.include?('Book')
  end
end
```
