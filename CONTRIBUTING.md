# Contributing to iris

Thanks for taking the time — iris is young (published on crates.io as [`iris-tui`](https://crates.io/crates/iris-tui)) and contributions are very welcome.

## The short version

- **Small fixes** (typos, docs, one-line bug fixes): just open a PR. No issue needed.
- **New features or behavior changes**: please [open an issue](https://github.com/itzenata/iris-tui/issues/new/choose) first so we can agree on the approach before you write the code. It saves you from building something that doesn't fit — and gets you a faster merge.
- **Always add a PR description.** A sentence on *what* and *why* is enough.

## The hard rules (non-negotiable)

Any change must respect the product's contract. These are spelled out in [README](README.md#hard-rules) and [CLAUDE.md](CLAUDE.md):

- **Read-only over transcripts.** iris tails the `.jsonl` files Claude Code writes; it never modifies or deletes them. "Removing" a session from the view means hiding it, not touching the file on disk.
- **Local-first.** The only outbound network calls are the on-demand AI summary (`s`) and risk read (`x`). No telemetry, no background network, no remote config.
- **Never hang a session.** The hook defers instantly to Claude Code's normal flow when iris isn't running or gating is disarmed.
- **Opt-in interception.** Approval gating stays off until armed (`A`) and disarms on exit.
- **Key safety.** The API key is entered in-app and written `0600`. Never log or commit it.

If a change seems to require breaking one of these, open an issue to discuss first — they're constraints, not defaults.

## Before you open a PR

```bash
cargo build        # must pass
cargo run          # sanity-check the TUI
```

- Match the existing module split (`app` state, `ui` rendering, `session` parsing, `bridge` hook, `anthropic` network, `cost` pricing). Key handling lives in `main.rs`'s event loop.
- Update docs when behavior changes — especially the **key map in [README.md](README.md#keys)** if you add a binding.
- Keep commits focused; no `Co-Authored-By` trailers needed.

## Releasing (maintainers)

Releases are automated by [`release.yml`](.github/workflows/release.yml):

1. Bump `version` in `Cargo.toml` and add a `## v0.x.y — <date>` section to
   [CHANGELOG.md](CHANGELOG.md), commit, and push.
2. Tag and push: `git tag v0.x.y && git push origin v0.x.y`.
3. The workflow verifies (clippy + tests + tag/version/CHANGELOG checks),
   publishes to crates.io (via the `CARGO_REGISTRY_TOKEN` repo secret), builds
   Linux and macOS binaries, and attaches them to a GitHub Release whose body
   is the CHANGELOG section plus GitHub's auto-generated notes.

## Code of conduct

Be kind and constructive. This is a small project — assume good intent.
