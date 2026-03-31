# News Flow

Workflow architecture for a full article pipeline built from:

- `news-workflow-main`
- `writing-qc`
- `integrate-capture-v2`

## Full Workflow

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

Short form:

`SITE IDENTITY -> RESEARCH -> DATA VISUALS PLAN -> SEO & OUTLINE -> WRITING -> WRITING-QC LOOP -> DATA VISUALS EXECUTION -> FINAL ARTICLE PATCH-UP -> THUMBNAIL -> PUBLISHING`

## What We Already Have

- `news-workflow-main` already provides the main workflow server, job queue, site config loading, prompt assembly, validation, RSS polling, and publishing hooks.
- Site identity is already modeled through per-site config files and a config loader.
- Research and article-brief generation already exist.
- Writing prompt construction already exists.
- Final output validation already exists.
- Schema generation already exists.
- `writing-qc` already provides reusable QC for factual clarity, sourcing, originality, heading quality, linking, readability, and visual-need detection.
- `integrate-capture-v2` already provides:
  - draft-level data-visual planning
  - live screenshot capture plus metadata
- The capture engine has already been shown to work for real visual sources and can be extended for new platforms.

## What Must Be Built

- A formal phase orchestrator that enforces the new sequence.
- A dedicated `WRITING-QC LOOP` controller:
  - draft
  - qc
  - fix
  - repeat
- A dedicated `DATA VISUALS PLAN` phase wired into the capture repo before final draft stabilization.
- A dedicated `DATA VISUALS EXECUTION` phase after the article passes QC.
- A dedicated `FINAL ARTICLE PATCH-UP` phase to insert:
  - real image paths
  - captions
  - source notes
  - metadata-derived adjustments
- A common artifact format across phases.
- Better failure handling for:
  - research failure
  - QC non-pass
  - capture block/challenge
  - thumbnail failure
  - publish failure
- A consistent per-job workspace layout.

## Recommended Architecture

Keep `news-workflow-main` as the orchestrator.

It should remain the top-level controller because it already owns:

- site configs
- job lifecycle
- RSS intake
- publishing

Treat `writing-qc` as a phase skill/service, not as the main application.

Treat `integrate-capture-v2` as a subordinate evidence engine used twice:

- once for `DATA VISUALS PLAN`
- once for `DATA VISUALS EXECUTION`

### Recommended Repo Roles

- `news-workflow-main` = orchestrator and publisher
- `writing-qc` = quality gate
- `integrate-capture-v2` = evidence planner and screenshot engine

## Proposed Phase Artifacts

Each article job should write structured artifacts like:

- `01-site-identity.json`
- `02-research-brief.json`
- `03-visual-plan.json`
- `04-seo-outline.json`
- `05-draft.html`
- `06-qc-report.json`
- `06b-draft-revised.html`
- `07-captures/`
- `07-metadata/`
- `08-final-article.html`
- `09-thumbnail.png`
- `10-publish-result.json`

## Integration Pattern

`news-workflow-main` should create a per-job workspace such as:

- `jobs/<job-id>/artifacts/`

Suggested execution flow:

1. Research phase writes `research-brief.json`
2. Visual-plan phase calls `integrate-capture-v2` planning and writes `visual-plan.json`
3. SEO/outline phase writes `seo-outline.json`
4. Writing phase writes `draft.html`
5. QC loop phase repeatedly reads the draft and writes:
   - `qc-report.json`
   - `draft-revised.html`
6. Capture execution phase writes:
   - screenshots
   - capture metadata
7. Patch-up phase merges draft plus captures plus metadata into `final-article.html`
8. Thumbnail phase generates the featured image
9. Publishing phase creates the post and writes `publish-result.json`

## Concrete Wiring

Add integration modules to `news-workflow-main`, for example:

- `lib/phase-runner.js`
- `lib/workspace-manager.js`
- `lib/qc-runner.js`
- `lib/capture-runner.js`
- `lib/finalize-article.js`

Responsibilities:

- `qc-runner.js`
  - invoke `writing-qc`
  - normalize QC output into machine-readable categories
- `capture-runner.js`
  - invoke `integrate-capture-v2` planning/execution
- `finalize-article.js`
  - merge article HTML with real capture outputs and captions

## Best Practical Design

The clean split is:

- `news-workflow-main` controls the workflow
- `writing-qc` decides whether the article is good enough
- `integrate-capture-v2` plans and captures evidence visuals

That matches what each repo is already good at and minimizes duplication.
