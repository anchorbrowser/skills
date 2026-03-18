---
name: anchorbrowser-cli
description: Use this skill when the user wants browser automation through the AnchorBrowser CLI (`anchorbrowser`). Covers installation checks, authentication, proxy commands (`anchorbrowser proxy ...`), authenticated session flows (`anchor session` + `--session-id`), and command troubleshooting for refs, sessions, and CDP connectivity.
---

# AnchorBrowser CLI

## Overview

Use this skill to execute browser tasks with `anchorbrowser` quickly and reliably.
Default path for browser actions is `anchorbrowser proxy ...`; use `anchorbrowser anchor ...` for session/identity/task APIs.

## Quick Start

1. Verify CLI and auth:
```bash
anchorbrowser version
anchorbrowser auth current
```

2. Run proxy commands:
```bash
anchorbrowser proxy open https://example.com
anchorbrowser proxy snapshot -i
anchorbrowser proxy click @e1
anchorbrowser proxy fill @e2 "hello"
```

3. Use anchored API commands when needed:
```bash
anchorbrowser anchor session --help
anchorbrowser anchor identity --help
anchorbrowser anchor task --help
```

## Authenticated Browsing Flow

1. Resolve identity for app:
```bash
anchorbrowser anchor identity list https://your-app.example
```

2. Create session with identity:
```bash
anchorbrowser anchor session create --identity-id <identity-id> --initial-url https://your-app.example
```

3. Run browser commands on that session:
```bash
anchorbrowser proxy --session-id <session-id> open https://your-app.example/dashboard
anchorbrowser proxy --session-id <session-id> snapshot -i
```

## Command Patterns

- Navigation/state: `open`, `back`, `forward`, `reload`, `wait`, `eval`
- Element actions: `click`, `dblclick`, `hover`, `focus`, `type`, `fill`, `press`, `find`
- Inspection: `snapshot`, `get`, `is`
- Artifacts: `screenshot`, `pdf`, `trace`, `profiler`, `diff`
- Browser controls: `tab`, `mouse`, `scroll`, `set`
- Data/session: `cookies`, `storage`, `clipboard`, `network`, `session`, `auth`

## Troubleshooting

- If command refs fail (`Unknown ref`), run a fresh `anchorbrowser proxy snapshot -i` and use new refs.
- If session issues occur, pass `--session-id` explicitly.
- If auth appears missing, confirm key source with `anchorbrowser auth current`.
- If command surface is unclear, use `anchorbrowser proxy --help` or `<command> --help`.

## Output Expectations

- Prefer direct CLI execution over abstract advice.
- Return exact commands used and concise pass/fail outcomes.
- For website tests, include session id used and cleanup step (`anchor session end`).
