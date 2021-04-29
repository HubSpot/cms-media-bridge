# media-bridge-embed-js

Helper JS for rendering Media Bridge embed `iframe`s.
[The `hubspotutk` cookie](https://developers.hubspot.com/docs/api/events/tracking-code) will be added as the `?hs_utk` query param if available.

## Live examples
View at https://app.hubspot.com/media-bridge-embed-js/example

## Alternatives
For a Media Bridge integration which is not based on `iframe`d players, we recommend including a third party script to make sure the UTK cookie is utilized to record play events.

## Installation
For now we recommend adding the script via Website Settings > Pages > Site footer html.  We are working to make its inclusion automatic for sites using Media bridge soon to prevent this step.
```
<script async type="text/javascript" src="https://static.hsappstatic.net/media-bridge-embed-js/ex/v1.js"></script>
```

## Usage
The script requires embeds to be declared as `div`s with the `hs-mb-embed-wrapper` className and a `data-oembed-url` attribute. 
```html
<div class="hs-mb-embed-wrapper" data-oembed-url="https://vimeo.com/33099121"></div>
```

This oembed URL will be fetched via XHR unless we can find the oembed response data in JS globals on the page.
To prevent having to fetch the oembed URL, place it as JSON in the `_hsMediaBridge.embedsByUrl` object. We are working on HubL helpers to make this easier.
```html
<script>
window._hsMediaBridge = window._hsMediaBridge || { embedsByUrl: {} };
window._hsMediaBridge.embedsByUrl["https://www.youtube.com/watch?v=s6ITvy4qfw0"] = { html: '<iframe src=..." };
</script>
```

### Dynamic iframe injection

If an iframe does not exist inside the wrapper `div` on pageload, the oembed response html will be injected. If an iframe already exists in the wrapper, we will update its url to append `?hs_utk`.
This will cause an existing iframe to reload, so we prefer not to eagerly render them. 

We advise using a "poster" image of the desired iframe dimensions within the wrapper, possibly with other affordances like a play button.
These should use the `hs-mb-embed-placeholder` className, and will be hidden as the iframe is injected.
In the future, we plan enable the injection and reveal of the iframe to be trigger on hover/click, or potentially conversion events like submitting a form.

```html
<div class="hs-mb-embed-wrapper" data-oembed-url="https://www.youtube.com/watch?v=s6ITvy4qfw0">
  <img class="hs-mb-embed-placeholder" src="https://i.ytimg.com/vi/s6ITvy4qfw0/hqdefault.jpg" />
  <div class="hs-mb-embed-placeholder play-button" style="background: url(https://f.hubspotusercontentqa10.net/hubfs/861453961/media-bridge/video-play-trans.png) center no-repeat; background-size: 120px;"></div>
</div>
```

The "progressive enhancement strategy above" makes an ideal as a fallback for email, which can link to the provider details url if desired. 
We encourage also considering the case where JavaScript is disabled or the viewer experiences an error; a poster image is generally preferable to nothing.

However if HTML injection is not desired, add the `data-inject-html="true"` attribute to the wrapper div its contents will not be modified. This makes the script of little use now but this may change as the MB feature set evolves.

### Player reveal options
By default the lazy placeholders are replaced with players on load (or as soon as the UTK can be obtained). The `data-reveal-on` attribute can customize this

`data-reveal-on`
- `hover` will require a mouseenter event on the wrapper before injecting the iframe
- `click` will require a click before injecting the iframe. `autoplay=1` will be appended to the iframe URL in hopes the initial click will start the video
