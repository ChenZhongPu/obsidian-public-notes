---
title: An Extended Self-Host Overleaf
draft: true
tags:
  - Linux
date: 2026-02-02
---
Although Overleaf provides a community version, it lacks several useful features, such as *review*. Recently, I found another open-sourced extended version [overleafcep/sharelatex](https://github.com/yu-i-i/overleaf-cep/).

## Set-up Overleaf Toolkit

Because `OveleafCEP` is based on [Oveafleaf Toolkit](https://github.com/overleaf/toolkit/), you have to download it via `git`, and then run `bin/init`.

```bash
git clone https://github.com/overleaf/toolkit.git ./overleaf-toolkit
cd overleaf-toolkit
./bin/init
```

Then, go to `config` folder:

- `overleaf.rc`: update `OVERLEAF_LISTEN_IP` and `OVERLEAF_PORT` if necessary. For example, in order to support external access, change the IP to `0.0.0.0`.
- `variable.env`: update `OVERLEAF_APP_NAME` if necessary; Setup the `OVERLEAF_ADMIN_EMAIL`.

## Install the Extended Version

Inside the `config` folder, create `docker-compose.override.yml`:

```yml
---
services:
    sharelatex:
        image: overleafcep/sharelatex:6.0.1-ext-v3.3
```

Then, `docker pull overleafcep/sharelatex:6.0.1-ext-v3.3`.

After that, `./bin/up` to start the application. For the first time, it will download dependency images required by `docker-compose`. As of the time of this writing, it uses:

- redis:7.4
- mongo:8.0

