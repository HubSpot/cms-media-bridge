# cms-media-bridge

Examples and docs for integrating Media Bridge with the HubSpot CMS

⚠️ **This is a BETA release that uses some HubSpot features that are not available to all customer accounts. Please refer to the HubSpot [Developer Beta Terms](https://legal.hubspot.com/developerbetaterms)** ⚠️

## Modules

### Embed Field with MB  `embed-field-with-mb.module`

Example of minimal changes to [the recommended Embed field snippet](https://developers.hubspot.com/docs/cms/building-blocks/module-theme-fields/oembed#page-and-or-blog-modules) to support MB.
If any MB integrations are installed in the portal, the "Media integration" radio button will lead to the MB picker.
The JS portion has been tweaked to ignore when `data-source-type="media-bridge"`, which our helper script will take fetch and render instead, including the `?hs_utk` query param if available.
We recommend this approach for adding MB to the default video module, or when the existing styles and sizing options must be supported.

![Embed field sizing options](./docs/embed-field-video-selected.png)

## CRM object `crm_object.module`

Uses the `crm_object` function to query for media selected in an Embed field, which allows using default and custom properties as an alternative to the oembed response.

> :construction: **The modules below rely on experimental macros which currently have to be imported** An official HubL tag may be available instead soon.

### MB Embed - `mb-embed.module`
Renders a oEmbed player, designed for iframe-based players in Pages and Blog Posts.
The embed player can be revealed on load, hover, or click, and the placeholder "poster image" shown initially can be customized.

### MB Eager embed - `mb-eager-embed.module`
Simply outputs the oEmbed response `html` in a wrapper div. If `media-bridge-embed-js` finds an iframe already present, it will add the `?hs_utk` query param if available (however this will cause it to reload).
This may be suitable when a third party script is managing rendering and handling MB events instead.

### MB Email Embed (Experimental) - `mb-email-embed.module`
Renders a linked poster image with a play button, suitable for emails.

## HubL macro helpers
The modules rely on `mb-hubl-macros.html` which currently has to be imported, but an official HubL tag based on this will likely be published soon.
While not required, the macros generate markup around embeds which the helper script relies on to work.


### Macro reference

#### `mb_embed`
Renders a wrapper for a player based on embed field oEmbed value, which will include the `media-bridge-embed-js` helper script.

```
mb_embed(embed_field, options) %}

Options:
- eager - render oembed html directly if true, defaults to false. recommended only if not using simple iframe players
- reveal_on - when to transform the placeholer into a full player, only applies when `eager` is not true. options: load (default), hover, click
- poster_url - customize placeholder image (defaults to oembed thumbnail)
```

#### `mb_email_embed` (experimental)
Renders an email appropriate player based on the oEmbed thumbnail, essentially a linked image without JS/CSS dependencies.
It will show a poster image based on the oembed thumbnail, linked to the oembed_url by default but possible to override with an image from Files.
When a custom poster URL is selected, a play button of a specific color can be overlayed on it as well.

*Note* Currently the module must link to a CMS page where the MB media is embedded in order to track play events.

```
mb_email_embed(embed_field, options) %}

Options:
- link_url - customize where placeholder links (defaults to oembed url)
- poster_url - customize placeholder image (defaults to oembed `thumbnail_url`)
- play_button_color - shows SVG play button overlay of specific color (experimental)
```

## Sizing options

Media Bridge integrates with the Embed field, and the `embed-field-with-mb.module` should support these options in the same way as the snippet's inline JS does.
The other modules based on the macro strive to support them with just inline styles and a bit of module CSS, but may need custom attention.

![Embed field sizing options](./docs/embed-field-sizing-options.png)

These options placed on the wrapper div in a `data-size-type` attribute.
- Original size (`size_type: auto`) - Based on the dimensions in the oEmbed response, but attempts to stay responsive
- Exact size (`size_type: exact`) - Based on the exact width and height the user providers, saved on the field as `width/height` 
- Full width (`size_type: auto_full_width`) - Fill the container responsively
- Custom - (`size_type: auto_custom_max`) - Allows the user to specify a max size while preserving the aspect ratio. Saved on the field as `max_width/max_height`

## Installation
- Make sure you're set up for [local development](https://designers.hubspot.com/tutorials/getting-started) with the [HubSpot CMS CLI](https://designers.hubspot.com/docs/developer-reference/local-development-cms-cli).
- Clone this repo and install dependencies by running `yarn install`
- To upload the modules to your HubSpot account, run `yarn upload-macros && yarn upload`. The assets will be placed inside a `media-bridge/` folder in the Design Manager.
