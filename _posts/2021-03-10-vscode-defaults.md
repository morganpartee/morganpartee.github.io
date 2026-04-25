---
layout: post
title: "VSCode Sensible Defaults"
date: 2021-03-10
description: "I love VSCode. It works well for every language I write, and on every platform I use. Here's my basic config recommendations."
author: "John"
original_slug: vscode-defaults
---

> **2026 note:** This is a snapshot of my 2021 setup. Bracket Pair Colorizer is now built into VSCode (so you can drop the extension), and TabNine has largely been displaced by Copilot / Cursor / Claude Code for AI completion.

**This is opinionated! If you dislike an extension, drop it. Let me know what I'm missing.**

VSCode Extensions I recommend (**Bold is a strong recommendation**):

AREPL for Python

Better Comments

**Bracket Pair Colorizer**

**Code Spell Checker**

**GitLens**

**Indented Block Highlighter**

~~Kite~~ **TabNine**

**Live Share Extension Pack (use this to collaborate on code)**

**One Dark Pro (It’s pretty!)**

**Python Docstring Generator**

**Python  Indent**

**Python Preview**

**Python Extension Pack**

**Rainbow CSV**

**SonarLint**

Test Explorer UI

Toggle Zen Mode

UNotes

In your config file (Control+shift+p, open settings (JSON)), add these options to enable autosave, auto linting, and auto formatting for Python.

```json
"python.formatting.provider": "black",
"python.linting.enabled": true,
"files.autoSave": "afterDelay",
"files.autoSaveDelay": 10000,
"python.linting.lintOnSave": true,
```

Now if you start writing Rust, JS, etc. You'll want to hunt for language specific extensions. But, this should get you in a good spot to start with Python. Good luck!
