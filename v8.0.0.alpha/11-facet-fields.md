---
layout: page
title: Configuring Facet Fields
permalink: /v8.0.0.alpha/facet_fields/
blacklight_version: v8.0.0.alpha
---

Blacklight provides facets to help a user filter search results.

Facet fields can be configured using the `add_facet_field` configuration method:

```ruby
configure_blacklight do |config|
  config.add_facet_field 'format', label: 'Format'
end
```

Solr calculates the number of hits for each format value. Blacklight displays the facet data with links so users can filter the results to just those with a particular value. Additional options control different types of interactions.

If there are many facet values, you can use `limit` to control how many appear on the search results page.

```diff
- config.add_facet_field 'subject_ssim', label: 'Topic'
+ config.add_facet_field 'subject_ssim', label: 'Topic', limit: 5
```

Additional facet values are available in a modal popup.

You can control whether the facet is displayed expanded by default:
```diff
- config.add_facet_field 'format', label: 'Format'
+ config.add_facet_field 'format', label: 'Format', collapse: false
```

You can also sort the values alphabetically [^1]:
```diff
- config.add_facet_field 'format', label: 'Format', collapse: false
+ config.add_facet_field 'format', label: 'Format', collapse: false, sort: 'alpha', limit: -1
```

If the facet data is mutually exclusive, you might consider using `single` to allow the user to toggle between values easily:

```diff
- config.add_facet_field 'pub_date_ssim', label: 'Publication Year'
+ config.add_facet_field 'pub_date_ssim', label: 'Publication Year', single: true
```


## Query facets

Blacklight also has built-in support for other types of Solr faceting. Query facets are good for providing facets based on dynamic data not directly present in the index. You can specify the "values" of a query facet and the applicable Solr query using the `query` parameter:

```ruby
config.add_facet_field 'example_query_facet_field', label: 'Publish Date', query: {
   years_5: { label: 'within 5 Years', fq: "pub_date_ssim:[#{Time.zone.now.year - 5 } TO *]" },
   years_10: { label: 'within 10 Years', fq: "pub_date_ssim:[#{Time.zone.now.year - 10 } TO *]" },
   years_25: { label: 'within 25 Years', fq: "pub_date_ssim:[#{Time.zone.now.year - 25 } TO *]" }
}
```

## Pivot facets

There is built in support for basic pivot facets in Blacklight.  This is a "hierarchical" facet that will nest the values of the facet fields that you provide.

```ruby
config.add_facet_field 'topic_language', label: 'Topic by language', pivot: [:language_ssim, :subject_ssim]
```

<div class="image-well">
  <img src="/public/images/blacklight-7-pivot-facet.png" alt="Example pivot facet" />
  <div class="caption">Example pivot facet</div>
</div>

You can also add configure this facet to be dynamically collapsible by setting the `collapsing` configuration option to `true`.

```diff
- config.add_facet_field 'topic_language', label: 'Topic by language', pivot: [:language_ssim, :subject_ssim]
+ config.add_facet_field 'topic_language', label: 'Topic by language', pivot: [:language_ssim, :subject_ssim], collapsing: true
```

<div class="image-well">
  <img src="/public/images/blacklight-7-collapsing-pivot-facet.png" alt="Collapsing pivot facet" />
  <div class="caption">Collapsing pivot facet</div>
</div>

## View components

Blacklight has added support for [view components](https://github.com/github/view_component). While there are several benefits to using this abstraction, the major benefit for Blacklight (as a Rails engine) the ability to define an explicit interface to views to make them easier and safer to override.

## View components for overriding a facet

A good example of this new abstraction is the new configuration parameter available for facets:

```ruby
config.add_facet_field 'format', label: 'Format', component: SuperSpecialFacetComponent
```

This tells Blacklight to use your custom component for rendering the facet (similar in some ways to the `partial` configuration). This component will get handed a `Blacklight::FacetFieldPresenter` instance, and it's responsible for rendering everything about the facet control.

We can start implement this component (in `app/components/super_special_facet_component.rb`) by inheriting the existing behavior:

```ruby
class SuperSpecialFacetComponent < Blacklight::FacetFieldListComponent
end
```

For this example, we want to customize the display of the facet, so we'll need to add a corresponding `app/components/super_special_facet_component.html.erb`. We'll start from the current list view:

```erb
<%= render(@layout.new(facet_field: @facet_field)) do |component| %>
  <% component.with(:label) do %>
    <%= @facet_field.label %>
  <% end %>
  <% component.with(:body) do %>
    <ul class="facet-values list-unstyled">
      <%= render_facet_limit_list @facet_field.paginator, @facet_field.key %>
    </ul>
  <% end %>
<% end %>
```

and then make a nice visible change:

```diff
<% component.with(:label) do %>
-   <%= @facet_field.label %>
+   -> <%= @facet_field.label %> <-
<% end %>
```

Note: the `view_component` gem is aggressive about caching lookup paths, so if you're not seeing the updated label, try restarting your Rails application.

We get `@layout` from the upstream component (also separately configurable using `layout`, but defaults to `Blacklight::FacetFieldComponent`, which provides the expand/collapse behavior). We can plug any custom rendering into the different content areas provided by the layout (`label` and `body`).

## Controlling URL parameters

TODO:


<div class="alert alert-primary">
  For more information about configuring facets in Blacklight, <a href="https://github.com/projectblacklight/blacklight/wiki/Configuration---Facet-Fields">checkout the Blacklight Wiki</a>.
</div>

[^1]: This is sorted by Solr based on the indexed term; Solr offers no control over direction, lexical vs natural sort order, etc.
