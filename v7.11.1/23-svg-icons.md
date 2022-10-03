---
layout: page
title: SVG Icons in Blacklight
permalink: /v7.11.1/svg-icons/
blacklight_version: v7.11.1
---

Internally Blacklight uses a helper method, `blacklight_icon`, to render icons throughout the application. This approach relies on an icon being available as an asset in an SVG file. The files are located in Blacklight at [`app/assets/images/blacklight`](https://github.com/projectblacklight/blacklight/tree/v7.11.1/app/assets/images/blacklight).

You can use this functionality in your  Blacklight application in several ways.

### Changing the icon used

Let's say you want to change the search icon used away from a magnifying glass. You can do this by adding the SVG file of the icon you would like to use in your local application at `app/assets/images/blacklight/search.svg`.

### Adding new icons

New icons can be added in the same way. For instance if we want to add a "soccer" icon. We could use this one: https://fonts.gstatic.com/s/i/materialiconssharp/sports_soccer/v5/24px.svg?download=true. We then add that to our project at `app/assets/images/blacklight/sports_soccer-24px.svg`.

Next we will be able to use the icon in a partial or helper by calling `blacklight_icon('sports_soccer-24px.svg')`.

### A note on accessibility

We have taken great care to provide the best possible accessibility experience for icons in Blacklight. You can update the title and aria-label attributes used by updating these in the locale i18n files under the `blacklight.icon` key. You can see [an example of this in GeoBlacklight](https://github.com/geoblacklight/geoblacklight/blob/v3.1.0/config/locales/geoblacklight.en.yml#L110-L148).
