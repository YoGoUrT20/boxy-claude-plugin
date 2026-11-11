# Boxy plugin for Claude Code

Connects Claude Code on your computer to your Boxy speaker. The speaker tells
you when a session finished, asks you something or needs your permission, and
passes your spoken answer back so Claude carries on.

You can also ask the speaker to start a Claude Code session in one of your
projects, to pass a follow-up to one that sits idle in the background, and to
stop one.

Claude Code keeps running on your computer under your own Claude login. The
plugin pipes hook events to your Boxy server and prints what it answers, and
keeps a small loop running that asks the server for sessions to start.

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

## What it runs

`bin/boxy-bridge` is a bash loop that starts when you log in (the setup line
adds a login item: a launchd agent on macOS, a `systemd --user` unit on
Linux, a `Run` registry value on Windows) and with your Claude Code sessions,
and keeps running in the background. It asks your Boxy server for work about once a
minute and listens on nothing. It can run exactly these commands, as you:

- `claude --bg "<task>"` in a folder Claude Code has already been used in;
- `claude stop <session>`, and `claude --bg --resume <session> "<words>"` to
  wake a background session;
- `claude agents --json`, to tell the server which sessions are open.

It also reports the folders in Claude Code's own `projects` directory, so a
project can be found by its spoken name. It stops when the computer is
removed in the app, when `~/.boxy/claude` is deleted or when the plugin is
uninstalled. After a restart it comes back when you log in.

Nothing is sent and nothing runs until the computer is paired
(`~/.boxy/claude/token`).

## Remove

```bash
claude plugin uninstall boxy
```

Then remove the computer on the Claude page in the Boxy app, delete
`~/.boxy/claude`, and remove the login item:

- macOS: `launchctl bootout gui/$(id -u)/com.boxy.claude-bridge` and delete
  `~/Library/LaunchAgents/com.boxy.claude-bridge.plist`;
- Linux: `systemctl --user disable --now boxy-claude-bridge.service`;
- Windows: `reg delete HKCU\Software\Microsoft\Windows\CurrentVersion\Run /v BoxyClaudeBridge /f`.

Left behind, it does nothing: without the plugin or the pairing it exits
right away.

How it works on the server: `docs/extensions/claude-code.md` in the Boxy repo.
