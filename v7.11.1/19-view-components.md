---
layout: page
title: View Components
permalink: /v7.11.1/view-components/
blacklight_version: v7.11.1
---

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
