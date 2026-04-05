# Phase 4 — SEO Metadata & Thumbnail

### Step 1: Generate SEO Elements
Based on the article content and SERP analysis:

- **SEO Title** (max 60 chars): Must start with primary asset/keyword, include measured market verb, include context
- **H1** (max 70 chars): Expand on title with one clarifying clause
- **Meta Description** (150-160 chars): Explain cause-effect, include main keyword once, factual tone
- **URL Slug**: Kebab-case, 20-35 characters, descriptive
- **Category**: Select the most appropriate category from the site config

### Step 2: Generate Thumbnail
Run a Whisk preflight first:

```bash
python3 "/home/claudeuser1/pipeline-v2/pipeline-v2/whisk-token-refresh.py" --preflight
```

If preflight shows `token_valid: false` and Whisk cannot refresh because DevTools is unreachable or the browser profile is not logged in, do not loop on Whisk. Attempt generation once, then fall back immediately.

Run the Whisk image generation script:

```bash
python3 "/home/claudeuser1/pipeline-v2/pipeline-v2/whisk-token-refresh.py" --prompt "<IMAGE_PROMPT>" --output "<OUTPUT_PATH>"
```

**Image prompt formula:**
1. Extract the main subject from the headline
2. Represent it as one dominant central object
3. Apply visual modifiers based on headline type:
   - Company → logo or symbolic representation
   - Country → abstract flag color tones
   - Price crash → subtle red downward graph accent
   - Price surge → subtle green upward graph accent
   - Hack/security → fractured digital lines or glitch accents
   - Partnership → two connected elements with faint beam
   - Regulation → structured frame, lock motif, grid overlay
4. Append the style prompt:
   "Editorial risograph style. Institutional, retro-futuristic, screen-printed texture. Sentiment-driven colors: reds for bearish, greens for bullish, deep orange accent. Halftone dot overlay, misregistered layers."

### Step 3: Logo Overlay (REQUIRED)
**Always overlay the site logo onto the thumbnail before uploading.** This brands every article thumbnail and is NOT optional.

The logo URL is defined in the site config: `https://coincu.com/wp-content/uploads/2025/03/CCNews_white.png`.

```bash
python3 "/home/claudeuser1/pipeline-v2/pipeline-v2/overlay-logo.py" \
  --thumbnail "<THUMB_PATH>" \
  --logo-url "<LOGO_URL>" \
  --output "<FINAL_PATH>" \
  --logo-height 400 \
  --opacity 0.75 \
  --position southeast \
  --margin 20
```

Do not READ the output image for manual verification unless the overlay script reports a failure or the output file is missing. At most one retry is allowed with a larger logo or higher opacity.
