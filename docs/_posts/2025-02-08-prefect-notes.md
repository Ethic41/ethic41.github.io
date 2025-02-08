---
layout: post
title:  Prefect Notes
date:   2025-02-08 02:46:19
categories: python_programming
post_id: 8
---

Notes on things learned while working with [Prefect](https://docs.prefect.io/). Somethings may seem obvious, but most likely weren't to me as of time of writing, others are just notes to help me remember things. If you find anything useful here, say hi on [Twitter](https://x.com/dahir_ng).

## Intro

This notes will be most useful for anyone trying to setup a standalone Prefect server. I'm using Prefect v3 and will be using a VM instance on Google Cloud Platform (GCP).

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
  uv tool install prefect
  ```
