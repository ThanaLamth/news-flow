# Internal Linking Spec

Specification for internal-link selection and insertion in the news workflow.

## Purpose

The workflow needs a dedicated internal-linking capability so articles consistently include strong internal authority signals.

Goal:

- insert at least `5` high-quality internal links per article whenever suitable candidates exist
- keep anchor text natural and descriptive
- avoid weak or forced links

## Why This Needs Its Own Skill

Internal linking is not just a formatting task.

It requires:

- topic matching
- category matching
- pillar-content preference
- anchor text judgment
- placement rules
- final QA

Because of that, internal linking should be treated as a dedicated workflow skill/module.

## Existing Rules We Already Have

Current guidance already exists in:

- `news-workflow-main/linking-rules.md`
- site config `internal_links` settings
- existing brief logic that can pick candidate internal links
- `writing-qc`, which already checks internal-link quality

This spec turns that existing knowledge into a formal new-flow component.

## Target Policy

### Required Count

- target: `5` internal links
- minimum acceptable: `5` where relevant candidates exist
- if fewer than `5` good candidates exist, insert only valid links and log the shortfall

Do not force junk links just to hit the count.

## Core Rules

1. Never put any links in the first intro paragraph.
2. Use only genuinely relevant internal posts.
3. Prefer same-topic and same-category links over generic recent posts.
4. Prefer pillar content when available and relevant.
5. Anchor text must be descriptive and flow naturally in the sentence.
6. Never use weak anchor text like:
   - `click here`
   - `this article`
   - `source`
   - `read more`
7. Insert links where they help reader understanding, not as decoration.

## Placement Rules

Internal links should be inserted in:

- body paragraphs after the lead
- explanatory sections
- historical context sections
- related-market-context sections

Avoid:

- intro paragraph
- headings
- figure captions unless explicitly required
- stacked links in one sentence
- multiple links to the same destination unless justified

## Candidate Selection Logic

The internal-link skill should score candidate posts using:

1. same token/project/entity
2. same topic type
3. same category
4. prior related event
5. pillar-content bonus
6. freshness within site limit

## Suggested Candidate Inputs

For each candidate post:

- title
- URL
- category
- publish date
- pillar flag or `pillar_post_id`
- relevance explanation

## Suggested Relevance Order

Highest to lowest:

1. same asset/project
2. same exact story theme
3. same market/event cluster
4. same category with meaningful context value
5. site pillar page

## Anchor Text Rules

Anchor text must:

- describe the target topic
- match surrounding sentence meaning
- avoid generic phrasing
- avoid exact same anchor repeated across the article

### Good Examples

- `USDD’s overcollateralized model`
- `stablecoin liquidity across DeFi`
- `TRON ecosystem expansion`
- `previous TVL growth milestone`

### Bad Examples

- `this article`
- `read here`
- `click here`
- `source`

## Skill Output

The internal-link skill should produce a structured artifact before writing final copy.

Recommended file:

- `jobs/<job-id>/artifacts/04b-internal-links.json`

### Recommended Schema

```json
{
  "job_id": "job-2026-03-31-001",
  "site_id": "coincu",
  "target_count": 5,
  "selected_links": [
    {
      "title": "USDD expands multi-chain liquidity strategy",
      "url": "https://example.com/usdd-multi-chain-liquidity/",
      "category": "DeFi",
      "relevance_reason": "Same stablecoin project and protocol-growth theme",
      "suggested_anchor_text": "USDD’s multi-chain liquidity strategy",
      "placement_hint": "growth drivers section",
      "is_pillar": false,
      "score": 92
    }
  ],
  "shortfall": 0,
  "notes": ""
}
```

## Workflow Placement

Recommended order:

1. `SITE IDENTITY`
2. `RESEARCH`
3. `DATA VISUALS PLAN`
4. `SEO & OUTLINE`
5. `INTERNAL LINK SELECTION`
6. `WRITING`
7. `WRITING-QC LOOP`

This keeps internal links intentional instead of bolting them on at the end.

## Integration Responsibilities

### In `news-workflow-main`

Add:

- `lib/internal-link-runner.js`
- `lib/internal-link-scorer.js`
- `lib/internal-link-inserter.js`

Responsibilities:

- fetch candidate posts from site source
- score candidates
- select best five
- suggest anchor text and placement
- insert links into draft HTML

## Insertion Strategy

Two-step approach:

1. `selection`
   - choose the best five targets
2. `insertion`
   - place them naturally in the draft

Do not combine scoring and insertion blindly in one pass.

## Quality Checks

`writing-qc` should verify:

- article has at least 5 internal links if enough candidates existed
- no links in intro paragraph
- anchor text is descriptive
- links are relevant
- no duplicate destination spam
- no obvious forced-link wording

## Shortfall Handling

If fewer than five good candidates exist:

- insert only the valid candidates
- log `shortfall > 0`
- include a reason in the internal-link artifact

Example reasons:

- `not enough same-topic history`
- `site archive too thin`
- `no valid pillar post`

## Automation Threshold

This skill is suitable for high automation earlier than capture, because:

- internal links are easier to validate than screenshots
- relevance scoring can be refined from QC feedback

But it still needs QC oversight for:

- forced anchor text
- irrelevant links
- overlinking

## Definition Of Done

The internal-link skill is implemented when:

- the workflow selects up to 5 strong internal links per article
- links are inserted outside the intro paragraph
- anchor text is natural and descriptive
- shortfalls are logged explicitly
- QC checks link quality before publish
