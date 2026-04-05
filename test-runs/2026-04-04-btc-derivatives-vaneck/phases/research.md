# Phase 1 — Research & Data Gathering

Execute these steps **in order**. Each step builds on previous findings. Use WebSearch for searches and WebFetch for fetching URLs/APIs.

**Target: Target proof set for a general story: 8-9 searches and 5-6 fetches max, stopping once the minimum proof set is satisfied.**

**Efficiency rules:**
- Do NOT make redundant API calls. If one API returns sufficient data, skip the next.
- Steps marked **(conditional)** should ONLY run if the condition is met.
- Combine related searches into a single query when possible.
- Prefer 3-5 searches and 1-3 fetches for a normal story. Exceed that only when verification genuinely requires it.
- If `WebFetch` returns `403`, `503`, or anti-bot HTML, do NOT keep retrying the same URL. Use your assigned Chrome MCP to load the page with a real browser, or switch to an equivalent primary/secondary source.
- A blocked news article is not a hard failure by itself. Continue the research with browser fallback, search-result summaries, official docs, APIs, or alternate reputable outlets.

**Story type:** `general`
- **Minimum proof set:** 1 primary source, 1 credible secondary confirmation, 1 optional context source if needed
- **Source priority:** Tier 1 original source or named statement. Tier 2: one direct confirmation source. Tier 3: optional reaction only if it adds real evidence.
- **Domain priority:** Prefer original documents, official announcements, and direct data sources before commentary.
- Do NOT fetch Tier 3 commentary while Tier 1 evidence is still missing.
- Competitor differentiation is optional until the core claim is already verified.
- Stop as soon as the research brief can be completed with a clear verified, partial, or unverified verdict.

### STEP 1 — Find Primary Source (1-2 searches + 1 fetch)

1. **WebSearch:** `"<headline>" official statement OR announcement OR press release`
   → Find the PRIMARY source (SEC filing, company blog, protocol governance post) — not news rewrites
2. **WebSearch:** `"<headline>" latest news`
   → Get the freshest multi-outlet coverage
3. **WebFetch:** Read the ORIGINAL source document found in search #1
   → Extract exact quotes, exact numbers, exact dates from the primary source

### STEP 2 — Market Data (conditional, 1-2 fetches max)

**Only execute if the headline mentions a specific coin/token/protocol.** Skip entirely for general industry news (e.g., regulation, exchange corporate news, macro policy).

4. **WebFetch:** `https://api.coingecko.com/api/v3/coins/<coin_id>` (includes price, market cap, volume, supply, ATH, 24h change — no need for separate `/simple/price` call)
5. **WebFetch (conditional):** If DeFi-related → `https://api.llama.fi/protocol/<protocol>` for TVL data
   If BTC-related → `https://mempool.space/api/v1/fees/recommended` for fee data
   Otherwise → `https://api.alternative.me/fng/?limit=1` for Fear & Greed Index (only if market sentiment is relevant to the story)

### Free APIs Reference Table

| API | URL Pattern | Data |
|-----|-------------|------|
| CoinGecko detail | `api.coingecko.com/api/v3/coins/<id>` | Price, 24h change, market cap, volume, supply, ATH, description |
| DeFiLlama protocol | `api.llama.fi/protocol/<name>` | TVL, chain breakdown, historical |
| Fear & Greed | `api.alternative.me/fng/` | Current index + historical |
| Mempool fees | `mempool.space/api/v1/fees/recommended` | Current Bitcoin fee estimates |

### STEP 3 — Expert Reactions & Community Sentiment (follow the EXPERT QUOTE STRATEGY block)

The system prompt contains an **EXPERT QUOTE STRATEGY** block with topic-matched expert accounts, research report sources, and the oEmbed pre-fetch rule. Follow that block for Steps A, B, and C.

Key rules for this step:
- Execute Step A (targeted X/Twitter search) using the accounts listed in that block — not a generic search.
- Execute Step B (research report search) only when a data-backed expert quote would materially strengthen the article.
- Execute Step C (community sentiment) only when controversy or community backlash is part of the story.
- For every tweet URL you find worth quoting, immediately fetch its oEmbed HTML per the rule in that block and store it as `embed_html` in `expert_quotes[]`.
- Never search for expert reactions before the core claim (Step 1) is verified.

### STEP 4 — Regulatory & Legal Context (conditional, 0-2 searches + 1 fetch)

**Only execute if the headline involves regulation, legal action, government policy, or compliance.** Skip entirely otherwise.

8. **WebSearch:** `"<entity>" site:sec.gov OR site:federalregister.gov ruling OR lawsuit OR enforcement action`
   → Regulatory documents and legal precedents in one search
9. **WebFetch:** Read the actual filing/document found above
   → Link to SEC.gov or official documents massively boosts E-E-A-T

### STEP 5 — Competitor Differentiation (0-1 search + 1 fetch)

10. **WebSearch:** `"<headline keywords>" site:coindesk.com OR site:theblock.co OR site:blockworks.co`
    → See existing coverage angles
11. **WebFetch:** Read the top competitor article (just 1 — scan for angle gaps, not full analysis)
    → Identify: what angle is NOT covered yet → this determines your UNIQUE ANGLE

### STEP 6 — Fact Verification (0-1 search)

12. **WebSearch:** `"<key claim or number from research>" <entity> -site:<original source>`
    → Verify the single most important claim/number appears in multiple independent sources

**Rules:**
- Flag any claim that appears in only 1 source as "unverified"
- If a number differs across sources, use the PRIMARY source value from Step 1
- Never publish unverified dates or dollar amounts
- Once the core facts are verified, stop researching and move on.

### Research Output Format
After all steps, compile a structured research brief:
```
RESEARCH BRIEF:
- Primary source: [URL and key facts from original document]
- Verified facts: [list of facts confirmed across 2+ sources]
- Unverified claims: [facts found in only 1 source — mark with ⚠️]
- Market data: [prices, volumes, changes — from CoinGecko API]
- Sentiment: [Fear & Greed score, social sentiment summary]
- Expert quotes: [attributed quotes with source links]
- Regulatory context: [relevant filings/rulings if applicable]
- Competitor gaps: [what existing articles miss — OUR unique angle]
- Key statistics: [hard numbers with source attribution]
```
