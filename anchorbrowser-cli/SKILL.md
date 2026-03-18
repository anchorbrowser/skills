---
name: anchorbrowser-cli
description: Use this skill when the user needs web automation with `anchorbrowser`, including agent-browser parity commands through `anchorbrowser proxy ...`, identity management, authenticated or interactive session creation, task execution, and troubleshooting session/cache/CDP issues.
---

# AnchorBrowser CLI

Use this skill to run browser automation through the AnchorBrowser CLI with the correct command namespace and session/auth model.

## Namespace Map

- `anchorbrowser proxy ...`: Browser actions and agent-browser parity commands.
- `anchorbrowser anchor session ...`: Create/manage Anchor cloud sessions.
- `anchorbrowser anchor identity ...`: Manage identities and credentials.
- `anchorbrowser anchor task ...`: Run task definitions and poll run status.
- `anchorbrowser auth ...`: Manage API keys for CLI access.

## Quick Baseline

```bash
anchorbrowser version
anchorbrowser auth current
anchorbrowser proxy --help
anchorbrowser anchor session --help
anchorbrowser anchor identity --help
anchorbrowser anchor task --help
```

## When To Use Which Feature

- Use `proxy` when the user wants direct browser control (open/click/fill/snapshot/debug).
- Use `anchor session` when the user wants explicit cloud session lifecycle control.
- Use `anchor identity` when the user needs login credentials tied to an app.
- Use `anchor task` when the user wants to run predefined task IDs with input params.
- Use `auth` when API keys are missing, wrong, or need profile switching.

## Session Workflows

### 1) Fast proxy flow (session cache aware)

```bash
anchorbrowser proxy open https://example.com
anchorbrowser proxy snapshot -i
anchorbrowser proxy click @e1
```

Behavior notes:
- `proxy` auto-resolves a session (cached latest session by default).
- Use `--session-id <id>` to pin a specific session.
- Use `--new-session` to force a fresh one.
- Use `--no-cache` to avoid cache lookup.

### 2) Interactive authenticated session creation

```bash
anchorbrowser anchor session create --interactive
```

Interactive flow asks for:
- Whether session should be authenticated.
- Application URL.
- Identity selection/creation for that application.
- Whether to apply recommended anti-bot bundle (stealth + captcha solver + proxy).

Then run proxy commands on the created session:

```bash
anchorbrowser proxy --session-id <session-id> open https://app.example/dashboard
anchorbrowser proxy --session-id <session-id> snapshot -i
```

### 3) Explicit authenticated session (non-interactive)

```bash
anchorbrowser anchor identity list https://app.example
anchorbrowser anchor session create --identity-id <identity-id> --initial-url https://app.example
anchorbrowser proxy --session-id <session-id> open https://app.example
```

## Identity Feature

Use identities to store login credentials per application and attach them to sessions/tasks.

```bash
anchorbrowser anchor identity list https://app.example
anchorbrowser anchor identity create --source https://app.example/login --username <user> --password <pass>
anchorbrowser anchor identity update <identity-id> --name "QA Login"
anchorbrowser anchor identity credentials <identity-id>
anchorbrowser anchor identity delete <identity-id>
```

## Task Feature

Use tasks for predefined server-side task execution with inputs and optional identity/session context.

```bash
anchorbrowser anchor task run <task-id> --input key=value --input other=value
anchorbrowser anchor task run <task-id> --input-file ./inputs.json --identity-id <identity-id> --session-id <session-id>
anchorbrowser anchor task status <run-id>
```

## Proxy Command Families

Core commands to use and test:
- Navigation and state: `open`, `back`, `forward`, `reload`, `wait`, `eval`
- Element actions: `click`, `dblclick`, `hover`, `focus`, `type`, `fill`, `press`, `find`
- Inspection: `snapshot`, `get`, `is`
- Artifacts: `screenshot`, `pdf`
- Tabs and input devices: `tab`, `mouse`, `scroll`, `keyboard`
- Browser/page settings: `set`
- Network and storage: `network`, `cookies`, `storage`, `clipboard`
- Debug and diff: `console`, `errors`, `trace`, `profiler`, `diff`
- Session/auth/install: `session`, `auth`, `install`

## Troubleshooting

- If command fails at top-level, ensure browser commands run via `anchorbrowser proxy ...`.
- If refs fail (`Unknown ref`), run `anchorbrowser proxy snapshot -i` again and use fresh refs.
- If wrong session is used, pass `--session-id` explicitly or add `--new-session`.
- If auth fails, check `anchorbrowser auth current` and re-run `anchorbrowser auth login`.
- If interactive create is used, do not combine `--interactive` with payload flags like `--identity-id` or `--initial-url`.

## Execution Expectations

- Execute commands directly; do not only provide theoretical guidance.
- Return exact commands used and concise pass/fail results.
- For site tests, report the session ID used and cleanup with:

```bash
anchorbrowser anchor session end --session-id <session-id>
```
