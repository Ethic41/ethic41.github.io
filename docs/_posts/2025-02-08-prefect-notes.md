---
layout: post
title:  Prefect Notes
date:   2025-02-08 02:46:19
categories: python_programming
post_id: 9
---

Notes on things learned while working with [Prefect](https://docs.prefect.io/). Somethings may seem obvious, but most likely weren't to me as of time of writing, others are just notes to help me remember things. If you find anything useful here, say hi on [Twitter](https://x.com/dahir_ng).

## Intro

This notes will be most useful for anyone trying to setup a standalone Prefect server. I'm using Prefect v3 and will be using a VM instance on Google Cloud Platform (GCP). The prefect server will be responsible for handling flow runs, scheduling etc. as such it needs to always be running, hence the need for a VM instance instead of a Cloud Run instance to save cost. The VM will utilize an external postgresql database for persistence. The UI will be served using a Cloud Run instance acting as a reverse proxy to the prefect server, this is to allow for easy domain configuration and SSL certificate management.

## Architecture

### Self Host Prefect Server (VM Instance)

- **Operating System**: Debian GNU/Linux 12
- **Machine Type**: e2-medium (2 vCPUs, 4 GB memory)
- **Disk**: 10GB
- **External IPs**: None
- **Internal IPs**: 1
- **Firewall Rules**: Allow port `4200` for prefect server

### External Postgresql Database (Cloud SQL)

- **Database Engine**: Postgresql
- **Machine Type**: db-custom-2-7680 (2 vCPUs, 8 GB memory)
- **Disk**: 10GB
- **External IPs**: None
- **Internal IPs**: 1
- **Firewall Rules**: Allow port `5432` for prefect server
- **Database Name**: prefect-db

### Reverse Proxy (Cloud Run)

- **Container**: Nginx
- **Port**: 80
- **Domain**: prefect.yourdomain.com

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
  $ uv tool install prefect-gcp

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

### Configuring Nginx Reverse Proxy

We will be using Nginx as a reverse proxy to the prefect server. Here's a sample configuration:

```nginx
# /etc/nginx/conf.d/nginx.conf

server {
    listen 80;  # Cloud Run only forwards traffic on port 80 or 443
    server_name prefect.yourdomain.com;  # Accept any hostname

    # WebSocket route for Prefect events
    location /api/events/in {
        proxy_pass http://10.128.0.34:4200/api/events/in;  # Explicitly include the path
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "Upgrade";
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
        proxy_read_timeout 86400;  # Critical for WebSocket long-lived connections
    }

    location / {
        proxy_pass http://10.128.0.34:4200; # Target VM IP & Port
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}
```

Note the websocket configuration for prefect events. This is important for the prefect server to work correctly, without it, you will encounter errors such as:

```bash
...
...
15:54:58.560 | WARNING | prefect.events.clients - Unable to connect to 'wss://prefect.yourdomain.com/api/events/in'. Please check your network settings to ensure websocket connections to the API are allowed. Otherwise event data (including task run data) may be lost. Reason: server rejected WebSocket connection: HTTP 401. Set PREFECT_DEBUG_MODE=1 to see the full error.
15:54:58.562 | ERROR   | GlobalEventLoopThread | prefect._internal.concurrency - Service 'EventsWorker' failed with 1 pending items.
^CReceived SIGINT. Sending SIGINT to the None worker (PID 34980)...
```

To containerize the Nginx server, you can use the following Dockerfile:

```Dockerfile
# Dockerfile

FROM nginx:alpine

# Remove default nginx config
RUN rm /etc/nginx/conf.d/default.conf

# Copy custom nginx config
COPY nginx.conf /etc/nginx/conf.d/

EXPOSE 80
```

#### Daemonizing workers

```bash
# create a systemd service file
$ cat /etc/systemd/system/prefect-worker.service

[Unit]
Description=Prefect Docker Worker
After=network.target

[Service]
# create a prefer user or use your own user
User=prefect
WorkingDirectory=/home/your-user/.prefect
Environment="PREFECT_API_AUTH_STRING=xxxxxxxxx"
ExecStart=/home/your-user/singer-server/.venv/bin/prefect worker start --pool docker-test-pool
Restart=always
StandardOutput=append:/var/log/prefect-docker-server.log
StandardError=append:/var/log/prefect-docker-server.log

[Install]
WantedBy=multi-user.target

# reload systemd
$ sudo systemctl daemon-reload

# enable and start the service
$ sudo systemctl enable prefect-worker.service
$ sudo systemctl start prefect-worker.service
```
