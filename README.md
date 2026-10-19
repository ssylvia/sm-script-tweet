# sm-script-tweet

Static ArcGIS StoryMaps iframe-embed demo with an **experimental** X Player Card.

Demo URL after deployment: https://ssylvia.github.io/sm-script-tweet/

## Setup and deployment

1. Ensure the story permits iframe embedding.
   The story and its dependent content must be publicly accessible.
   This repository cannot change those settings.
2. In this repository's **Settings → Pages → Build and deployment**, select
   **GitHub Actions** as the source.
3. Push to `main` (or run **Deploy demo to GitHub Pages** manually).
   The workflow publishes only `site/`, with no build or package installation.
   Allow the deployment branch in the `github-pages` environment if protection
   rules are configured.

The embed uses an iframe with `autoplay=true` in the story URL and autoplay
permission enabled. Browser autoplay policies still apply.
Its responsive 960:600 aspect ratio matches the declared X Player Card dimensions.
The configured story is https://storymaps.arcgis.com/stories/fffa28a9ea7447839ee9debfd9693bb8.

To preview locally, run:

```sh
python3 -m http.server 8000 --directory /home/runner/work/sm-script-tweet/sm-script-tweet/site
```

Open http://localhost:8000/. There is no existing test/build/lint infrastructure.

## X/Twitter Player Card research

The initial HTML hardcodes the title, description, canonical URL, Open Graph
preview, PNG image, and `twitter:card=player` metadata. `twitter:player` points to
the HTTPS demo itself, with a declared 960 × 600 player. There is no iframe
pointing back to the demo and no client-side metadata generation.
The hardcoded `twitter:site` attribution is `@ssylvia`; confirm this is the
intended X account before sharing.

**This is a feasibility experiment, not a supported way to run arbitrary websites
in X.** X's [Player Card documentation](https://developer.x.com/en/docs/x-for-websites/cards/overview/player-card)
describes audio/video players; an iframe-embedded StoryMap is not an audio/video
player. Metadata alone does not guarantee eligibility, approval, or interactive
rendering. Do not add a fake `twitter:player:stream`: that field is for actual
media streams. Current X documentation and the live story could not be fetched
from the implementation environment, so current approval rules and live playback
remain unverified.

After deployment:

- Check that the page, `card.png`, and story load anonymously over HTTPS.
- Inspect **View Source** to confirm the hardcoded metadata is present without
  JavaScript. If hosting under another account, repository, or custom domain,
  update every absolute page/image URL in `site/index.html`.
- Submit the demo URL to the [Card Validator](https://cards-dev.twitter.com/validator)
  if available, and test a real post on X web/mobile. A validator response alone
  does not prove interactive playback works; X can cache previous metadata.
- If X rejects the interactive player, change `twitter:card` to
  `summary_large_image` and remove the three `twitter:player*` tags. Share the
  image/link card so readers open the live story on this site instead.

The direct story link remains visible if scripts are disabled or the embed is
blocked.
