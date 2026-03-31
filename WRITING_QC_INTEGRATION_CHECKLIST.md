# Writing QC Integration Checklist

Checklist of what is still missing to fully integrate `writing-qc` into the new workflow.

## Status Summary

What we already have:

- `writing-qc` as an installed skill
- clear editorial and SEO QC logic
- pass / revise / fail review framing
- issue categories for title, headings, anchor text, internal links, search quality, and visual-need detection

What is still missing:

- orchestration
- machine-readable outputs
- automated fix loop
- workflow artifact wiring

## Checklist

### 1. Machine-Readable QC Output

- [ ] Define a normalized QC result schema
- [ ] Require `verdict` field
- [ ] Require structured `issues[]`
- [ ] Require `severity` per issue
- [ ] Require `category` per issue
- [ ] Require `suggested_fix` per issue
- [ ] Add `pass_blocking` or equivalent flag
- [ ] Add `visual_needed` output field
- [ ] Add `internal_link_shortfall` output field

### 2. QC Artifact Files

- [ ] Write QC result to `06-qc-report.json`
- [ ] Write revised draft to `06b-draft-revised.html`
- [ ] Preserve original draft as `05-draft.html`
- [ ] Preserve QC history across passes

### 3. QC Loop Controller

- [ ] Add explicit `WRITING-QC LOOP` phase to orchestration
- [ ] Run QC automatically after first draft
- [ ] Stop on `Pass`
- [ ] Retry on `Revise`
- [ ] Block progression on hard `Fail`
- [ ] Add max-attempt limit
- [ ] Log attempt count

### 4. Draft-to-Fix Bridge

- [ ] Convert QC findings into rewrite instructions
- [ ] Group issues by severity for rewrite
- [ ] Separate blocking issues from polish issues
- [ ] Ensure rewrite prompts stay grounded in actual findings
- [ ] Avoid generic “improve SEO” rewrite prompts

### 5. Internal-Link Integration

- [ ] Implement internal-link candidate selection before QC
- [ ] Implement insertion of at least 5 valid internal links where candidates exist
- [ ] Log shortfall if fewer than 5 good candidates exist
- [ ] Make QC verify internal-link count and quality
- [ ] Make QC verify no links in intro paragraph

### 6. Visual-Need Handoff

- [ ] Capture `writing-qc` visual recommendations in machine-readable form
- [ ] Pass visual-need findings into `DATA VISUALS PLAN`
- [ ] Distinguish between:
  - [ ] required visuals
  - [ ] optional visuals
  - [ ] visuals that should block publishing if missing

### 7. Override Policy

- [ ] Define who can override `Revise`
- [ ] Define who can override `Fail`
- [ ] Log override reason
- [ ] Store override in workflow artifacts
- [ ] Prevent silent publish after failed QC

### 8. Phase Runner Integration

- [ ] Wire QC into `news-workflow-main` phase runner
- [ ] Ensure later phases read QC artifacts instead of raw memory state
- [ ] Block `DATA VISUALS EXECUTION` until draft is QC-approved or explicitly overridden
- [ ] Block `PUBLISHING` until final QC state is acceptable

### 9. Logging And Traceability

- [ ] Store every QC pass result
- [ ] Record which issues were fixed between passes
- [ ] Record unresolved issues at publish time
- [ ] Record whether publish happened after override

### 10. Acceptance Criteria

- [ ] A draft automatically enters QC after writing
- [ ] QC produces structured machine-readable output
- [ ] The workflow can revise and rerun automatically
- [ ] Internal-link requirements are checked
- [ ] Visual-need findings feed downstream capture planning
- [ ] Articles cannot silently bypass QC before publish

## Recommended Build Order

1. normalized QC schema
2. QC artifact files
3. QC loop controller
4. draft-to-fix bridge
5. internal-link integration
6. visual-need handoff
7. override policy
8. final phase-runner enforcement

## Definition Of Done

`writing-qc` is fully integrated when:

- the workflow can run draft -> QC -> fix -> QC automatically
- QC results are stored in structured artifacts
- internal links and visual needs are enforced through QC
- publishing cannot proceed without acceptable QC state or explicit override
