---
layout: page
title: Thumbnails
permalink: /v8.0.0.alpha/thumbnails/
redirect_from: /thumbnails/
blacklight_version: v8.0.0.alpha
---

Blacklight provides the ability to easily configure thumbnails to show up in your search results out-of-the-box. There are two main ways of customizing your search results with thumbnails using Blacklight's provided mechanism.

## Thumbnail Field

If you have a solr field that contains a url to a thumbnail image you can easily add that field as the `thumbnail_field` configuration option.

{% highlight ruby %}
# app/controllers/catalog_controller.rb
config.index.thumbnail_field = :thumbnail_url_field
{% endhighlight%}

This will construct an image tag with the first url in the configured field and place it in the correct place in the UI.

## Thumbnail Method

If you don't have a field in the index that has the full url to the thumbnail image you can construct the image markup by configuring a method as the `thumbnail_method`. This method needs to be available to your views/helpers, for the purpose of this tutorial we'll put it in the `ApplicationHelper`. A common example of this is where the document identifier needs to be combined with a file identifier to build the url to the thumbnail. This also allows you to configure environment specific image servers.

{% highlight ruby %}
# app/controllers/catalog_controller.rb
config.index.thumbnail_method = :render_thumbnail
{% endhighlight%}

{% highlight ruby %}
# app/helpers/application_helper.rb
def render_thumbnail(document, options)
  return unless document[:file_id].present?
  image_tag(
    "#{image_server}/#{document.id}/#{document.first(:file_id)}.png",
    options.merge(alt: presenter(document).document_heading)
  )
end
{% endhighlight%}

<div class='image-well'>
  <img src='/public/images/search-result-with-thumbnail.png' alt='Blacklight search result' />
  <div class='caption'>Updated search result with thumbnail</div>
</div>

## Default thumbnail
TODO
## Thumbnail presenter

TODO

You are now able to customize Blacklight to include thumbnails by configuring fields that contain urls to thumbnail images as well as by providing custom thumbnail rendering methods.
