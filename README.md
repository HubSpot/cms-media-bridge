# cms-media-bridge

Reference for Media Bridge custom modules

## Live Examples (WIP)
- Visit https://www.mediabridge.io/examples/video and consent to cookies in the top banner. This should ensure a `?hs_utk` query param is passed to embed iframes. There should be detailed logs prefixed with `[media-bridge-embed]` from the [`media-bridge-embed-js`](./docs/media-bridge-embed-js.md) helper script which handles this.
- Play a video 
- Fill out the form in the lower right (please ensure adblockers are disabled).
- Coming soon: This should create a play event on their contact record

## Installation
0. Make sure that you're set up for [local development](https://designers.hubspot.com/tutorials/getting-started) with the [HubSpot CMS CLI](https://designers.hubspot.com/docs/developer-reference/local-development-cms-cli).
1. Clone this repo to your machine
2. Install dependencies by running `yarn install`
3. To upload the modules to your HubSpot account, run `yarn upload-macros && yarn upload`. The assets will be created inside a `media-bridge/` folder in your Design Manager.

## Modules

### MB Embed (mb-embed.module)
Renders a oEmbed player, meant for Pages and Blog Posts.
The oEmbed html is not eagerly rendered by default, instead we show a placeholder image which [`media-bridge-embed-js`](./docs/media-bridge-embed-js.md) will transform into an iframe. 
The embed player can be revealed on load, hover, or click. The placeholder "poster image" and play button overlay color are also customizable.

### MB Email Embed (mb-email-embed.module)
Renders a poster image with a play button, suitable for emails. By default the image links to the oEmbed URL, but the link destination can be customized.

## HubL macro helpers
The modules rely on `mb-hubl-macros.html` which currently has to be imported, but may become supported in the near future.

### `mb_embed`

```
mb_embed(embedField, options) %}

Options:
- playButtonColor - overrides play button SVG overlay to specific color
- posterUrl - customize placeholder image (defaults to oembed thumbnail)
- revealOn - load (default), hover, click
- eager - render oembed html directly if true, defaults to false. recommended only if not using simple iframe players
```

### `mb_email_embed`

```
mb_email_embed(embedField, options) %}

Options:
- playButtonColor - overrides play button SVG overlay to specific color
- posterUrl - customize placeholder image (defaults to oembed thumbnail)
- linkUrl - customize where placeholder links (defaults to oembed url)
```
