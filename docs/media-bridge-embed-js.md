# media-bridge-embed-js

Helper JS for rendering Media Bridge embeds.
[The `hubspotutk` cookie](https://developers.hubspot.com/docs/api/events/tracking-code) will be added to the iframe URL as the `?hs_utk` query param if available.
The `?hs_portal_id` query param will also be included to allow tracking a play event.

## Alternatives
For a Media Bridge integration which is not based on `iframe` players, we recommend including a third party script to utilize the `hubspotutk` cookie for tracking play events.

## Installation
Add this to a custom module. This is automatic if using [the `media_bridge_embed` HubL tag](../README.md#media_bridge_embed-hubl-tag) (recommended).
```
{{ require_js('https://static.hsappstatic.net/media-bridge-embed-js/ex/v1.js' }}
```

## Usage
The script expects embeds to be declared as `div`s with the `hs-mb-embed-wrapper` className and a `data-oembed-url` attribute. 
```html
<div class="hs-mb-embed-wrapper" data-oembed-url="https://vimeo.com/33099121"></div>
```

The style of markup used in the Embed field's JS snippet and default Video module is also support, if `data-source-type="media_bridge"`. This includes its sizing options
```html
<div class="oembed_container">
  <div class="iframe_wrapper" data-embed-url="https://vimeo.com/33099121" data-source-type="media_bridge"></div>
</div>
```

### oEmbed fetching

This oEmbed URL will be fetched via XHR unless we can find the oembed response data in JS globals on the page.
To prevent having to fetch the oembed URL, place it as JSON in the `_hsMediaBridge.embedsByUrl` object (which the `mb_embed` macro handles automatically)
```html
<script>
window._hsMediaBridge = window._hsMediaBridge || { embedsByUrl: {} };
window._hsMediaBridge.embedsByUrl["https://www.youtube.com/watch?v=s6ITvy4qfw0"] = { html: '<iframe src=..." };
</script>
```

### Dynamic iframe injection

If an iframe does not exist inside the wrapper element on pageload, the oembed response html will be injected.
If an iframe already exists in the wrapper, its url will be updated to append `?hs_utk` when the `hubspotutk` cookie becomes available.
This will cause an existing iframe to reload, so we prefer not to inject the markup instead of eagerly rendering them in HubL.

We advise using a "poster" image of the desired iframe dimensions within the wrapper, possibly with other affordances like a play button.
These should use the `hs-mb-embed-placeholder` className, and will be hidden as the iframe is injected.
In the future, we plan enable the injection and reveal of the iframe to be trigger on hover/click, or potentially conversion events like submitting a form.

```html
<div class="hs-mb-embed-wrapper" data-oembed-url="https://www.youtube.com/watch?v=s6ITvy4qfw0">
  <img class="hs-mb-embed-placeholder" src="https://i.ytimg.com/vi/s6ITvy4qfw0/hqdefault.jpg" />
  <div class="hs-mb-embed-placeholder play-button" style="background: url(https://f.hubspotusercontentqa10.net/hubfs/861453961/media-bridge/video-play-trans.png) center no-repeat; background-size: 120px;"></div>
</div>
```

We encourage considering the case where JavaScript is disabled, or the viewer experiencing an error.
A poster image is preferable to nothing as a fallback, or until it can be enhanced into an iframe player.

### Player reveal options
By default placeholders are replaced with players on load (or as soon as the UTK can be obtained). The `data-reveal-on` attribute can customize this

`data-reveal-on`
- `click` will require a click before injecting the iframe. `autoplay=1` will be appended to the iframe URL in hopes the initial click will start the video
- `defer` will wait for `hsMediaBridgeApi.revealDeferredPlayer(oembedUrl)` to be called to reveal player iframe
