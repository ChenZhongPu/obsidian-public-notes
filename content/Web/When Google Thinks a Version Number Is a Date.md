---
title: When Google Thinks a Version Number Is a Date
draft: false
tags:
  - Network
date: 2026-03-19
---
A small, strange thing was spotted in Google Search: the `npm` package `@anthropic-ai/claude-code`, currently at version `2.1.78`, was displaying a last-updated date of **January 2, 1978** in its search snippet. 

No such date exists anywhere on the package page. Google had simply looked at the version number and decided it was a date. However, checking structurally similar packages across `npm`, `PyPI`, `Maven`, and `RubyGems` reveals no other confirmed cases.

![[google-date.png]]