---
layout: post
title:  Prefect Notes
date:   2025-02-08 02:46:19
categories: python_programming
post_id: 9
---

Notes on things learned while working with [Prefect](https://docs.prefect.io/). Somethings may seem obvious, but most likely weren't to me as of time of writing, others are just notes to help me remember things. If you find anything useful here, say hi on [Twitter](https://x.com/dahir_ng).

## Intro

This notes will be most useful for anyone trying to setup a standalone Prefect server. I'm using Prefect v3 and will be using a VM instance on Google Cloud Platform (GCP). The prefect server will be responsible for handling flow runs, scheduling etc. as such it needs to always be running, hence the need for a VM instance instead of a Cloud Run instance to save cost. The VM will utilize an external postgresql database for persistence.

### VM Specs & Configs

- **Operating System**: Debian GNU/Linux 12
- **Machine Type**: e2-medium (2 vCPUs, 4 GB memory)
- **Disk**: 10GB
- **External IPs**: None
- **Internal IPs**: 1
- **Firewall Rules**: Allow port `4200` for prefect server

### Installation

- **Python**: 3.11.2

- **Setup uv for Python package management**:

  ```bash
  curl -LsSf https://astral.sh/uv/install.sh | sh
  ```

- **Install Prefect**:

  ```bash
  # Install prefect
  $ uv tool install prefect

  # check version
  $ prefect version
  Version:             3.1.10
  API version:         0.8.4
  Python version:      3.11.2
  ...

  # start the server
  $ prefect server start
  ...
  ```

### Configuring Prefect Server

We will be using the `prefect.toml` file to configure the server. If you've started the server once, you should have a `~/.prefect` directory in your home directory. The content should look like this:

```bash
$ ls ~/.prefect
memo_store.toml  prefect.db  prefect.toml  profiles.toml
```

The `prefect.db` file is the sqlite database used by the server when you haven't configured an external database. The `prefect.toml` file is the configuration file for the server. Here's a sample configuration:

```toml
# ~/.prefect/prefect.toml

[server.api]
host = "0.0.0.0"
port = 4200
auth_string = "deadbeefcafebabedeadbeefcafebabedadbabecafedad"

[server.ui]
api_url = "https://prefect.yourdomain.com/api"

[server.database]
connection_url = "postgresql+asyncpg://postgres:password@10.104.120.4:5432/prefect-db"
migrate_on_start = "True"
```

#### Config Breakdown

- `server.api`: This section configures the API server. The `host` and `port` are the address and port the server will listen on. The `auth_string` is the token used to authenticate with the server, it's important to keep this secret.

- `server.ui`: This section configures the UI server. The `api_url` is the URL to the API server.

- `server.database`: This section configures the database connection. The `connection_url` is the URL to the postgresql database. The `migrate_on_start` is a boolean that tells the server to run migrations on start.
