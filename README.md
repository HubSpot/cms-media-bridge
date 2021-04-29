# cms-media-bridge

Reference for Media Bridge custom modules

## Live Examples (WIP)
- Visit https://www.mediabridge.io/examples/video and consent to cookies in the top banner. This should ensure a `?hs_utk` query param is passed to embed iframes. There should be detailed logs prefixed with `[media-bridge-embed]` from the [`media-bridge-embed-js`](./docs/media-bridge-embed-js.md) helper script which handles this.
- Play a video 
- Fill out the form in the lower right (please ensure adblockers are disabled).
- _Coming soon:_ This should create a play event on their contact record

## Installation
0. Make sure you're set up for [local development](https://designers.hubspot.com/tutorials/getting-started) with the [HubSpot CMS CLI](https://designers.hubspot.com/docs/developer-reference/local-development-cms-cli).
1. Clone this repo to your machine
2. Install dependencies by running `yarn install`
3. To upload the modules to your HubSpot account, run `yarn upload-macros && yarn upload`. The assets will be created inside a `media-bridge/` folder in your Design Manager.

## Modules

### MB Embed (mb-embed.module)
Renders a oEmbed player, designed for iframed-based players in Pages and Blog Posts.
The oEmbed html is not eagerly rendered by default, instead renders a placeholder image which [`media-bridge-embed-js`](./docs/media-bridge-embed-js.md) will transform into an iframe. 
The embed player can be revealed on load, hover, or click, and the placeholder "poster image" shown before real can be customized.

### MB Eager embed - `mb-eager-embed.module`
Simply outputs the oEmbed response `html` in a wrapper div, with no additional JS or styles. This may be suitable when a third party script is managing rendering and handling MB events instead.

### MB Email Embed (Experimental) - `mb-email-embed.module`
Renders a linked poster image with a play button, suitable for emails.

## HubL macro helpers
The modules rely on `mb-hubl-macros.html` which currently has to be imported, but an official HubL tag based on this will likely be published soon.
While not required, the macros generate markup around embeds which the helper script relies on to work.

### Sizing options

Media Bridge integrates with the Embed field, and aims supports its 3 responsive sizing options via macro. These are placed on the wrapper div in a `data-size-type` attribute.
These rely on the bit of CSS in the page modules.

![Embed field sizing options](./docs/embed-field-sizing-options.png)

- Original size (`size_type: auto`) - Based on the width and height dimensions in the oEmbed response, which will be placed as inline `width/height` styles on the wrapper, but aims to be responsive when needed.
- Full width (`size_type: auto_full_width`) - Attempts to fill the container responsively. 
- Custom - (`size_type: auto_custom_max`) - Allows the user to specify a max width and max height, while preserving the aspect ratio. The macro will place inline styles on the wrapper tag.

### Macro reference

#### `mb_embed`
Renders a wrapper for a player based on embed field oEmbed value, which will include the `media-bridge-embed-js` helper script.

```
mb_embed(embedField, options) %}

Options:
- eager - render oembed html directly if true, defaults to false. recommended only if not using simple iframe players
- reveal_on - when to transform the placeholer into a full player, only applies when `eager` is not true. options: load (default), hover, click
- poster_url - customize placeholder image (defaults to oembed thumbnail)
- play_button_color - shows SVG play button overlay of specific color (experimental)
```

#### `mb_email_embed` (experimental)
Renders an email appropriate player based on the oembed thumbnail, essentially a linked image without JS/CSS dependencies.
It will show a poster image based on the oembed thumbnail, linked to the oembed_url by default but possible to override.

```
mb_email_embed(embedField, options) %}

Options:
- link_url - customize where placeholder links (defaults to oembed url)
- poster_url - customize placeholder image (defaults to oembed `thumbnail_url`)
- play_button_color - shows SVG play button overlay of specific color (experimental)
```
