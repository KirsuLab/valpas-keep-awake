---
description: Show whether Valpas is keeping this Mac awake, and for how long
allowed-tools: Bash(valpas:*), Bash(${CLAUDE_PLUGIN_ROOT}/bin/valpas-hold:*)
---

Run `"${CLAUDE_PLUGIN_ROOT}/bin/valpas-hold" status` and report the result in one line. If it says the valpas command is not installed, say that Valpas comes from Homebrew (`brew install --cask KirsuLab/tap/valpas`) or Ko-fi, that the Mac App Store build has no command line, and point to https://kirsulab.com/macos/valpas.
