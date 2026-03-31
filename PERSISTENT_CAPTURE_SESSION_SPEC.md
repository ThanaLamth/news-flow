# Persistent Capture Session Spec

Specification for saving and reusing browser session state for capture-heavy or challenge-heavy platforms.

## Purpose

Some visual sources are unreliable in stateless automation because they depend on:

- login state
- cookie state
- local browser storage
- challenge clearance
- region or site-preference state

To make capture smoother over time, the workflow should support persistent per-platform browser profiles.

## Goal

Allow the workflow to reuse previously warmed browser state so repeated captures on the same platform are more reliable.

This is especially useful for:

- Arkham
- CoinGecko
- CoinShares
- any site that uses bot challenges, sticky login state, or repeated consent flows

## Core Principle

Do not store one global browser session for all capture sources.

Instead, keep a separate persistent profile per platform.

## Recommended Profile Layout

- `profiles/arkm/`
- `profiles/coingecko/`
- `profiles/coinshares/`
- `profiles/<platform_key>/`

Each profile directory should contain browser state for one platform only.

## Session Lifecycle

### 1. Session Check

Before `DATA VISUALS EXECUTION`, the workflow checks whether the platform already has a valid persistent profile.

Possible states:

- `ready`
- `stale`
- `missing`
- `blocked`

### 2. Warmup Mode

If the profile is missing or stale, start a headed browser session using the platform's persistent profile directory.

Human actions in warmup mode may include:

- pass challenge
- log in
- dismiss cookie prompts
- set site preferences
- verify page accessibility

### 3. Save Profile State

Once the page is accessible, preserve the profile directory for reuse.

### 4. Capture Mode

Future capture jobs should reuse the saved profile automatically.

### 5. Health Check

If a capture using the saved profile starts failing, mark the session as `stale` or `blocked` and require warmup again.

## Suggested Workflow Placement

Recommended execution order:

1. `DATA VISUALS PLAN`
2. `SESSION CHECK`
3. `SESSION WARMUP REQUIRED` if needed
4. `DATA VISUALS EXECUTION`
5. `HUMAN CAPTURE CONFIRM`

## Session Metadata File

Each profile should have a metadata file:

- `profiles/<platform_key>/session.json`

### Recommended Schema

```json
{
  "platform_key": "arkm",
  "profile_dir": "./profiles/arkm",
  "status": "ready",
  "last_verified_at": "2026-03-31T10:00:00Z",
  "last_used_at": "2026-03-31T10:12:00Z",
  "challenge_required": true,
  "login_required": false,
  "notes": "Challenge cleared manually in headed mode"
}
```

## Recommended Status Values

- `ready`
  - profile is usable for automation
- `stale`
  - profile exists but likely needs fresh warmup
- `missing`
  - no profile exists
- `blocked`
  - platform remains inaccessible even after warmup

## Inputs To Record

For each platform profile, store:

- `platform_key`
- `profile_dir`
- `status`
- `last_verified_at`
- `last_used_at`
- `challenge_required`
- `login_required`
- `notes`

Optional:

- `homepage_url`
- `verification_url`
- `preferred_capture_mode`
- `known_blockers`

## What Counts As Session Success

A session should be considered valid only if:

- target pages load real content
- challenge pages are not shown
- intended capture selectors or data blocks are visible

## Failure Rules

Mark the session as `stale` if:

- page title becomes challenge/interstitial text
- selectors disappear
- auth expires
- repeated capture attempts fail

Mark the session as `blocked` if:

- the platform cannot be accessed even after manual warmup

## Integration With `integrate-capture-v2`

This fits naturally into the existing `profileDir` concept already present in the capture engine.

Instead of treating `profileDir` as optional ad hoc input, make it a first-class workflow dependency.

Recommended behavior:

- `capture-runner` resolves `profileDir` from `profiles/<platform_key>/`
- if no valid profile exists, orchestration pauses and requests warmup
- after warmup, capture resumes using the stored profile

## Modules To Add In `news-workflow-main`

- `lib/session-manager.js`
- `lib/session-checker.js`
- `lib/session-warmup.js`

Responsibilities:

- `session-manager.js`
  - locate profiles
  - read/write `session.json`
- `session-checker.js`
  - determine whether a session is `ready`, `stale`, `missing`, or `blocked`
- `session-warmup.js`
  - launch a headed browser flow for manual clearance

## Human Warmup UX

When warmup is required, the system should show:

- platform name
- reason warmup is needed
- target page URL
- profile directory being used
- instructions:
  - open page
  - pass challenge
  - log in if required
  - confirm page is visible

After that, save the session state and mark it `ready`.

## Benefits

- smoother repeated captures
- less manual cookie exporting
- less brittle one-off automation
- better reliability for challenge-heavy sources
- clean path from semi-automation to stronger automation

## Recommended Rollout

### Phase 1

Manual warmup + persistent profile reuse

### Phase 2

Automatic session checking before capture

### Phase 3

Automatic stale-session detection and re-warmup requests

### Phase 4

Per-platform reliability scoring using review logs and session success history

## Definition Of Done

This capability is implemented when:

- a profile can be created per platform
- a human can warm it once and reuse it later
- the workflow checks session status before capture
- capture can automatically use the stored profile
- stale sessions are detected and flagged for re-warmup
