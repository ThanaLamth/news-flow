# Implementation Plan

Concrete build plan for implementing the full workflow across:

- `news-workflow-main`
- `writing-qc`
- `integrate-capture-v2`

## Target Workflow

1. `SITE IDENTITY`
2. `RESEARCH`
3. `DATA VISUALS PLAN`
4. `SEO & OUTLINE`
5. `WRITING`
6. `WRITING-QC LOOP`
7. `DATA VISUALS EXECUTION`
8. `FINAL ARTICLE PATCH-UP`
9. `THUMBNAIL`
10. `PUBLISHING`

## Implementation Strategy

Build this in layers:

1. establish common job artifacts and workspace layout
2. add a formal phase runner inside `news-workflow-main`
3. wire in `writing-qc`
4. wire in `integrate-capture-v2`
5. add final article patch-up
6. move thumbnail and publishing behind the new phase pipeline

Do not start with UI or dashboards. Start with artifact contracts and server-side orchestration.

## Phase 0: Define Contracts

### Goal

Make every phase read and write structured artifacts instead of passing unstructured text through ad hoc logic.

### Deliverables

- artifact directory layout
- file naming rules
- JSON schemas or interface definitions
- standard phase status model

### Workspace Layout

Each job should get:

- `jobs/<job-id>/artifacts/01-site-identity.json`
- `jobs/<job-id>/artifacts/02-research-brief.json`
- `jobs/<job-id>/artifacts/03-visual-plan.json`
- `jobs/<job-id>/artifacts/04-seo-outline.json`
- `jobs/<job-id>/artifacts/05-draft.html`
- `jobs/<job-id>/artifacts/06-qc-report.json`
- `jobs/<job-id>/artifacts/06b-draft-revised.html`
- `jobs/<job-id>/artifacts/07-captures/`
- `jobs/<job-id>/artifacts/07-metadata/`
- `jobs/<job-id>/artifacts/08-final-article.html`
- `jobs/<job-id>/artifacts/09-thumbnail.png`
- `jobs/<job-id>/artifacts/10-publish-result.json`

### Files To Add In `news-workflow-main`

- `lib/workspace-manager.js`
- `lib/artifact-types.js`

### Acceptance Criteria

- a new job can create a complete artifact directory
- every phase knows where to read input and write output
- artifacts are predictable enough to inspect manually

## Phase 1: Add Phase Runner

### Goal

Replace implicit pipeline flow in `server.js` with an explicit phase controller.

### Deliverables

- ordered phase execution
- per-phase status tracking
- phase retry policy
- stop/resume behavior

### Files To Add

- `lib/phase-runner.js`
- `lib/phase-definitions.js`

### Files To Change

- `server.js`

### Responsibilities

- load job context
- execute phases in order
- persist phase progress
- stop on hard failure
- allow restart from a later phase if artifacts already exist

### Acceptance Criteria

- a job can move through explicit phases
- phase start/end is logged
- failed jobs show which phase failed

## Phase 2: Site Identity Phase

### Goal

Make site identity a first-class artifact rather than an implicit config lookup.

### Inputs

- requested site key
- headline or source URL

### Outputs

- `01-site-identity.json`

### Reuse

- existing config loading from `news-workflow-main`

### Fields To Include

- `site_id`
- `domain`
- `mcp_server`
- `categories`
- `default_category_id`
- `author_strategy`
- `phase_models`
- `thumbnail_style`
- `seo_rules`
- `writing_rules`
- `publish_rules`

### Acceptance Criteria

- every later phase can operate without re-resolving site config from scratch

## Phase 3: Research Phase

### Goal

Produce a structured research brief that downstream phases can trust.

### Inputs

- `01-site-identity.json`
- job title
- source URL if provided

### Outputs

- `02-research-brief.json`

### Reuse

- current research and brief logic in `news-workflow-main`

### Fields To Include

- verified facts
- primary source
- supporting sources
- market data
- internal links
- angle candidates
- recommended angle
- unresolved questions

### Acceptance Criteria

- writing does not need to re-research from scratch
- SEO and visual planning can read the same brief

## Phase 4: Data Visuals Plan

### Goal

Use `integrate-capture-v2` planning before final draft creation.

### Inputs

- `01-site-identity.json`
- `02-research-brief.json`
- optional early draft fragment or generated article notes

### Outputs

- `03-visual-plan.json`

### Reuse

- `integrate-capture-v2/src/batch/plan-draft.ts`

### What This Phase Must Decide

- what claims need visual support
- where visuals should appear
- which platforms should be used
- which capture keys should be targeted
- what source labels and captions should be prepared

### Files To Add In `news-workflow-main`

- `lib/capture-runner.js`

### Acceptance Criteria

- each article has a structured list of proposed visuals before final writing

## Phase 5: SEO & Outline

### Goal

Separate editorial structure from article prose generation.

### Inputs

- `01-site-identity.json`
- `02-research-brief.json`
- `03-visual-plan.json`

### Outputs

- `04-seo-outline.json`

### Fields To Include

