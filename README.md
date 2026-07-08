# iris

> A live terminal supervisor for every active Claude Code session. **`cargo install iris-tui`** — the binary is `iris`.

[![crates.io](https://img.shields.io/crates/v/iris-tui?logo=rust&color=e6b800)](https://crates.io/crates/iris-tui)
[![CI](https://img.shields.io/github/actions/workflow/status/itzenata/iris-tui/ci.yml?branch=main&label=CI)](https://github.com/itzenata/iris-tui/actions/workflows/ci.yml)
[![License: MIT](https://img.shields.io/github/license/itzenata/iris-tui?color=blue)](LICENSE)
[![Built with Rust](https://img.shields.io/badge/built%20with-Rust-dea584?logo=rust&logoColor=white)](https://www.rust-lang.org)
[![Made for Claude Code](https://img.shields.io/badge/made%20for-Claude%20Code-c678dd)](https://claude.com/claude-code)
[![Stars](https://img.shields.io/github/stars/itzenata/iris-tui?style=social)](https://github.com/itzenata/iris-tui/stargazers)
[![Last commit](https://img.shields.io/github/last-commit/itzenata/iris-tui?color=green)](https://github.com/itzenata/iris-tui/commits/main)

🌐 **Landing page:** [itzenata.github.io/iris-tui](https://itzenata.github.io/iris-tui/)

## What it does

A fast terminal dashboard that watches **all your running Claude Code sessions at once** — what each one is doing right now, its model, tokens and estimated cost, an AI "doing / done / next" summary, and one-key approval of pending tool calls routed from any session into a single pane.

It reads the transcripts Claude Code already writes under `~/.claude/projects/` — **no daemon, no config, nothing to set up** beyond an optional approval hook.

**Hard rules:** local-first (the only network call is the AI summary you opt into), read-only over your transcripts, and a heartbeat so sessions never hang waiting on iris.

```
 iris  approved Bash   4 active    pending 1    · 3m · 14:21:07

┌ sessions ───────────────┐ ┌ detail ────────────────────────────┐
│ ⚠ Build CLI to superv…  │ │ ⚠ PENDING APPROVAL — Bash in iris  │
│   APPROVE Bash — a/d    │ │ git push --force                   │
│   iris · opus-4-8 · 7.2M│ │ a allow   d deny                   │
│ ● Slack triage          │ │ model opus-4-8  turns 31  ~cost $24│
│   running · Bash        │ │ tool calls                         │
│   slack · haiku-4-5     │ │ Bash    ████████░░ 18              │
│ ✓ Configure CloudSQL…   │ │ Edit    █████░░░░░ 9               │
│   done · awaiting you   │ │ ── activity ──                     │
│ ○ Complete five actions │ │ ▸ you  add the gitignore           │
│   idle · lanoria-club   │ │ ⚒ Bash cargo build --release       │
└─────────────────────────┘ └────────────────────────────────────┘
 j/k move  a/d allow/deny  ⏎ details  s summary  i approvals  q quit
```

> See a real screenshot of it running on the [landing page](https://itzenata.github.io/iris-tui/).

## What's working today

A single live pane, refreshed every second:

| Panel | What it shows |
|---|---|
| **Sessions list** | Every session active in the last N minutes, grouped and sorted, color-coded by state |
| **Aggregate cost** | The header totals the estimated USD spend of everything on screen (also in `iris ls`) |
| **Status glyphs** | `⚠` pending approval · `●` running · `✓` done / awaiting you · `○` idle |
| **Per-session meta** | Model (`opus-4-8`, `sonnet`, `haiku`, `fable`), token total, estimated USD cost |
| **Activity feed** | The latest prompt, thinking, tool call, and result of the entered session, tailed live |
| **Tool timeline** | A histogram of which tools a session leans on — spot the one stuck in a build loop |
| **AI summary** | `s` for a Haiku-generated "doing / done / next" briefing of any session |
| **Approval modal** | `⏎` opens the full tool input with an `x` AI risk read; `a`/`d` allow or deny |

**Views & navigation:** vim motions (`j`/`k`, `g`/`G`, `Ctrl-d`/`Ctrl-u`) on both the session list and the activity feed, foldable groups (`space`/`z`), `D` to remove a stale session from the view (the transcript on disk is untouched), and an `ls` subcommand that prints a one-shot table with no TUI.

**Remote approvals:** `iris install-hook` registers a `PreToolUse` hook in `settings.json`. With gating armed (`A`), any session's permission prompt routes into iris — approve or deny it, for one session or a whole group, from one place.

**Cost model:** per-model pricing (input / output / cache-write / cache-read) kept as editable constants in [`src/cost.rs`](./src/cost.rs). Figures are estimates — adjust them to your plan.

## Hard rules

- **Read-only over your data.** iris tails the transcript files Claude Code writes; it never edits them.
- **Local-first.** The only outbound request is the AI summary / risk read, and only when you press `s` / `x`. No telemetry, no remote config.
- **Never hangs a session.** iris touches a heartbeat file while running. If it's stale (iris not up) or gating is disarmed, the hook instantly defers to Claude Code's normal permission flow — your sessions are never blocked on a dashboard that isn't there.
- **Opt-in interception.** Approval gating is off until you arm it with `A`, and it disarms automatically when iris exits.
- **Your key, your machine.** The Anthropic API key for summaries is entered in-app (`K`) and saved `0600` in your home directory.

## Install

```bash
cargo install iris-tui   # installs the `iris` binary in ~/.cargo/bin
```

(The crate is `iris-tui` — `iris` is a reserved name on crates.io — but the command you run is `iris`.) Prebuilt Linux and macOS binaries are on the [releases page](https://github.com/itzenata/iris-tui/releases), or build from source with `cargo install --path .` after cloning.

Then:

```bash
iris                     # live dashboard
iris ls                  # one-shot table, no TUI
iris install-hook        # route approvals through iris (--project for ./.claude)
iris uninstall-hook      # remove the hook
```

Single static binary, built with Rust + [ratatui](https://ratatui.rs). Reads `~/.claude/projects/` — override with `-d <path>`.

## Keys

| Key | Action |
|---|---|
| `j` `k` | move between sessions |
| `g` `G` | jump to first / last · `Ctrl-d` `Ctrl-u` half-page |
| `space` `z` | fold a group / fold all |
| `D` | remove the selected session from the view (transcript on disk untouched) |
| `⏎` | open the approval detail (full input + AI risk read), or enter a session's feed |
| `a` `d` | allow / deny the pending tool call (whole group when a header is selected) |
| `s` | AI summary of the selected session (`g` to regenerate) |
| `x` | AI risk read of the pending tool call |
| `i` | open the approval-interception proposal |
| `A` | arm / disarm approval gating |
| `K` | set your Anthropic API key (saved `0600`) |
| `r` | force refresh |
| `q` | quit |

## Progress

- [x] MIT-licensed, single static Rust binary
- [x] [Landing page](https://itzenata.github.io/iris-tui/) on GitHub Pages
- [x] [Issue templates](.github/ISSUE_TEMPLATE) for bugs, features, integration ideas
- [x] Live session discovery + tailing from `~/.claude/projects/`
- [x] Dashboard: status glyphs, model, tokens, estimated cost
- [x] Activity feed with vim navigation and foldable groups
- [x] Tool-usage histogram per session
- [x] AI "doing / done / next" summaries (Haiku)
- [x] `PreToolUse` hook bridge — remote approve / deny from one pane
- [x] AI risk read on a pending tool call
- [x] Heartbeat fallback so sessions never block on iris
- [x] `ls` one-shot table mode
- [x] Remove sessions from the view (`D`) without touching transcripts
- [x] Unit tests for the heartbeat and transcript parsing
- [x] CI on every push and PR (clippy, build, test)
- [x] [CONTRIBUTING guide](./CONTRIBUTING.md) + PR template
- [x] crates.io publish metadata in `Cargo.toml`
- [x] 60s demo video + validation post
- [x] Published to crates.io as [`iris-tui`](https://crates.io/crates/iris-tui) + prebuilt binaries on [releases](https://github.com/itzenata/iris-tui/releases)

## Get involved

- ⭐ Star to follow progress
- 💡 [Suggest an integration or signal](https://github.com/itzenata/iris-tui/issues/new?template=integration_suggestion.md)
- 💬 [Open an issue](https://github.com/itzenata/iris-tui/issues/new/choose) for any session-supervision problem you'd want solved
- 🔧 Want to contribute code? Read [CONTRIBUTING.md](./CONTRIBUTING.md) — small fixes go straight to PR, features start as an issue

![iris supervising two live Claude Code sessions — session list on the left; model, cost, token and tool-call breakdown plus the live activity feed on the right](https://raw.githubusercontent.com/itzenata/iris-tui/main/docs/assets/iris-white.png)

## Development

```bash
cargo run                 # live TUI
cargo run -- ls           # one-shot table
cargo test                # heartbeat + transcript parsing tests
cargo clippy --all-targets -- -D warnings   # CI gates on this
cargo build --release     # optimized binary (LTO, stripped)
```

CI runs clippy (warnings as errors), build, and tests on every push and PR — see [`.github/workflows/ci.yml`](./.github/workflows/ci.yml). Contribution guidelines live in [CONTRIBUTING.md](./CONTRIBUTING.md).

Code layout: `main.rs` is the CLI entry and event loop; modules under [`src/`](./src) split as `app` (state), `ui` (rendering), `session` (transcript parsing), `bridge` (the hook + heartbeat), `anthropic` (summaries / risk reads), and `cost` (estimation).

License: [MIT](./LICENSE)
