# Phase 2 — SEO Analysis & Outline

### Step 1: Analyze SERP Competition
From the competitor analysis in Phase 1 Step 5, identify:
- What angles every competitor covers (skip these or cover better)
- What nobody covers well (this is your opportunity)
- What data/evidence competitors cite vs. what you found in research

### Step 2: Determine Article Scope
Based on research findings, decide what this article NEEDS to cover — nothing more.

Ask: "What does the reader need to understand this story?" Only include sections that answer that question.

**Possible sections** (use only what the research supports):
- The core event/news — always
- Why it matters — only if the impact is non-obvious
- Market data — only if price/volume movement is significant and relevant
- On-chain evidence — only if blockchain data proves or disproves a claim
- Expert reaction — only if someone credible said something that adds insight
- Regulatory/legal context — only if regulation is part of the story
- Historical precedent — only if a meaningful comparison exists
- Outlook — only if there are concrete upcoming events or data points

**Do NOT include a section just because the template has it.** If research found no meaningful expert reaction, there is no expert section. If the news is self-explanatory, there is no "why it matters" section.

### Step 3: Plan Evidence Placement
For each section, identify what specific evidence (data, quotes, links, on-chain proof, embedded tweets) supports it. If a section has no evidence to support it, remove the section.

---

# Phase 3 — Article Writing

### Writing Principles

**Core rule: Write exactly as much as the research supports. Then stop.**

- **Tone:** Follow the Writing Voice directives from the Editorial Identity section in CLAUDE.md. If no directives are set, default to Reuters/Bloomberg neutral wire-service tone.
- **Format:** HTML — `<h2>`, `<p>`, `<ul>`, `<li>`, `<strong>`, `<em>`, `<blockquote>`
- **No first H2:** WordPress uses the post title as H1. Article starts with `<p>`.
- **Word count range: 1200–2500 words.** Stay within this range. A clear breaking news story can be at the low end; a deep regulatory analysis at the high end. Length follows substance, but respect the bounds.
- **No financial advice:** Never say "you should buy/sell"
- **No em-dashes (—):** Use commas or semicolons instead

**Paragraph discipline:**
- Each paragraph makes ONE point, supported by evidence
- If a paragraph doesn't contain a fact, a number, a quote, or a direct insight — delete it
- **Maximum 2-3 sentences per paragraph (~40-60 words).** If a paragraph exceeds 3 sentences, split it. No exceptions.
- One-sentence paragraphs are fine for impactful standalone facts
- No transition filler ("It's worth noting that...", "Interestingly...", "In the broader context...")
- Visual scannability is critical — crypto readers skim. Dense paragraphs lose readers.

**Heading discipline:**
- Headings are navigation aids, not decoration. Use them only when they help the reader find information faster.
- If the article is short and flows naturally without H2s, don't force them in.
- If the article covers multiple distinct topics, use H2s to separate them clearly.
- Never use a heading that just restates what the paragraph says. A heading should tell the reader what VALUE this section provides.
- BAD: `<h2>Market Data</h2>` → GOOD: `<h2>Bitcoin Dropped 8% in 4 Hours After the Announcement</h2>`
- BAD: `<h2>Expert Opinions</h2>` → GOOD: `<h2>Analysts Split on Whether SEC Will Appeal</h2>`

**Evidence standard:**
- Every major claim must be backed by: a source link, a number, on-chain data, or an attributed quote
- If you can't prove it, don't write it
- Prefer specific numbers over vague language: "dropped 8.3%" not "dropped significantly"

### Link Insertion

#### Internal Links (Smart Matching)
Use WordPress MCP to find genuinely related articles — not random recent posts.

1. **Fetch candidates:** `wp_get_posts` with `per_page=50, status=publish, after=2026-01-04`
2. **Only link to posts published within the last 90 days** (the `after` filter above enforces this at the API level).
3. **Match by relevance**, not recency. **Prefer posts in the same category** as the current article. Look for posts about:
   - The same coin/token/project mentioned in the headline
   - The same topic category (regulation, DeFi, exchange, hack, etc.) — **preferred**
   - Related events (e.g., previous enforcement actions, prior price moves)
4. **NEVER put any links (internal or external) in the first intro paragraph.** The lead paragraph is bold, standalone, and link-free.
5. **Insert 3–5 internal links** with natural anchor text that flows in the sentence
   - GOOD: `Bitcoin has <a href="/bitcoin-etf-inflows-march/">seen sustained ETF inflows</a> throughout March`
   - BAD: `Read our <a href="/bitcoin-etf-inflows-march/">related article</a>`
6. Only link where it genuinely helps the reader understand context

#### External Links

**Rule 1: Always link to PRIMARY sources, not news rewrites.**

Primary sources include: official documents, regulatory filings (SEC.gov, congress.gov), company/project announcements, original datasets, dashboards, research reports, on-chain explorers, direct market data pages.

Do NOT link to secondary news sites (CoinDesk, CoinTelegraph, TheBlock, Decrypt) that merely rewrite or summarize the same story. If a news outlet is only reporting the story, trace the information back to the original document, dataset, or official announcement and link to that instead.

