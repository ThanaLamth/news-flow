# Capture Review Spec

Specification for the semi-automated capture confirmation layer in the news workflow.

## Purpose

During the first rollout phase, `DATA VISUALS EXECUTION` should not be fully trusted to auto-accept screenshots.

Instead:

1. captures are generated automatically
2. a human reviews each capture
3. the review decision is logged in a structured format
4. the review history is later used to decide which capture classes can become fully automated

This phase sits between:

- `DATA VISUALS EXECUTION`
- `FINAL ARTICLE PATCH-UP`

## Temporary Workflow

1. `SITE IDENTITY`
2. `RESEARCH`
3. `DATA VISUALS PLAN`
4. `SEO & OUTLINE`
5. `WRITING`
6. `WRITING-QC LOOP`
7. `DATA VISUALS EXECUTION`
8. `HUMAN CAPTURE CONFIRM`
9. `FINAL ARTICLE PATCH-UP`
10. `THUMBNAIL`
11. `PUBLISHING`

## Review Goal

For each generated capture, the human should decide whether the screenshot is good enough to be inserted into the article.

## Human Review Actions

Allowed actions:

- `approve`
- `retry`
- `skip`
- `replace_source`

Meaning:

- `approve`
  - screenshot is correct and should be used
- `retry`
  - same source/platform should be captured again
- `skip`
  - no visual should be used for this capture item
- `replace_source`
  - the planned source is wrong and a different source URL should be used

## Reason Codes

Allowed `reason_code` values:

- `correct_visual`
- `wrong_source`
- `wrong_visual`
- `bad_crop`
- `challenge_page`
- `overlay_or_popup`
- `unreadable`
- `duplicate`
- `not_needed`
- `stale_data`
- `other`

## Per-Job Review Artifact

Each article job should write:

- `jobs/<job-id>/artifacts/07-capture-review.json`

### Recommended Schema

```json
{
  "job_id": "job-2026-03-31-001",
  "site_id": "coincu",
  "article_title": "USDD TVL Hits $1.93 Billion Record High as DeFi Liquidity Expands",
  "reviewed_at": "2026-03-31T10:15:00Z",
  "reviewer": "human",
  "captures": [
    {
      "capture_id": "capture-001",
      "claim_supported": "USDD TVL reached $1.93 billion",
      "source_url": "https://defillama.com/...",
      "platform_key": "defillama",
      "capture_key": "tvl_chart",
      "selector_used": ".chart-container",
      "mode": "element",
      "page_title": "USDD - DeFiLlama",
      "challenge_detected": false,
      "cookies_applied": false,
      "output_path": "07-captures/usdd-tvl-chart.png",
      "metadata_path": "07-metadata/usdd-tvl-chart.json",
      "decision": "approve",
      "reason_code": "correct_visual",
      "review_note": "",
      "replacement_source_url": null,
      "review_duration_seconds": 9
    }
  ]
}
```

## Global Learning Log

In addition to the per-job review file, append one flat event per reviewed capture to:

- `capture-review-history.jsonl`

### JSONL Example

```json
{"job_id":"job-2026-03-31-001","site_id":"coincu","platform_key":"defillama","capture_key":"tvl_chart","decision":"approve","reason_code":"correct_visual","source_url":"https://defillama.com/...","reviewed_at":"2026-03-31T10:15:00Z"}
{"job_id":"job-2026-03-31-002","site_id":"coincu","platform_key":"arkm","capture_key":"entity_overview","decision":"skip","reason_code":"challenge_page","source_url":"https://intel.arkm.com/...","reviewed_at":"2026-03-31T10:22:00Z"}
```

## Minimum Fields To Log

At minimum, each reviewed capture must log:

- `job_id`
- `site_id`
- `article_title`
- `capture_id`
- `claim_supported`
- `source_url`
- `platform_key`
- `capture_key`
- `selector_used`
- `output_path`
- `metadata_path`
- `decision`
- `reason_code`
- `reviewed_at`

## Recommended Review UI

The review popup should show:

- image preview
- source URL
- claim supported
- platform key
- capture key
- decision buttons
- optional note field

Recommended controls:

- `Approve`
- `Retry`
- `Skip`
- `Replace Source`
- reviewer note text box

## Suggested Automation Thresholds

After about one month of reviews, evaluate capture classes by:

- `platform_key`
- `capture_key`
- source domain
- selector used

Do not auto-approve a capture class unless it has enough history.

Suggested thresholds:

- at least `20` reviewed captures
- at least `90%` approval rate
- less than `10%` retry rate
- less than `5%` challenge-page failures

## Tier Model

After enough review data, classify capture paths into:

### Tier 1: Auto-Approve

- high approval rate
- low retry rate
- low challenge rate

### Tier 2: Auto-Capture, Human Review Required

- often usable
- still unstable enough to need confirmation

### Tier 3: Never Fully Automate

- frequent blocking
- ambiguous visuals
- unstable layouts

## Decision Rules For Patch-Up

- only `approve`d captures should be inserted into the article automatically
- `retry` should requeue the same capture task
- `skip` should leave the article without that visual
- `replace_source` should block patch-up until a new source is selected or the visual is skipped

## Why This Matters

This review layer creates the data needed to turn semi-automation into high-confidence automation later.

Without structured review logs, a month of human approvals will not produce reusable operational knowledge.
