# Phase 1.5 — Data Visualization Capture

**Goal:** Capture 1-2 screenshot visuals of key data referenced in the article.

**Rule:** If no good visual data source exists for this article's topic, SKIP this phase entirely. Do not force generic charts. Not every article needs data screenshots.

**Efficiency rule:** Maximum 4 Chrome DevTools tool calls per screenshot (navigate → snapshot → optional click → screenshot). If a site shows Cloudflare/CAPTCHA on first snapshot, skip immediately — do not retry.

### Step 1: Identify Visual Candidates from Research

Review the research brief. Pick the **single most impactful** data visualization that proves a key claim. Only add a second if it covers a distinctly different data point.

**Priority order:**
1. Research-discovered sources (best) — the specific platform the article discusses
2. Known fallback sources — CoinGecko, DeFiLlama, Alternative.me

**Maximum: 0 screenshots. Prefer 1-2.**

**Sites to ALWAYS SKIP (bot detection):**
- SoSoValue — Cloudflare Turnstile CAPTCHA
- TradingView — blocks headless Chrome
- Any site showing "Verify you are human"

### Step 2: Capture Screenshots via Chrome DevTools

For each visual candidate, follow this sequence:

```
1. {your_chrome_mcp}__navigate_page → Navigate to the chart URL
2. {your_chrome_mcp}__take_snapshot → Get DOM tree. Check for:
   - Cloudflare/CAPTCHA → SKIP this site immediately
   - Cookie banners → click to dismiss
3. Find the chart/widget element uid in the DOM
4. {your_chrome_mcp}__take_screenshot with uid → Element-level screenshot
```

**CRITICAL:** Always use element-level screenshots (with uid). Never full-page. If you can't find a clean chart element, skip. After taking a screenshot, READ the PNG to verify it shows clean data.

**Quick recipes:**

**CoinGecko** (`coingecko.com/en/coins/{coin_id}`):
Navigate → wait 5s → snapshot → dismiss cookie banner if present → find chart wrapper div → screenshot by uid → verify PNG

**DeFiLlama** (`defillama.com/protocol/{protocol}`):
Navigate → wait 3s → snapshot → find TVL chart container → screenshot by uid

**Fear & Greed** (`alternative.me/crypto/fear-and-greed-index/`):
Navigate → wait 3s → snapshot → hide ad iframes via evaluate_script (`document.querySelectorAll('iframe').forEach(f=>f.style.display='none')`) → find gauge image → screenshot by uid → verify no ad overlays

Save screenshots to: `/home/thananewsflow/news-workflow-main/news-workflow-main/output/0f371958a150/`

### Step 3: Upload Screenshots

Use the `upload-image.py` helper script:

```bash
python3 "/home/claudeuser1/pipeline-v2/pipeline-v2/upload-image.py" \
  --file "<screenshot_path>" \
  --filename "<descriptive-name>.jpg" \
  --site coincu
```

The script auto-compresses, base64-encodes, and uploads. Do NOT manually compress or retry.

### Step 4: Prepare Figure Blocks

For each uploaded screenshot:

```html
<figure style="margin:24px 0;">
  <img src="{wordpress_media_url}"
       alt="{Descriptive alt text with key data point}"
       style="width:100%;border-radius:6px;" />
  <figcaption style="text-align:center;color:#888;font-size:13px;margin-top:8px;">
    {What the chart shows}. Source: {Platform Name}
  </figcaption>
</figure>
```

**Alt text must be descriptive** (e.g., `alt="Bitcoin price chart showing 8% decline from $97,500 to $89,600 over 7 days"`).

### Output for Phase 3

Write `/home/thananewsflow/news-workflow-main/news-workflow-main/output/0f371958a150/data-visuals.json` with this structure:

```json
{
  "required": true,
  "justification": "Why these visuals strengthen the article",
  "items": [
    {
      "type": "screenshot_plan",
      "title": "Platform Chart Name",
      "source_url": "https://...",
      "caption": "Descriptive caption. Source: Platform Name",
      "placement_hint": "Insert IMMEDIATELY AFTER the paragraph that states the specific [data type] claim about [topic] that this chart proves.",
      "value": "",
      "figure_html": "<figure style=\"margin:24px 0;\"><img src=\"{wordpress_url}\" alt=\"{descriptive alt text with key numbers}\" style=\"width:100%;border-radius:6px;\" /><figcaption style=\"text-align:center;color:#888;font-size:13px;margin-top:8px;\">{caption}</figcaption></figure>",
      "media_id": 12345,
      "url": "https://wordpress-media-url",
      "path": "/local/path/to/screenshot.png"
    }
  ]
}
```

**placement_hint must be specific**: state the exact family of claim and entity, e.g.
- `"Insert IMMEDIATELY AFTER the paragraph stating the spot price or market cap claim about Bitcoin that this CoinGecko chart proves."`
- `"Insert IMMEDIATELY AFTER the paragraph stating the TVL or protocol revenue claim about Uniswap that this DeFiLlama chart proves."`