**Exception:** You may link to a secondary news outlet only when it is the original publisher of unique information:
- Exclusive interviews conducted by that outlet
- Investigative reports or original journalism
- Original analysis not found elsewhere
- Direct quotes from that publication's own reporting
- Breaking news where that outlet is the first and only source

| Link Type | When to Use | Example |
|-----------|-------------|---------|
| Official filing/announcement | Always — the original source | SEC.gov filing, company blog post, governance proposal |
| Data/dashboard | When citing a specific number | CoinGecko price page, DeFiLlama TVL page, Etherscan tx |
| On-chain explorer | When citing blockchain data | Etherscan tx, Mempool.space, Solscan |
| Original research | When citing analysis | Glassnode report, Chainalysis study, Messari research |
| Expert's own platform | When quoting someone | The original tweet, blog post, or podcast |

**Rule 2: Embed links naturally in contextual phrases — no attribution phrases for data.**

- **Raw data sources** (price, TVL, index score, market cap, volume): Embed the link in the data point or metric name. Do NOT write "according to CoinGecko" or "per Alternative.me."
  - BAD: `traded at $68,819, <a href="...">according to CoinGecko data</a>`
  - BAD: `the index sits at 8, <a href="...">per Alternative.me</a>`
  - GOOD: `traded at <a href="...">$68,819</a> at press time`
  - GOOD: `the <a href="...">Fear & Greed Index</a> dropped to 8`
  - GOOD: `total value locked reached <a href="...">$50.2 billion</a>`

- **Analysis, reporting, or quotes** (editorial content where credibility matters): Mention the source explicitly.
  - GOOD: `Matrixport analysts noted a divergence in funding rates`
  - GOOD: `<a href="..." target="_blank" rel="noopener">a Glassnode report</a> highlighted declining exchange reserves`

- Never use "click here", "this article", or "source"
- Only link to pages that actually contain the cited information

### Embedded Tweet / X Post

**When:** The primary source or a key expert reaction IS a tweet/X post.
**Why:** Embedded tweets are visual proof — readers see the original statement, not a paraphrase. Google treats embedded social content as E-E-A-T signal.

**Step 1: Fetch oEmbed HTML** (required for rich card rendering)

Use WebFetch to call the Twitter oEmbed endpoint:
```
WebFetch: https://publish.twitter.com/oembed?url=https://x.com/username/status/1234567890&omit_script=true
```

This returns JSON with an `html` field containing the full blockquote with tweet text, attribution, links, and image references. Extract the `html` value.

**Step 2: Insert the oEmbed HTML into the article**

Use the `html` from the oEmbed response directly. It looks like:
```html
<blockquote class="twitter-tweet"><p lang="en" dir="ltr">Tweet text here <a href="https://t.co/xxx">https://t.co/xxx</a> <a href="https://t.co/yyy">pic.twitter.com/yyy</a></p>&mdash; Author Name (@handle) <a href="https://twitter.com/handle/status/1234567890?ref_src=twsrc%5Etfw">Date</a></blockquote>
<p style="text-align:center;color:#888;font-size:13px;">Source: @username on X</p>
```

**Why oEmbed is required:** A bare `<blockquote class="twitter-tweet"><a href="..."></a></blockquote>` will NOT render as a rich card. Twitter's `widgets.js` needs the full tweet text, `lang`, `dir`, `t.co` links, and `pic.twitter.com` references inside the blockquote to render the rich card with profile picture, images, like counts, and reply buttons.

**IMPORTANT:**
- Do NOT use `[embed]...[/embed]` shortcode — WordPress auto-links the URL before processing the shortcode, breaking it
- Do NOT use `<figure class="wp-block-embed">` wrapper — it only works in the Block Editor
- Do NOT use bare blockquotes without tweet text — they render as empty cards
- The `widgets.js` script is loaded automatically by the Twitter Embed Loader plugin — do NOT include `<script>` tags in the post content (WordPress strips them)

**Rules:**
- Only embed tweets that ARE the story (announcements, official statements, key reactions)
- Maximum 2 embedded tweets per article — more feels like a tweet roundup, not journalism
- Always add a `<figcaption>` with attribution
- Always include context paragraph BEFORE the embed explaining why this tweet matters
- If the tweet contains the primary claim, still write it in your own words first, then embed as proof

**Do NOT embed tweets that:**
- Are just random community reactions
- Say the same thing you already wrote
- Are from unverified or low-follower accounts (unless they are the direct source)

### On-Chain Data as Evidence

**When:** The article makes claims about blockchain activity, whale movements, protocol metrics, or network health.
**Why:** On-chain data is verifiable, immutable proof. It separates real journalism from speculation.