- primary title
- alternate titles
- slug direction
- meta description direction
- section list
- required internal links
- required evidence blocks
- placeholder positions for visuals

### Reuse

- `template-engine.js`

### Acceptance Criteria

- writing phase receives a fixed structure, not just a headline

## Phase 6: Writing

### Goal

Generate the first complete article draft.

### Inputs

- `01-site-identity.json`
- `02-research-brief.json`
- `03-visual-plan.json`
- `04-seo-outline.json`

### Outputs

- `05-draft.html`

### Requirements

- use verified facts only
- respect site voice and SEO constraints
- include placeholders for planned visuals

### Acceptance Criteria

- draft is structurally publishable even before QC

## Phase 7: Writing-QC Loop

### Goal

Make `writing-qc` an iterative gate, not a one-off check.

### Inputs

- `05-draft.html`
- `01-site-identity.json`
- `02-research-brief.json`

### Outputs

- `06-qc-report.json`
- `06b-draft-revised.html`

### Loop Logic

1. run QC on current draft
2. normalize the result into machine-readable issues
3. if pass, continue
4. if fail but fixable, rewrite the draft
5. rerun QC
6. stop after max attempts or acceptable threshold

### Files To Add

- `lib/qc-runner.js`
- `lib/qc-normalizer.js`

### Suggested Limits

- max 3 full QC passes
- hard stop on repeated factual/source failures

### Acceptance Criteria

- articles do not progress to capture or publishing without passing or explicit override

## Phase 8: Data Visuals Execution

### Goal

Run `integrate-capture-v2` for real screenshots and metadata only after the draft is stable.

### Inputs

- approved draft from phase 7
- `03-visual-plan.json`

### Outputs

- `07-captures/*`
- `07-metadata/*`

### Responsibilities

- execute real captures
- handle platform blocks
- persist output paths
- record capture metadata
- surface failures back to orchestration

### Acceptance Criteria

- a successful article has real visuals, not placeholders
- blocked captures are explicitly recorded

## Phase 9: Final Article Patch-Up

### Goal

Merge the final approved draft with real evidence artifacts.

### Inputs

- approved draft
- capture PNGs
- capture metadata

### Outputs

- `08-final-article.html`

### Responsibilities

- replace placeholders with actual image paths
- add captions and source notes
- adjust wording if captured evidence changes emphasis
- ensure final HTML remains valid

### Files To Add

- `lib/finalize-article.js`

### Acceptance Criteria

- final article can be manually checked without cross-referencing raw artifacts

## Phase 10: Thumbnail

### Goal

Generate the featured image only after the article angle is locked.

### Inputs

- `08-final-article.html`
- site thumbnail rules

### Outputs

- `09-thumbnail.png`

### Acceptance Criteria

- thumbnail matches final article framing and site style

## Phase 11: Publishing

### Goal

Publish only the final, patched, validated article.

### Inputs

- `08-final-article.html`
- `09-thumbnail.png`
- site identity artifact

### Outputs

- `10-publish-result.json`

### Reuse

- existing publishing logic in `news-workflow-main`

### Responsibilities

- create post
- upload media
- assign category
- set featured image
- set SEO fields
- persist publish result

### Acceptance Criteria

- publishing consumes artifacts from the new flow instead of bypassing them

## File-Level Build Order

Recommended implementation sequence in `news-workflow-main`:

1. `lib/workspace-manager.js`
2. `lib/artifact-types.js`
3. `lib/phase-definitions.js`
4. `lib/phase-runner.js`
5. `lib/qc-runner.js`
6. `lib/qc-normalizer.js`
7. `lib/capture-runner.js`
8. `lib/finalize-article.js`
9. patch `server.js` to use the new runner

## Repo Wiring Rules

### `news-workflow-main`

- owns orchestration
- owns site config
- owns publishing
- owns job state

### `writing-qc`

- returns review findings in a machine-readable structure where possible
- does not publish
- does not decide workflow order

### `integrate-capture-v2`

- plans visuals from article/evidence context
- executes captures
- writes PNGs and metadata
- does not own article publishing

## Risks

- QC output may be too unstructured for reliable automation until normalized
- capture sources will fail on challenge-heavy sites without persistent profiles
- final article patch-up can drift from validated wording if not carefully scoped
- current `server.js` may require careful refactoring because it already contains pipeline logic inline

## Recommended First Milestone

Implement a non-publishing prototype with these phases only:

1. `SITE IDENTITY`
2. `RESEARCH`
3. `SEO & OUTLINE`
4. `WRITING`
5. `WRITING-QC LOOP`
6. `FINAL ARTICLE PATCH-UP`

This proves orchestration first.

Then add:

7. `DATA VISUALS PLAN`
8. `DATA VISUALS EXECUTION`
9. `THUMBNAIL`
10. `PUBLISHING`

## Definition Of Done

The workflow is fully implemented when:

- one job runs through all phases in order
- every phase writes artifacts to a predictable workspace
- QC is iterative and enforced
- capture outputs are attached to the final article
- thumbnail generation happens after article stabilization
- publishing reads only the final artifacts
- phase failures are visible and resumable
