---
layout: page
title: Metadata Fields
permalink: /v7.11.1/metadata_fields/
blacklight_version: v7.11.1
---

## TODO

* Add section about new blacklight field types
* Add section about adding arbitrary config options to field

Blacklight allows you to configure which solr fields display in search results and the record view. There are various ways to customize how these fields display and how they behave.  Blacklight will auto-generate a configuration into a controller in your application. For the purposes of this tutorial we'll assume that it is the `CatalogController`.

There is a lot more detailed information on the [Blacklight wiki](https://github.com/projectblacklight/blacklight/wiki/Configuration---Results-View) but we'll cover some of the basic customization options.

## Labels

The label configuration is pretty self explanatory. One useful strategy is to use rails' i18n functionality to return a string that you can configure in your locale files.

{% highlight ruby %}
# app/controllers/catalog_controller.rb
config.add_index_field 'custom_field', label: I18n.t('your_application.index.custom_field.label')
{% endhighlight %}


{% highlight yaml %}
# config/locales/en.yml
en:
  your_application:
    index:
      custom_field:
        label: 'Custom Data'
{% endhighlight %}

Now that you have that key in your locale file that value will be used as the label for that field when present. This happens to be configured for the `index` view (search results), but all the same configurations are available for the `show` view (full record).

## Custom Helper Method

One of the more powerful techniques for customizing metadata fields is to configure a `helper_method` for a particular field. The value of the `helper_method` configuration is a symbol which represents the name of a method available to your helpers/views. This is a common customization technique if you wanted to do something like link ISBNs to external services for lookup.

<div class='image-well'>
  <img src='/public/images/link-record.png' alt='Record view without customization' />
  <div class='caption'>Unlinked ISBN fields</div>
</div>

{% highlight ruby %}
# app/controllers/catalog_controller.rb
config.add_show_field 'isbn_t', helper_method: :link_to_external_lookup, label: 'Lookup'
{% endhighlight %}

{% highlight ruby %}
# app/helpers/application_helper.rb
def link_to_external_lookup(options={})
  url_prefix = 'http://www.amazon.com/gp/search/ref=sr_adv_b/?field-isbn='
  options[:value].map do |isbn|
    link_to("#{isbn}", "#{url_prefix}#{isbn}") << ' (Amazon)'
  end
end
{% endhighlight %}

<div class='image-well'>
  <img src='/public/images/linked-isbns.png' alt='Customized record view with ISBN links' />
  <div class='caption'>ISBN fields linked to an external search</div>
</div>

The options passed to the `link_to_external_lookup` method also include the name of the field that is being displayed as well as the entire document, so there are many possible ways to customize the display of the metadata fields with this kind of helper method.

## Accessor

Using the `helper_method` configuration above was particularly useful due to the fact that we were using rails' `link_to` helper for linking. Another approach is to configure an `accessor` which will call that method on our document model.  This is a particularly useful strategy if you're constructing data that is available at the model level and does not need access to anything in the controller/view layers.

{% highlight ruby %}
# app/controllers/catalog_controller.rb
config.add_index_field 'author_display', accessor: :primary_author_with_role, label: 'Title'
{% endhighlight %}

{% highlight ruby %}
# app/models/solr_document.rb
def primary_author_with_role
  "#{first(:primary_author_name)} (#{first(:primary_author_role)})"
end
{% endhighlight %}

You have now used several strategies for customizing metadata fields on the `index` and `show` views in Blacklight. You should now be able to link the values of particular solr fields as well as concatenate various data from around the solr document into one field.
