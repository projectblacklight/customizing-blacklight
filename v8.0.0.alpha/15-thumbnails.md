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

Sometimes, not all documents have a thumbnail. In this case, you can configure a default thumbnail to be used for documents that don't have a thumbnail.

{% highlight ruby %}
# app/controllers/catalog_controller.rb
  config.index.default_thumbnail = 'data:image/svg+xml;base64,PHN2ZyB4bWxucz0iaHR0cDovL3d3dy53My5vcmcvMjAwMC9zdmciIHdpZHRoPSIzMDAiIGhlaWdodD0iMTUwIiB2aWV3Qm94PSIwIDAgMzAwIDE1MCI-IDxyZWN0IGZpbGw9IiNkZGQiIHdpZHRoPSIzMDAiIGhlaWdodD0iMTUwIi8-IDx0ZXh0IGZpbGw9InJnYmEoMCwwLDAsMC41KSIgZm9udC1mYW1pbHk9Ikdlb3JnaWEsIHNlcmlmIiBmb250LXNpemU9IjMwIiBkeT0iMTAuNSIgZm9udC13ZWlnaHQ9Im5vcm1hbCIgeD0iNTAlIiB5PSI1MCUiIHRleHQtYW5jaG9yPSJtaWRkbGUiPjMwMMOXMTUwPC90ZXh0PiA8L3N2Zz4='
{% endhighlight%}

You are now able to customize Blacklight to include thumbnails by configuring fields that contain urls to thumbnail images as well as by providing custom thumbnail rendering methods.
