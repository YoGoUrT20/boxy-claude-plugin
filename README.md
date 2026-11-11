# Boxy plugin for Claude Code

Connects Claude Code on your computer to your Boxy speaker. The speaker tells
you when a session finished, asks you something or needs your permission, and
passes your spoken answer back so Claude carries on.

Claude Code keeps running on your computer under your own Claude login. The
plugin only pipes hook events to your Boxy server and prints what it answers.

## Install

Open the Claude page in the Boxy app, tap Add, and paste the line it shows in
a terminal on the computer (Git Bash on Windows). The line pairs the computer
and installs this plugin.

Needs `bash` and `curl`, which macOS, Linux and Git for Windows have.

## What it sends

For each of these Claude Code hook events the plugin posts the event's JSON
to your Boxy server: `SessionStart`, `SessionEnd`, `UserPromptSubmit`, `Stop`,
`PreToolUse` for `AskUserQuestion`, `PermissionRequest`, and the `Notification`
kinds that mean Claude is waiting. That includes Claude's last message, its
questions, and the input of a tool it asks permission for.

Nothing is sent until the computer is paired (`~/.boxy/claude/token`).

## Remove

```bash
claude plugin uninstall boxy
```

Then remove the computer on the Claude page in the Boxy app, and delete
`~/.boxy/claude`.

How it works on the server: `docs/extensions/claude-code.md` in the Boxy repo.
