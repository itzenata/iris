# Changelog

Notable changes per release. The section matching the pushed tag becomes the
GitHub Release body (with GitHub's auto-generated PR/contributor notes
appended). Format inspired by [Keep a Changelog](https://keepachangelog.com).

## v0.1.0 — 2026-07-07

First public release. Published on crates.io as
[`iris-tui`](https://crates.io/crates/iris-tui) — the installed binary is `iris`.

### Added

- Live dashboard of every active Claude Code session: status glyphs, model,
  token totals, and estimated USD cost, refreshed every second.
- Activity feed tailing the selected session (prompts, thinking, tool calls,
  results) with vim navigation and foldable session groups.
- Per-session tool-usage histogram.
- Opt-in AI features: `s` "doing / done / next" summaries and `x` risk read of
  a pending tool call.
- Remote approvals: `iris install-hook` registers a `PreToolUse` hook; with
  gating armed (`A`), approve or deny any session's tool calls from one pane.
- Heartbeat fallback so sessions never block when iris isn't running.
- `iris ls` one-shot table mode, `D` to hide a session from the view
  (transcripts on disk are never touched).
- Landing page with light/dark theme; CI (clippy, build, test) and
  tag-triggered release automation.

### Thanks

- [@Laktab-Noureddine-code](https://github.com/Laktab-Noureddine-code) —
  heartbeat & transcript test suite (#4), the CI workflow (#6), and the
  crates.io publish metadata (#8).
- [@achraf-ayar](https://github.com/achraf-ayar) — remove-session-from-view
  feature (#1, #2).
