# sm-script-tweet

Static ArcGIS StoryMaps iframe-embed demo with crawler-readable Open Graph image
previews and an experimental X Player Card. The player requests the interactive
website behind X's play button; in-feed rendering is subject to X's support and
approval, not guaranteed by these tags. The interactive experience also opens
directly on this site.

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
The iframe fills the available viewport without enforcing an aspect ratio in CSS.
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

## URL-only sharing research

Research date: **October 20, 2026**. Scope: pasting a public page URL or opening a
share composer with URL/text parameters, without an authenticated publishing API.
The person sharing still signs in to the platform and confirms the post.
Share intents do not grant richer preview capabilities than pasting the same URL.

### Popularity and evidence boundaries

“Most popular” needs a metric. This comparison uses the **social-media category
in Cloudflare Radar's 2025 Internet-services ranking** as a bounded top-ten
selection, then adds YouTube, WhatsApp, Telegram and WeChat because video and
messaging links matter here. Cloudflare measures relative popularity using
anonymized DNS queries to its 1.1.1.1 resolver, **not monthly active users, people,
or market share**. Category placement is not a cross-category audience ranking.

The [original 2025 report](https://blog.cloudflare.com/radar-2025-year-in-review-internet-services/)
was unreachable. A [third-party captured copy](https://github.com/Cloudflare-Mining/Cloudflare-Datamining/blob/main/data/blog/radar-2025-year-in-review-internet-services.html)
was retrieved; its reported order is used below, with direct-origin verification
still outstanding. This is dated 2025 evidence, not an assertion of October 2026
rankings. [DataReportal's social-media overview](https://datareportal.com/social-media-users)
could not be retrieved either. A current comparable global MAU top ten could not
be established; counts from advertising reach, registered members, daily users,
and MAU have deliberately not been combined. Messenger and Douyin may matter in
an audience-based selection.

Official public GitHub specifications and implementations were retrieved.
Most official documentation websites failed DNS resolution in this environment,
so reference links are not proof that their current contents were verified.
No real-account posts, platform approval or live preview playback were tested.
Unverified behavior below must be checked on the target client before relying
on it. Source-code capability is not proof of a public metadata contract.

### Best option per platform

Preference numbers match the request: **1** immediate interactive iframe;
**2** video + text/link; **3** interactive iframe after play; **4** custom
thumbnail + link; **5** ordinary link preview. “Baseline” is the best defensible
target with the assets in this repository, not a guarantee of rendering.

| Platform | Baseline for this app and URL-only workflow | Beyond the baseline / important limits |
| --- | --- | --- |
| **Facebook** | **4** — OG title, description, custom image and page link. | **2** is an experiment with a real hosted MP4 and video metadata, not established for arbitrary domains. Native video upload is a separate workflow. No verified arbitrary iframe. [OG specification](https://github.com/facebook/open-graph-protocol/blob/master/content/index.markdown). |
| **Instagram** | No controllable website-preview feed post was established; copy/share the URL on a supported surface instead. | A pasted URL is not a feed image/video upload. Story link stickers, profile links and DM previews are separate surfaces; current eligibility and DM rendering were not verified. Native video + a permitted outbound link is outside meta-only sharing. [Official link-sticker announcement](https://about.fb.com/news/2021/10/expanding-sharing-links-in-stories-to-everyone/) (reference not retrieved). |
| **TikTok** | No generic external-URL-to-feed-preview mechanism was verified. | Hosting an MP4 and adding OG tags does not publish a TikTok. Native Share Kit takes photo-library media, not arbitrary HTML; profile/message links are surface-dependent. [Verified official sharing implementation](https://github.com/tiktok/tiktok-opensdk-ios/blob/main/Sources/TikTokOpenShareSDK/Public/TikTokShareRequest.swift). |
| **Snapchat** | URL sharing on supported surfaces; a controlled OG card for this app is unverified. | Do not promise a Snap, autoplaying video, or interactive iframe from pasting a URL. Native media/link attachment is different. [Creative Kit for Web](https://developers.snap.com/snap-kit/creative-kit/Tutorials/web) is an integration lead, not a verified metadata-only contract; its current requirements were inaccessible. |
| **LinkedIn** | **4** — image/title/link card. | **No automatic native PDF carousel from a pasted URL is established.** Upload a PDF/document in the composer for that format; native video also uses a separate workflow. Self-hosted MP4 metadata is not a guaranteed LinkedIn player. [Post Inspector](https://www.linkedin.com/post-inspector/) (reference not retrieved). |
| **X** | Experimental **3** requested via `twitter:card=player`, pointing to this interactive site. | Arbitrary StoryMaps HTML behind a play button is not verified as supported. Current Player Card acceptance/approval and autoplay are unverified; open the page directly if no player appears. A real-audio/video player is a separate option. [Official video-player sample](https://github.com/xdevplatform/cards-player-samples/blob/main/player/container.html). |
| **Kwai** | URL/manual sharing only; a custom website-card contract was not verified. | Do not promise external-URL-to-video publication, an OG-controlled feed preview, or arbitrary HTML playback. No primary preview contract was retrieved from [Kwai](https://www.kwai.com/); Chinese Kuaishou integrations must not be assumed to apply to international Kwai. |
| **Discord** | Target **4** — a custom-image link embed, where user/channel settings permit previews. | Rich video embeds may be possible, but arbitrary-domain MP4 recognition, exact metadata selection and interactive HTML were not verified here. A bot-created embed is different. [Auto-embed help](https://support.discord.com/hc/en-us/articles/206342858--How-do-I-disable-auto-embed) is an official lead whose current text was inaccessible. |
| **Pinterest** | Target **4** — an image-backed Pin linking to the app through the user-facing Pin creation flow. | Supply/select the actual image and destination; OG tags do not automatically create video Pins or arbitrary iframes. [Save Button](https://developers.pinterest.com/docs/add-ons/save-button/) and [Rich Pins](https://help.pinterest.com/en/business/article/rich-pins) are official leads; current parameters/eligibility were not verified. |
| **Reddit** | **4** — link post with OG thumbnail, subject to community and user settings. | Historical first-party code distinguishes recognized media providers from thumbnail-only scraping. An existing provider's player does not establish arbitrary MP4/iframe support. [Archived implementation](https://github.com/reddit-archive/reddit/blob/master/r2/r2/lib/media.py), not evidence of current production behavior. |

The rows above follow the captured report's social-category order. Four
additional large destinations, deliberately **not** assigned a social-category
rank:

| Platform | Baseline for this app and URL-only workflow | Beyond the baseline / important limits |
| --- | --- | --- |
| **YouTube** | No generic webpage-to-video/feed-card publication was verified. Share a link on an appropriate supported surface. | Uploading a real video and choosing its thumbnail is separate from OG metadata. External-link clickability varies across descriptions, Shorts, comments and Posts. [Verified official native-upload example](https://github.com/youtube/api-samples/blob/master/python/upload_video.py); [link guidance](https://support.google.com/youtube/answer/13748639?hl=en) (not retrieved). |
| **WhatsApp** | Target **4** — image/title/description link preview in a message, if generated. | Text intents do not attach video. Arbitrary MP4 inline playback and arbitrary iframe execution were not established. Current consumer preview documentation was inaccessible, so client/settings verification remains necessary. |
| **Telegram** | **4** — image/text link preview. | Conditional **2**: the iOS client can render supplied MP4/player previews. It also contains generic iframe support, but requires a server-supplied embed URL: that is not proof that arbitrary publishers can enable **1** or **3** with metadata. [Verified player selection](https://github.com/TelegramMessenger/Telegram-iOS/blob/master/submodules/TelegramUniversalVideoContent/Sources/WebEmbedPlayerNode.swift) and [embed URL requirement](https://github.com/TelegramMessenger/Telegram-iOS/blob/master/submodules/TelegramUniversalVideoContent/Sources/WebEmbedVideoContent.swift). |
| **WeChat** | **5 or plain link** conservatively; a custom pasted-link thumbnail is unverified. | Rich share-card customization uses a signed JS-SDK integration, outside strict credential-free meta tags. In-app webpage sharing is not the same as pasting a URL. Tencent's [TCMPP bridge sample](https://github.com/TencentCloud/tcmpp-demo-miniprogram/blob/main/index.js) corroborates signing/share fields, not every current WeChat permission. [Official JS-SDK reference](https://developers.weixin.qq.com/doc/offiaccount/OA_Web_Apps/JS-SDK.html) was inaccessible. |

### Sharing URLs, not publishing APIs

Percent-encode each parameter value separately. These endpoints open a composer
or destination chooser; they neither silently post nor attach the page's MP4.
Only X's current intent documentation was directly retrieved; the conventional
endpoints below are implementation patterns to verify, not new platform
guarantees.

| Platform | User-facing sharing route |
| --- | --- |
| X | `https://x.com/intent/tweet?url={URL}&text={TEXT}` — [verified official documentation](https://github.com/xdevplatform/docs/blob/main/x-for-websites/post-button/guides/web-intent.mdx). |
| Facebook | `https://www.facebook.com/sharer/sharer.php?u={URL}`. The separate official Share Dialog requires app configuration. |
| LinkedIn | `https://www.linkedin.com/sharing/share-offsite/?url={URL}` — no document upload or guaranteed prefilled commentary. |
| WhatsApp | `https://api.whatsapp.com/send?text={TEXT_AND_URL}` — text only. |
| Telegram | `https://t.me/share/url?url={URL}&text={TEXT}` — [official reference](https://core.telegram.org/widgets/share#custom-buttons) (not retrieved). |
| Reddit | `https://www.reddit.com/submit?url={URL}&title={TITLE}` — [historical first-party implementation](https://github.com/reddit-archive/reddit/blob/master/r2/r2/public/static/button/button-embed.js). |
| Pinterest | `https://www.pinterest.com/pin/create/button/?url={URL}&media={IMAGE_URL}&description={TEXT}` — user selects/confirms a Pin; not a video attachment. |
| Instagram, TikTok, YouTube, Snapchat, Kwai, Discord, WeChat | No general credential-free web composer for posting this arbitrary URL was verified. Use copy link/manual sharing or an applicable OS share sheet; native SDK integrations are separate. |

Facebook, WhatsApp, Telegram and Reddit endpoint patterns were also corroborated
in the existing [react-share implementation](https://github.com/nygardk/react-share/tree/master/src);
LinkedIn's conventional endpoint was located in
[social-share-urls](https://github.com/bradvin/social-share-urls#linkedin).
These are research references, **not dependencies added to this site**.

### Why an iframe is not a universal preview

No metadata-only mechanism was verified that makes this arbitrary StoryMaps
iframe interactive inside any of these platforms, either immediately
(preference **1**) or behind a play button (preference **3**). An in-app browser
opening the link is still navigation, not an embedded feed preview.

oEmbed is not a universal exception: a platform's embed endpoint normally lets
**your website embed its posts**, not vice versa. Consumer-supported external
media providers and native mini-app integrations are different from accepting
arbitrary HTML via meta tags. Discovery tags alone do not enroll a new provider.
X's [official oEmbed documentation source](https://github.com/xdevplatform/docs/blob/main/x-for-websites/oembed-api.mdx)
illustrates the direction of that integration.

### Richer video and document options

**X Player Cards:** the [official sample landing page](https://github.com/xdevplatform/cards-player-samples/blob/main/player/page.html)
advertises an HTTPS player, image and dimensions; its
[player container](https://github.com/xdevplatform/cards-player-samples/blob/main/player/container.html)
contains an actual MP4 `<video>` player. That is evidence for an audio/video
implementation pattern, not a loophole for embedding a complete StoryMap,
game, form or arbitrary interactive website. Current Player Card support,
eligibility/approval and playback behavior remain unverified: the retrieved
current X documentation tree did not contain a Cards section, and the
[legacy Player Card reference](https://developer.x.com/en/docs/x-for-websites/cards/overview/player-card)
was unreachable. Missing documentation does not establish discontinuation.

For a separate real-video implementation, the relevant fields are
`twitter:card=player`, `twitter:player` (the HTTPS HTML video-player URL),
`twitter:player:width`, `twitter:player:height`, title, description and image.
An optional `twitter:player:stream` must identify actual supported media, not an
HTML page, with the matching `twitter:player:stream:content_type`. Keep the
landing page as the shared URL and put the interactive-app link in the post
text and on that page. Do not rely on a clickable overlay inside the player.
Neither no-click autoplay nor arbitrary interaction is established.

**Facebook / other OG consumers:** the
[Open Graph specification](https://github.com/facebook/open-graph-protocol/blob/master/content/index.markdown)
defines `og:video` as a video URL, with `og:video:secure_url`,
`og:video:type`, `og:video:width` and `og:video:height`. With a real public HTTPS
MP4, `video/mp4` and actual pixel dimensions can describe a video alongside
the existing title, description, page URL and poster image. This does **not**
guarantee Facebook playback. Current external-video/domain eligibility rules
could not be established; neither universal acceptance nor a universal
allowlist requirement is claimed. Test the
[webmaster video behavior](https://developers.facebook.com/docs/sharing/webmasters/#video)
before treating preference **2** as available. A raw MP4 link may open a media
file instead of linking back to the app.

No MP4 or genuine video-player asset was supplied in this repository, so the
deployed HTML deliberately does not advertise imaginary video URLs, wrong
dimensions, or an HTML iframe as `video/mp4`. Hosting a real preview later can
enable a separate video experiment; it cannot force every platform to play it.
Native video uploads with text linking back to the app are another option,
but are not the pasted-URL/meta-tag workflow.

**LinkedIn PDFs:** a native swipeable, multi-page document post is a document
upload workflow, not a metadata card. Pasting a PDF URL, linking an HTML PDF
viewer, adding repeated `og:image` values, or inventing an `og:document` tag
does not establish automatic conversion to that format. For URL-only sharing,
use an HTML landing page with a single designed poster (for example a montage
of PDF pages), then open the document/viewer on the app. For a native carousel,
manually upload the document and include the app URL in the accompanying text.
The identified official
[Upload and share documents on LinkedIn](https://www.linkedin.com/help/linkedin/answer/a518909/upload-and-share-documents-on-linkedin?lang=en)
reference could not be fetched; the workflow distinction is the recommendation,
not a claim of tested current PDF-link rendering.

### Item-specific previews

Every query string currently receives the same static HTML, image and root
canonical/OG URL. A share intent does not change this. In particular,
`?itemId=…` and `?storyId=…` select the browser's interactive item only; they
must not be advertised as producing item-specific previews.

For distinct item previews on GitHub Pages, publish distinct generated HTML
paths with their own title, description, poster, canonical URL and `og:url`.
Otherwise move metadata generation to request-time server/edge hosting and
validate the item before returning HTML. JavaScript mutations or
`?twitter:card=player` parameters cannot change crawler-readable metadata on
this static host.

## Implemented preview metadata

The initial HTML contains the title, description, canonical URL, Open Graph
website metadata, site name, locale, and an absolute HTTPS PNG URL. Structured
image metadata declares its actual MIME type and dimensions: `image/png`,
1200 × 630 (about 315 KiB). X receives explicit title, description, image, image
alt text, and `twitter:card=player`. The HTTPS `twitter:player` URL points to this
site's full interactive embed, with declared player dimensions of 640 × 640.
These card dimensions do not enforce an aspect ratio on the website's iframe.

These are descriptions for a crawler, not instructions that force a particular
feed layout. Platforms may omit the description, crop the image, suppress the
preview, or retain cached metadata. The shared page URL leads back to the
interactive app; share the page, not `card.png` or a future raw MP4.

The original Player Card experiment has been restored instead of the X
large-image card. It requests the complete StoryMaps website rather than an
audio/video player; X may reject or suppress it. A play button and interactive
in-feed playback cannot be guaranteed. The Open Graph image preview is unchanged
for other consumers, and no `og:video*` or direct media stream is advertised.
The artwork's Player Card label is not a claim of X player support.
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
