---
layout: page
title: Overrides
permalink: /v5.14.0/overrides/
redirect_from: /overrides/
blacklight_version: v5.14.0
---

Much of Blacklight is written in a way that is overridable, helper methods are no different.

Let's take a look at the [module that renders search constraints on results pages](https://github.com/projectblacklight/blacklight/blob/master/app/helpers/blacklight/render_constraints_helper_behavior.rb). This module is mixed into the `Blacklight::RenderConstraintsHelper` which allows us to override methods mixed in.

For example, the `render_constraints` method renders the constraints area in the results page.

{% highlight ruby %}
##
# Render the actual constraints, not including header or footer
# info.
#
# @param [Hash] query parameters
# @return [String]
def render_constraints(localized_params = params)
  render_constraints_query(localized_params) + render_constraints_filters(localized_params)
end
{% endhighlight %}

<div class='image-well'>
  <img src='/public/images/default-constraints.jpg' alt='Default constraints' />
  <div class='caption'>Default constraints</div>
</div>

If we want to modify this method to return something different, we first need to create the `RenderConstraintsHelper` module in our local application.

{% highlight bash %}
$ touch app/helpers/render_constraints_helper.rb
{% endhighlight %}

And then within `render_constraints_helper.rb` we need to include the `Blacklight::RenderConstraintsHelperBehavior` mixin.

{% highlight ruby %}
# app/helpers/render_constraints_helper.rb
module RenderConstraintsHelper
  include Blacklight::RenderConstraintsHelperBehavior
end
{% endhighlight %}

Now we are free to override methods to meet our custom application needs. For example, lets override the `render_constraints` method.

{% highlight ruby %}
# app/helpers/render_constraints_helper.rb
module RenderConstraintsHelper
  include Blacklight::RenderConstraintsHelperBehavior
  ##
  # Overridden to include an extra message
  #
  # @param [Hash] query parameters
  # @return [String]
  def render_constraints(localized_params = params)
   (super + "<--- Remove limits by clicking the x").html_safe
  end
end
{% endhighlight %}

This overridden method adds a string to the `super` call. `super` here calls the `render_constraints` method defined in `Blacklight::RenderConstraintsHelperBehavior` and our concatenated string is returned and displayed in the interface.

<div class='image-well'>
  <img src='/public/images/custom-constraints.jpg' alt='Custom constraints' />
  <div class='caption'>Custom constraints</div>
</div>

This is just one way that the Blacklight code can be overridden and customized. Similar patterns can be used to customize controllers and search behavior.
