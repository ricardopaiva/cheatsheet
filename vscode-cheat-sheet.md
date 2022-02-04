---
title: Business Central Versions Naming
nav_order: 6
---

# {{ page.title }}

{: .no_toc }

---

## How to turn off auto select in File Explorer when changing window tabs?

In VSCode, if I have an editor window tab open and I click anywhere in that editor window, the selected file in the File Explorer changes to the file being edited. I would like to prevent that behavior.

**Preferences -> Settings -> Explorer: Auto Reveal** (explorer.autoReveal)

Change to **False**.

Keyboard shortcut to manually show the current file in Explorer:

**Preferences -> Keyboard Shortcuts**

Search for **workbench.files.action.showActiveFileInExplorer**

Setup shortcut. Eg. **Ctrl+Alt+E**.

{ "key": "ctrl+alt+e", "command": "workbench.files.action.showActiveFileInExplorer" }