#### Inline Data Callout (for key metrics)
```html
<div class="on-chain-data" style="background:#0d1117;border-left:3px solid #f7931a;padding:16px 20px;margin:20px 0;border-radius:4px;">
<p style="margin:0 0 8px;color:#f7931a;font-weight:600;font-size:14px;">ON-CHAIN DATA</p>
<ul style="margin:0;color:#e6edf3;">
<li><strong>Transaction hash:</strong> <a href="https://etherscan.io/tx/0x..." target="_blank" rel="noopener" style="color:#f7931a;">0xabc...def</a></li>
<li><strong>Amount:</strong> 15,000 ETH ($45.2M at time of transfer)</li>
<li><strong>From:</strong> Binance Hot Wallet → Unknown Wallet</li>
<li><strong>Block:</strong> #19,234,567 (March 9, 2026, 14:23 UTC)</li>
</ul>
</div>
```

#### When to Include On-Chain Evidence
| Claim Type | On-Chain Source | What to Show |
|------------|---------------|--------------|
| Whale movement | Etherscan/Blockchain.com tx link | Tx hash, amount, from/to, timestamp |
| Exchange flow | Etherscan address page | Wallet balance change, tx count |
| Smart contract event | Etherscan event logs | Contract address, method called, parameters |
| TVL change | DeFiLlama protocol page | Current TVL, % change, chain breakdown |
| Network congestion | Mempool.space | Fee estimates, pending tx count |
| Token burn/mint | Etherscan token tracker | Supply change, burn tx |

**Rules:**
- Only include on-chain data when it PROVES a claim in the article
- Always link to the explorer page so readers can verify themselves
- Include human-readable amounts (not just raw wei/satoshi)
- Add USD equivalent at time of event
- If you cannot find the specific transaction, say "according to blockchain data" with the explorer link to the address — never fabricate a tx hash

#### Block Explorer Links by Chain
| Chain | Explorer | URL Pattern |
|-------|----------|-------------|
| Ethereum | Etherscan | `etherscan.io/tx/{hash}` or `etherscan.io/address/{addr}` |
| Bitcoin | Mempool.space | `mempool.space/tx/{txid}` or `blockchain.com/explorer/transactions/btc/{txid}` |
| Solana | Solscan | `solscan.io/tx/{sig}` |
| BNB Chain | BscScan | `bscscan.com/tx/{hash}` |
| Polygon | Polygonscan | `polygonscan.com/tx/{hash}` |
| Arbitrum | Arbiscan | `arbiscan.io/tx/{hash}` |
| Base | Basescan | `basescan.org/tx/{hash}` |

### Article Structure

**Only fixed requirement:** Start with a bold `<p><strong>` lead paragraph with NO links. Everything after follows the research.

```html
<p><strong>Lead — the single most important fact from the story. What happened, to whom, and why it matters.
Include the primary keyword naturally. This paragraph must stand alone as a complete summary.</strong></p>

<!-- After the lead, structure follows the story. Examples: -->

<!-- Simple breaking news (no H2s needed if short): -->
<p>Supporting details, data, source attribution...</p>
<p>Context or reaction if meaningful...</p>

<!-- Complex story (H2s help readers navigate): -->
<h2>Descriptive heading that tells reader what they'll learn</h2>
<p>...</p>
<!-- Embed tweet/on-chain data HERE only if it proves a claim above -->

<!-- End with forward-looking info ONLY if concrete (upcoming dates, scheduled votes, etc.) -->
<!-- Do NOT end with vague "time will tell" or "remains to be seen" -->
```

**What NOT to include:**
- "In conclusion" / summary paragraph that repeats the lead
- "Time will tell" / "remains to be seen" / "only time will tell" closings
- Sections with no evidence backing them
- FAQ section unless there are genuinely useful unanswered questions

### Embedded Data Screenshots

If Phase 1.5 produced data visualization screenshots, embed them in the article using the prepared `<figure>` blocks.

**Placement rules:**
- Place each figure IMMEDIATELY AFTER the paragraph that references or discusses that data
- Never place two figures back-to-back without at least one paragraph of text between them
- Never place a figure at the very start or very end of the article
- The figure should feel like visual proof of the claim in the preceding paragraph

**Visual rhythm target:** Readers should never see more than 3-4 paragraphs without a visual break (chart, embedded tweet, on-chain callout, or data screenshot).

**Example placement:**
```html
<p>Spot Bitcoin ETFs recorded inflows of $1.42 billion over the past week, marking the highest weekly total in three months, <a href="https://sosovalue.com/..." target="_blank" rel="noopener">according to SoSoValue data</a>.</p>

<figure style="margin:24px 0;">
  <img src="https://example.com/wp-content/uploads/2026/03/btc-etf-flows.png"
       alt="Bitcoin ETF weekly net inflows showing $1.42 billion for the week ending March 7, 2026"
       style="width:100%;border-radius:6px;" />
  <figcaption style="text-align:center;color:#888;font-size:13px;margin-top:8px;">
    U.S. Spot Bitcoin ETF Weekly Net Flows. Source: SoSoValue
  </figcaption>
</figure>

<p>The last comparable inflow spike occurred in October 2025, when ETFs attracted $2.71 billion.</p>
```
