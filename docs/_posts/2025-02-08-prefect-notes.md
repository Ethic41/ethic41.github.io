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
