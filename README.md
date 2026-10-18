# valpas-keep-awake

A Claude Code plugin that keeps your Mac awake while Claude is working, through the [Valpas](https://kirsulab.com/macos/valpas) menu bar app.

Every prompt and tool call asks Valpas to hold the Mac awake for at least the next 20 minutes. When Claude stops, the last hold runs out by itself and the Mac is free to sleep again. The hold never shortens a session you started by hand, and nothing in the plugin can keep the Mac awake past one grace window, whatever happens to the hooks.

What you get over a bare `caffeinate`:

- the time left is visible in the menu bar, and in `valpas status`
- a battery guard ends the session when the charge runs low
- several Claude Code sessions, a build and a render share one switch
- with [Valpas Pro](https://kirsulab.com/macos/valpas#pro), the lid can be closed and the Mac keeps working, with sleep coming back on timer, battery, quit and heat

## Requirements

- macOS 13 or later
- Valpas 1.3 or later from Homebrew or the disk image. The Mac App Store build has no command line, so the plugin cannot talk to it.

```
brew install --cask KirsuLab/tap/valpas
```

## Install

In Claude Code:

```
/plugin marketplace add KirsuLab/valpas-keep-awake
/plugin install valpas-keep-awake@valpas-keep-awake
```

Restart Claude Code so the hooks load. From then on every prompt holds the Mac awake while Claude works, plus 20 minutes.

`/valpas` inside Claude Code prints what Valpas is doing right now.

## Settings

Three environment variables, read where Claude Code runs:

| Variable | Default | Meaning |
|---|---|---|
| `VALPAS_GRACE` | `20m` | How long each hold lasts. `10m`, `1h`, `90s` all work. |
| `VALPAS_THROTTLE_SECS` | `60` | How often tool calls may renew the hold. |
| `VALPAS_BIN` | found on PATH | Where the `valpas` command is, if not on PATH. |

## How it works

Four hooks, one script:

| Event | What the script does |
|---|---|
| `UserPromptSubmit` | `valpas hold 20m` |
| `PostToolUse` | the same, at most once a minute |
| `SubagentStop` | the same, at most once a minute |
| `Stop` | `valpas hold 20m`, then nothing: the hold runs out |

`hold` is a Valpas command made for this: keep the Mac awake for at least this long from now, and never for less than it already would be. Off, it starts a 20 minute session. Running out sooner, it is lengthened to 20 minutes. Running longer, or set to Forever, it is left alone.

With a Valpas older than 1.3 the plugin falls back to a plain 20 minute session, started only when nothing is running.

## Lid closed

`caffeinate` and every power assertion stop working the moment a MacBook lid closes. That is a separate, lower-level sleep that only an external display or the `pmset disablesleep` setting gets through. Valpas Pro sets that for the length of a session and puts it back on its own. [The whole story](https://kirsulab.com/macos/valpas/keep-mac-awake-for-ai-agents), with the alternatives.

## Without Claude Code

The same thing from any script or agent, with the command Valpas ships:

```
valpas run -- claude          # awake while the command runs
valpas hold 20m               # awake for at least 20 more minutes
valpas status                 # what Valpas is doing, exit 0 while awake
```

## Licence

MIT. Valpas itself is a separate app by KirsuLab LLC.
