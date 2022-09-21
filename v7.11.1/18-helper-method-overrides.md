---
layout: page
title: Helper method overrides
permalink: /v7.11.1/helper-method-overrides/
redirect_from: /overrides/
blacklight_version: v7.11.1
---

Much of Blacklight is written in a way that is overridable, helper methods are no different.

Let's take a look at the [module that is used to help with some of the layout for Blacklight](https://github.com/projectblacklight/blacklight/blob/v7.11.1/app/helpers/blacklight/layout_helper_behavior.rb). This  module is mixed into the `Blacklight::BlacklightHelperBehavior` which allows us to override methods mixed in.

For example, the `html_tag_attributes` method is used to inject HTML tag attributes into the main Blacklight layout.

{% highlight ruby %}
  ##
  # Attributes to add to the <html> tag (e.g. lang and dir)
  # @return [Hash]
  def html_tag_attributes
    { lang: I18n.locale }
  end
{% endhighlight %}

<div class='image-well'>
  <img src='/public/images/blacklight-7-html-attr-default.png' alt='Default HTML attributes' />
  <div class='caption'>Default HTML attributes</div>
</div>

If we want to modify this method to return something different, we first need to create the `CustomLayoutHelper` module in our local application.

{% highlight bash %}
$ touch app/helpers/custom_layout_helper.rb
{% endhighlight %}

And then within `custom_layout_helper.rb` we need to include the `Blacklight::LayoutHelperBehavior` mixin.

{% highlight ruby %}
# app/helpers/custom_layout_helper.rb
module CustomLayoutHelper
  include Blacklight::LayoutHelperBehavior
end
{% endhighlight %}

Now we are free to override methods to meet our custom application needs. For example, lets override the `html_tag_attributes` method.

{% highlight ruby %}
# app/helpers/custom_layout_helper.rb
module CustomLayoutHelper
  include Blacklight::LayoutHelperBehavior
  
  ##
  # Overriden to include dir
  def html_tag_attributes
    super.merge(dir: 'rtl')
  end
end
{% endhighlight %}

This overridden method adds an additional attribute `dir="rtl"` to display our page right-to-left. While this is not a fully sufficient solution to implementing right-to-left behavior in your site, it does demonstrate how this helper can be extended.

<div class='image-well'>
  <img src='/public/images/blacklight-7-html-attr-custom.png' alt='Custom HTML attributes' />
  <div class='caption'>Custom HTML attributes</div>
</div>

This is just one way that the Blacklight code can be overridden and customized. Similar patterns can be used to customize controllers and search behavior.

<div class="alert alert-primary">
  For more information about overriding helpers, <a href="https://github.com/projectblacklight/blacklight/wiki/Configuring-and-Customizing-Blacklight#custom-view-helpers">checkout the Blacklight Wiki</a>.
</div>
