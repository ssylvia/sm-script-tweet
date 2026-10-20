# sm-script-tweet

Static ArcGIS StoryMaps iframe-embed demo with crawler-readable Open Graph and
X large-image link previews. The interactive experience opens on this site,
not inside a social feed.

Demo URL after deployment: https://ssylvia.github.io/sm-script-tweet/

## Setup and deployment

1. Ensure the StoryMaps item permits iframe embedding.
   The item and its dependent content must be publicly accessible.
   This repository cannot change those settings.
2. In this repository's **Settings → Pages → Build and deployment**, select
   **GitHub Actions** as the source.
3. Push to `main` (or run **Deploy demo to GitHub Pages** manually).
   The workflow publishes only `site/`, with no build or package installation.
   Allow the deployment branch in the `github-pages` environment if protection
   rules are configured.

The embed uses an iframe with `autoplay=true` in the story URL and autoplay
permission enabled. Browser autoplay policies still apply.
The iframe is centered in a tall mobile frame with a **9:16 width-to-height**
aspect ratio (16 units tall by 9 units wide). It scales to fit the viewport,
without depending on a social platform's player dimensions.
The default story is https://storymaps.arcgis.com/stories/fffa28a9ea7447839ee9debfd9693bb8.

To load another public StoryMaps item, add `?itemId=<32-character-item-id>` to the
demo URL. For example:
https://ssylvia.github.io/sm-script-tweet/?itemId=fffa28a9ea7447839ee9debfd9693bb8.
The existing `?storyId=` parameter also accepts any supported StoryMaps item ID;
`itemId` takes precedence when both parameters are present.
Only 32-character hexadecimal IDs are accepted; missing, empty, or invalid values
select the default story.

Before loading the iframe, the browser anonymously fetches item details from
`https://www.arcgis.com/sharing/rest/content/items/<item-id>?f=json` and checks
that the item is public. Its `typeKeywords` determine the iframe route:

| Type keyword | Route |
| --- | --- |
| `storymapcollection` | `/collections/<item-id>` |
| `storymapbriefing` | `/briefings/<item-id>` |
| `storymapframe` | `/frames/<item-id>` |
| None of the above | `/stories/<item-id>` |

If lookup fails, times out after 10 seconds, or returns a non-public item, the
default story is displayed with an explanatory message. Autoplay remains enabled.
Selecting an item requires JavaScript and does not change the hardcoded card
metadata or preview image.

### Dynamic metadata and Next.js

[GitHub Pages is static hosting](https://docs.github.com/en/pages/getting-started-with-github-pages/what-is-github-pages)
and cannot run Next.js server-side rendering (SSR). A Next.js static export would
not provide request-time metadata for arbitrary item IDs, so this demo remains a
basic static site. Dynamic social metadata would require SSR-capable hosting or
separate HTML pages generated at build time for known items; client-side updates
are not a reliable substitute for social crawlers.

To preview locally, run:

```sh
python3 -m http.server 8000 --directory /home/runner/work/sm-script-tweet/sm-script-tweet/site
```

Open http://localhost:8000/. There is no existing test/build/lint infrastructure.

## Implemented preview metadata

The initial HTML contains the title, description, canonical URL, Open Graph
website metadata, site name, locale, and an absolute HTTPS PNG URL. Structured
image metadata declares its actual MIME type and dimensions: `image/png`,
1200 × 630 (about 315 KiB). X receives explicit title, description, image, image
alt text, and `twitter:card=summary_large_image`.

These are descriptions for a crawler, not instructions that force a particular
feed layout. Platforms may omit the description, crop the image, suppress the
preview, or retain cached metadata. The shared page URL leads back to the
interactive app; share the page, not `card.png` or a future raw MP4.

The previous `twitter:card=player` declaration pointed at an arbitrary StoryMaps
iframe rather than an audio/video player. It has been replaced with the supported
image-card fallback; neither `twitter:player*` nor `og:video*` advertises media
that this repository does not supply. The existing artwork still labels the
original Player Card experiment; that label is not a claim of X player support.
The hardcoded `twitter:site` attribution is `@ssylvia`; confirm this is the
intended X account before sharing.

### Deployment and verification

- Deploy `site/` and check that the page, `card.png`, and story load anonymously
  over HTTPS with successful responses and correct HTML/PNG content types.
  Do not put bot challenges, cookies, authentication, or crawler exclusions in
  front of the page or its assets.
- Inspect **View Source**, not just the browser's live DOM: the metadata must
  exist before JavaScript executes. If hosting under another account,
  repository, or custom domain, update every absolute page/image URL in
  `site/index.html`.
- Use the [Facebook Sharing Debugger](https://developers.facebook.com/tools/debug/)
  and [LinkedIn Post Inspector](https://www.linkedin.com/post-inspector/), then
  paste the deployed URL into the actual target composer/chat. Test X web/mobile
  too; the [legacy Card Validator](https://cards-dev.twitter.com/validator), if
  available, is not proof of feed rendering.
- Previews depend on client, surface, privacy settings, and cache. Re-scrape
  where supported; a new versioned image URL can help when artwork changes.
  A query parameter alone does not guarantee cache invalidation.
- This repository has no build, lint, or test suite. Local source/asset checks
  do not demonstrate that any social platform has crawled or approved the URL;
  real-account posting and playback have not been verified here.

With JavaScript disabled, the iframe loads the default story and a direct link
to that story is displayed.
