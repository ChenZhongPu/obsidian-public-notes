---
title: Set Up Postgres (PG 001)
draft: true
tags:
  - postgres
date: 2025-04-02
---
 
In this series, I am going to analyze the source code of [Postgres](https://github.com/postgres/postgres), the most advanced open-source database.

## Install

First of all, download the least code from GitHub, and the commit ID is `121d774caea4c93c8b36fb20a17ef774e60894d6`:

```bash
git clone --depth 1 git@github.com:postgres/postgres.git
```

Configure the build, as suggested in [What debugging features are available?](https://wiki.postgresql.org/wiki/Developer_FAQ#What_debugging_features_are_available.3F):

```bash
./configure --prefix=<YOUR_INSTALL_PATH_PREFIX> --enable-depend --enable-cassert --enable-debug CFLAGS="-ggdb -Og -g3 -fno-omit-frame-pointer"
```

In my computer, the `--prefix` is set to `/run/media/zhongpu/DATA/projects/pg-install`.  For brevity, I will use `${PREFIX}` to denote this path.  After that, 

```bash
make
make install
```

> In fact, PG provides both *Makefile* and *Meson* building, and I use the traditional *Makefile*.

If everything is okay, then PG will be installed at `${PREFIX}` with four folders:

```
├── bin
├── include
├── lib
└── share
```


## Init DB

Navigate to `${PREFIX}`,

```bash
./bin/initdb -D <YOUR_PG_DATA_PATH> -U postgres
```

In my computer, `-D` is set to `/run/media/zhongpu/DATA/projects/pg-data`, and I will `${DATA}` to denote it. The terminal should display something like

> Success. You can now start the database server using:
> bin/pg_ctl -D /run/media/zhongpu/DATA/projects/pg-data -l logfile start

Since I have already started another PG,  I need to change the port in `${DATA}/postgresql.conf` (Line 64) before starting this PG server. Here I choose `5435`:

```
#port = 5432   # (change requries restart)
port = 5435
```


## Start DB 

Inside  `${PREFIX}`,

```bash
./bin/pg_ctl -D <YOUR_PG_DATA_PATH> start
```

It would display

> 2025-04-02 11:40:12.678 CST [2904107] LOG:  listening on IPv6 address "::1", port 5435
> 2025-04-02 11:40:12.678 CST [2904107] LOG:  listening on IPv4 address "127.0.0.1", port 5435
> 2025-04-02 11:40:12.713 CST [2904107] LOG:  listening on Unix socket "/tmp/.s.PGSQL.5435"
> 2025-04-02 11:40:12.780 CST [2904113] LOG:  database system was shut down at 2025-04-02 11:30:35 CST
> 2025-04-02 11:40:12.817 CST [2904107] LOG:  database system is ready to accept connections
 done
> server started

And we can use the following command to test the connection:

```bash
./bin/psql postgres -U postgres -p 5435
```



## Debug in Clion

Import the source into Clion, and open it as a *Makefile* project.