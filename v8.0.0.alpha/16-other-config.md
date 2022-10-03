---
layout: page
title: Other configuration
permalink: /v8.0.0.alpha/other-config/
blacklight_version: v8.0.0.alpha
---

## Views

Blacklight has the concept of a "view", which is a set of configuration options that are used to render a search results page. We call default view `list`. In this exercise, we'll go through steps to add a "gallery" view that highlights the thumbnails.

```ruby
# app/controllers/catalog_controller.rb
configure_blacklight do |config|
  config.view.gallery(default_thumbnail: :random_thumbnail)
end
```

```ruby
# app/helpers/application_helper.rb
def random_thumbnail(...)
  image_tag "https://loremflickr.com/320/180?random=#{Random::DEFAULT.rand(100)}"
end
```

```css
/* app/assets/stylesheets/blacklight.scss */

.documents-gallery {
  display: grid;
  gap: 1rem;
  grid-template-columns: repeat(auto-fill, minmax(200px, 1fr));

  article {
    @extend .card;
    display: flex;
    flex-direction: column-reverse;
    justify-content: start;
    overflow: hidden;

    .document-thumbnail {
      @extend .card-img-top;
    }

    .document-main-section {
      @extend .card-body;
    }

    dt {
      text-align: left;
      width: 100%;
    }
  }
}
```

As with fields, sometimes you want even more control over how Blacklight renders the documents. You can do this by creating a custom view component. In this example, we'll create a custom view component that renders the gallery view with the Boostrap card markup we want.


```ruby
# app/components/gallery_document_component.rb
class GalleryDocumentComponent < Blacklight::DocumentComponent
end
```

```ruby
# app/components/gallery_document_component.html.erb
<div class="card">
  <%= @presenter.thumbnail.render classes: 'card-img-top' %>
  <div class="card-body">
    <h3 class="h5 card-title"><%= link_to @presenter.heading, @document %></h3>

    <p class="card-text"><%= @document[:author_tsim]&.to_sentence %></p>
  </div>
</div>
```

```ruby
class Blacklight::Icons::GalleryComponent < ViewComponent::Base
  def call
    svg&.html_safe # rubocop:disable Rails/OutputSafety
  end

  class_attribute :svg, default: <<~SVG
  <svg xmlns="http://www.w3.org/2000/svg" width="16" height="16" fill="currentColor" class="bi bi-grid-3x3-gap-fill" viewBox="0 0 16 16">
    <path d="M1 2a1 1 0 0 1 1-1h2a1 1 0 0 1 1 1v2a1 1 0 0 1-1 1H2a1 1 0 0 1-1-1V2zm5 0a1 1 0 0 1 1-1h2a1 1 0 0 1 1 1v2a1 1 0 0 1-1 1H7a1 1 0 0 1-1-1V2zm5 0a1 1 0 0 1 1-1h2a1 1 0 0 1 1 1v2a1 1 0 0 1-1 1h-2a1 1 0 0 1-1-1V2zM1 7a1 1 0 0 1 1-1h2a1 1 0 0 1 1 1v2a1 1 0 0 1-1 1H2a1 1 0 0 1-1-1V7zm5 0a1 1 0 0 1 1-1h2a1 1 0 0 1 1 1v2a1 1 0 0 1-1 1H7a1 1 0 0 1-1-1V7zm5 0a1 1 0 0 1 1-1h2a1 1 0 0 1 1 1v2a1 1 0 0 1-1 1h-2a1 1 0 0 1-1-1V7zM1 12a1 1 0 0 1 1-1h2a1 1 0 0 1 1 1v2a1 1 0 0 1-1 1H2a1 1 0 0 1-1-1v-2zm5 0a1 1 0 0 1 1-1h2a1 1 0 0 1 1 1v2a1 1 0 0 1-1 1H7a1 1 0 0 1-1-1v-2zm5 0a1 1 0 0 1 1-1h2a1 1 0 0 1 1 1v2a1 1 0 0 1-1 1h-2a1 1 0 0 1-1-1v-2z"/>
  </svg>
  SVG
end
```

```ruby
# app/controllers/catalog_controller.rb
  config.view.gallery(default_thumbnail: :random_thumbnail, document_component: GalleryDocumentComponent)
```

### Document actions

Document actions are links that appear in the document show view. They are configured in the `document_actions` configuration on the view. Let's create a new action for showing the Solr document.

```ruby
# app/controllers/catalog_controller.rb
config.add_show_tools_partial(:download)
```

```ruby
# config/routes.rb
resources :solr_documents, only: [:show], path: '/catalog', controller: 'catalog' do
  concerns [:exportable, :marc_viewable]
  get 'download', on: :member
end
```

```html
<%= render Blacklight::System::ModalComponent.new do |component| %>
  <% component.title { 'JSON' } %>

  <pre><%= JSON.pretty_generate(@documents.map(&:to_h)) %></pre>
<% end %>
```

But what if we want to download it instead of displaying it in the browser?


```ruby
# app/controllers/catalog_controller.rb
  config.add_show_tools_partial(:download, component: BlahComponent)
  ...
  def download
    render json: action_documents.first.as_json
  end
```

```
class BlahComponent < Blacklight::Document::ActionComponent
  def call
    link_to 'Download', url, class: 'btn btn-primary', download: 'document.json'
  end
end
```


## Other configuration
