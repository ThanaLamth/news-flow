# BTC Derivatives Hit Extreme Hedging Range: VanEck

- Date: `2026-04-04`
- Job ID: `0f371958a150`
- Site: `coincu`
- Pipeline mode: `phased`
- Final status: `failed`

## What this test proves

- The new workflow skeleton now initializes correctly for phased jobs.
- The job created the expected workflow artifacts under `artifacts/`.
- Research completed and synced into the workflow artifact contract.
- The run stopped before writing/publishing, so nothing was posted to Coincu.

## Where it stopped

The job failed at `Data Visuals`.

Failure reason:

```text
Phase "Data Visuals" failed (exit 1): {"type":"error","status":400,"error":{"type":"invalid_request_error","message":"The 'claude-sonnet-4-6' model is not supported when using Codex with a ChatGPT account."}}
```

## Files to inspect

- `artifacts/workflow-state.json`: workflow phase state
- `artifacts/01-site-identity.json`: site identity artifact
- `artifacts/02-research-brief.json`: synced research artifact
- `data-visuals.json`: generated data visuals payload before failure
- `publish.json`: confirms publishing never started
- `job.log`: tail of the real run log

## Notes

- `post_id` and `post_url` are still `null`
- `create_post` remains `pending`
- This was a real backend test run, not a published article
