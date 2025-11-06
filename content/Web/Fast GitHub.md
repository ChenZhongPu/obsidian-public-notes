---
title: "Fast GitHub"
tags:
  - Network
date: 2024-04-04
---

When using a new server in China, most of time you would struggle with the network issues due to [Great Firewall](https://en.wikipedia.org/wiki/Great_Firewall).

Recently, I came across a good proxy mirror `https://githubfast.com`, which can alleviate this problem. In what follows, I will use `uv` as an example to illustrate how to use it.

## A showcase of `uv`

In a normal network environment, installing `uv` is easy:

```bash
curl -LsSf https://astral.sh/uv/install.sh | sh
```

But note that this shell and its related binary release files are, in fact, hosted in GitHub. The **Fast GitHub** solution:

```bash
export UV_INSTALLER_GITHUB_BASE_URL=https://githubfast.com
curl -LsSf https://githubfast.com/astral-sh/uv/releases/download/0.6.12/uv-installer.sh | sh
```

## A short explanation

The usage of *Fast GitHub* is easy: just replace *github.com* with *githubfast.com*. As for `uv`, it respects `UV_INSTALLER_GITHUB_BASE_URL`, which defaults to `https://github.com`.

As for a general solution, one can conduct the replacing manually:

```bash
sed -i 's/github\.com/githubfast\.com/g' YOUR_FILE
```


