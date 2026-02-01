---
title: How to copy in Zathura
draft: false
tags:
  - Linux
date: 2026-02-01
---
 
[Zathura](https://pwmt.org/projects/zathura/) is a lightweight yes highly customizable PDF reviewer. It works well with Latex.

Recently, I found that its copying text does not well. The solution can be found [here]([# Copy text not working](https://github.com/homebrew-zathura/homebrew-zathura/issues/5)).

## Primary and Clipboard

In X11, text copying is handled through selections rather than a single clipboard. The **PRIMARY** selection copies text implicitly when it is selected and is typically pasted using the middle mouse button, making it transient and selection-driven. In contrast, the **CLIPBOARD** selection requires an explicit copy action (such as `Ctrl+C`) and is pasted with `Ctrl+V`, providing a more persistent and familiar copy-paste behavior aligned with modern desktop environments.

Zathura uses `Primary` as its selection by default.