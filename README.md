# Crib Ops CLI Tool Documentation

Welcome to the Crib Ops CLI tool! This guide will help you get started quickly with your new CLI. 

Need more help? Join our new community [Crib Ops on Skool](https://skool.com/cribops) 

## Downloads

For users of different operating systems, we provide specific builds of CribOps CLI. Choose the one that matches your system:

- **MacOS Version**
  - [Download CribOps CLI for MacOS](https://github.com/cloudbedrock/cribops-docs/raw/main/downloads/cribops-cli-mac-2.13.4.zip)

- **Windows Version**
  - [Download CribOps CLI for Windows](https://github.com/cloudbedrock/cribops-docs/raw/main/downloads/cribops-cli-windows-2.13.4.zip)

The CLI tool now supports:
- N8N docker compose file generation for quick local install
- PostgreSQL with PGVector support
- **NEW: Cloudflare Zero Trust tunnel integration for secure webhook access**
- **NEW: Pre-configured CribOps databases (memory, CRM, RAG)**
- **NEW: TablePlus GUI integration for database management**
- **NEW: Custom port support for multiple n8n instances**
- **NEW: Community packages tool usage enabled by default**
- **NEW: API-based credential setup for PostgreSQL databases**
- **NEW: Automatic DNS route refresh for existing tunnels (fixes Error 1033)**
- **NEW: Local tunnel credential storage for better portability**

The CLI is free to download from this repository in the downloads folder. Installer for Windows and Mac. Or just use the docker run commands listed below to run it instantly with no install.



---

## Table of Contents

- [Prerequisites](#prerequisites)
- [Installation](#installation)
- [Getting Started](#getting-started)
- [NEW: Cloudflare Tunnel Setup](#new-cloudflare-tunnel-setup)
- [NEW: Database Management](#new-database-management)
- [Expected Output](#expected-output)
  - [Generated Files Tree](#generated-files-tree)
  - [Example compose.yaml](#example-composeyaml)
- [Docker Compose Commands](#docker-compose-commands)
- [Troubleshooting & Support](#troubleshooting--support)
- [Recent Changes](#recent-changes-v2133)

---

## Prerequisites

- **Docker:** Ensure Docker is installed and running
- **Installers:** Use the provided macOS or Windows installer which installs the CLI and adds it to your PATH
- **Cloudflare Account (Optional):** For tunnel setup, you'll need a Cloudflare account with a domain

---

## Installation

* Note: references to license.txt install is deprecated. CribOps is now offered as a hosted SaaS vs requiring installation and hosting yourself and works with community cribops node
* See Cribops.com for details.

### N8N Easy Install Windows
[![Watch the video](https://img.youtube.com/vi/XKbNmytgwz4/hqdefault.jpg)](https://youtu.be/XKbNmytgwz4)

### N8N Easy Install macOS
[![Watch the video](https://img.youtube.com/vi/XnsjeuT6LXE/hqdefault.jpg)](https://youtu.be/XnsjeuT6LXE)

1. **Download and Install:**  
   Run the macOS or Windows installer for Crib Ops CLI. The installer adds `cribops-cli` to your system's PATH. The linux
   version can be placed manually into an appropriate path of your choosing.
   
   You can also run the cribops-cli via docker run if you don't wish to install the cli on your os for linux and mac

   macOS/Linux
   
   ```bash
   docker run --rm -v "$(pwd):/app" -w /app ghcr.io/cloudbedrock/cribops-cli setup -h
   ```
   
   Windows Command Prompt

   ```bat
   docker run --rm -v "%cd%:/app" -w /app ghcr.io/cloudbedrock/cribops-cli setup -h
   ```

---

## Getting Started

### Getting Help on Setup Options
```bash
cribops-cli setup -h
```

### Basic Setup
```bash
# Simple local setup
cribops-cli setup

# Setup with custom port (for multiple instances)
cribops-cli setup --n8n-port 5679

# Setup with custom PostgreSQL port
cribops-cli setup --postgres-port 5433
```

---

## NEW: Cloudflare Tunnel Setup

Expose your n8n instance securely to the internet for webhooks without port forwarding!

### Prerequisites
1. Install cloudflared:
   - **macOS:** `brew install cloudflared`
   - **Windows:** Download from [Cloudflare](https://github.com/cloudflare/cloudflared/releases)
   - **Linux:** `wget -q https://github.com/cloudflare/cloudflared/releases/latest/download/cloudflared-linux-amd64.deb && sudo dpkg -i cloudflared-linux-amd64.deb`

2. Have a domain managed by Cloudflare

### Quick Setup
```bash
# Interactive setup (prompts for tunnel name and hostname)
cribops-cli setup --cloudflare-tunnel

# One-line setup (no prompts!)
cribops-cli setup --cloudflare-tunnel --tunnel-name my-n8n --tunnel-hostname n8n.example.com

# Multiple instances with different ports and tunnels
cribops-cli setup --n8n-port 5679 --postgres-port 5433 --cloudflare-tunnel --tunnel-name n8n-dev --tunnel-hostname n8n-dev.example.com
```

### What Happens
1. Browser opens for Cloudflare authentication (first time only)
2. Creates a secure tunnel to your local n8n
3. Configures DNS automatically
4. Sets up HTTPS with your custom domain
5. Includes cloudflared service in docker-compose

Your n8n will be accessible at `https://n8n.example.com` with all Cloudflare security features!

---

## NEW: Database Management

CribOps CLI now creates **4 pre-configured PostgreSQL databases** with n8n credentials ready to use:

### Available Databases
- **n8n** - System database for workflows and credentials
- **cribops_memory** - Long-term memory storage for AI agents
- **cribops_crm** - Customer relationship management data
- **cribops_rag** - Vector storage for RAG (Retrieval Augmented Generation)

### TablePlus Integration

Open any database in TablePlus GUI with a single command:

```bash
# Open default n8n database
cribops-cli db open

# Open specific database
cribops-cli db open memory
cribops-cli db open crm
cribops-cli db open rag

# List all available databases
cribops-cli db list
```

**Features:**
- Works on macOS, Windows, and Linux
- Automatic password retrieval from `.env`
- Pre-configured connection with CribOps branding
- Falls back to manual URL if TablePlus not installed

### Using Databases in n8n
After setup, you'll find pre-configured credentials in n8n:
- CribOps - n8n System
- CribOps - Memory
- CribOps - CRM
- CribOps - RAG

Just select the appropriate credential when creating PostgreSQL nodes in your workflows!

### API-Based Credential Setup

You can also create PostgreSQL credentials automatically using the n8n API:

```bash
# Create credentials using n8n API key
cribops-cli db setup-credentials YOUR_API_KEY

# This will:
# 1. Create all CribOps databases if they don't exist
# 2. Set up PostgreSQL credentials in n8n for each database
```

To get your n8n API key:
1. Go to your n8n instance settings
2. Navigate to "API" section
3. Generate or copy your API key

---

## Community Packages

n8n is configured with `N8N_COMMUNITY_PACKAGES_ALLOW_TOOL_USAGE=true` by default, which enables:
- Installation of community nodes from npm
- Access to extended functionality beyond core n8n nodes
- Integration with specialized tools and services

This allows you to extend n8n with community-contributed nodes for additional integrations and capabilities.

---

## Expected Output

### Generated Files Tree
```
.
├── compose.yaml                      # Docker Compose configuration
├── .env                              # Environment variables (includes POSTGRES_PORT)
├── .gitignore                        # Updated with secrets/, .env, and local files
├── init-dbs.sql                      # Database initialization (creates 3 databases)
├── secrets/                          # Directory containing secrets
│   └── .postgrespassword             # PostgreSQL password file
└── (Optional with Cloudflare tunnel)
    ├── [tunnel-name]-config.yml      # Tunnel configuration (e.g., n8n6-config.yml)
    └── .cloudflared-[tunnel-name].json  # Local tunnel credentials (e.g., .cloudflared-n8n6.json)
```

### Example compose.yaml

**Standard setup (without Cloudflare tunnel):**
```yaml
version: '3.8'

services:
  postgres:
    image: ghcr.io/cloudbedrock/postgres:latest
    restart: always
    ports:
      - "${POSTGRES_PORT:-5432}:5432"  # Optional external access
    environment:
      POSTGRES_PASSWORD: ${POSTGRES_PASSWORD}
      POSTGRES_USER: postgres
      POSTGRES_DB: n8n
      PGDATA: /var/lib/postgresql/data/pgdata
    volumes:
      - postgres_data:/var/lib/postgresql/data
      - ./init-dbs.sql:/docker-entrypoint-initdb.d/init-dbs.sql
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U postgres"]
      interval: 10s
      timeout: 5s
      retries: 5
    networks:
      - n8n-network

  n8n:
    image: ${N8N_IMAGE:-n8nio/n8n:latest}
    restart: always
    environment:
      DB_TYPE: postgresdb
      DB_POSTGRESDB_DATABASE: n8n
      DB_POSTGRESDB_HOST: postgres
      DB_POSTGRESDB_PORT: 5432
      DB_POSTGRESDB_USER: postgres
      DB_POSTGRESDB_PASSWORD: ${DB_POSTGRESDB_PASSWORD}
      DB_POSTGRESDB_SCHEMA: public
      WEBHOOK_URL: ${WEBHOOK_URL}
      N8N_HOST: ${N8N_HOST}
      GENERIC_TIMEZONE: ${GENERIC_TIMEZONE}
      N8N_RUNNERS_ENABLED: ${N8N_RUNNERS_ENABLED:-true}
      N8N_ENCRYPTION_KEY: ${N8N_ENCRYPTION_KEY}
      N8N_COMMUNITY_PACKAGES_ALLOW_TOOL_USAGE: ${N8N_COMMUNITY_PACKAGES_ALLOW_TOOL_USAGE:-true}
    ports:
      - "5678:5678"
    volumes:
      - n8n_data:/home/node/.n8n
      - ./n8n-credentials:/home/node/.n8n/credentials
    depends_on:
      postgres:
        condition: service_healthy
    networks:
      - n8n-network

volumes:
  postgres_data:
  n8n_data:

networks:
  n8n-network:
    driver: bridge
```

**With Cloudflare tunnel:**
```yaml
version: '3.8'

services:
  cloudflared:
    image: cloudflare/cloudflared:latest
    restart: always
    command: tunnel --config /etc/cloudflared/config.yml run
    volumes:
      - ./[tunnel-name]-config.yml:/etc/cloudflared/config.yml:ro
      - ./.cloudflared-[tunnel-name].json:/etc/cloudflared/credentials.json:ro
    networks:
      - n8n-network

  postgres:
    image: ghcr.io/cloudbedrock/postgres:latest
    restart: always
    ports:
      - "${POSTGRES_PORT:-5432}:5432"  # Optional external access
    environment:
      POSTGRES_PASSWORD: ${POSTGRES_PASSWORD}
      POSTGRES_USER: postgres
      POSTGRES_DB: n8n
      PGDATA: /var/lib/postgresql/data/pgdata
    volumes:
      - postgres_data:/var/lib/postgresql/data
      - ./init-dbs.sql:/docker-entrypoint-initdb.d/init-dbs.sql
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U postgres"]
      interval: 10s
      timeout: 5s
      retries: 5
    networks:
      - n8n-network

  n8n:
    image: ${N8N_IMAGE:-n8nio/n8n:latest}
    restart: always
    environment:
      DB_TYPE: postgresdb
      DB_POSTGRESDB_DATABASE: n8n
      DB_POSTGRESDB_HOST: postgres
      DB_POSTGRESDB_PORT: 5432
      DB_POSTGRESDB_USER: postgres
      DB_POSTGRESDB_PASSWORD: ${DB_POSTGRESDB_PASSWORD}
      DB_POSTGRESDB_SCHEMA: public
      WEBHOOK_URL: ${WEBHOOK_URL}
      N8N_HOST: ${N8N_HOST}
      GENERIC_TIMEZONE: ${GENERIC_TIMEZONE}
      N8N_RUNNERS_ENABLED: ${N8N_RUNNERS_ENABLED:-true}
      N8N_ENCRYPTION_KEY: ${N8N_ENCRYPTION_KEY}
      N8N_COMMUNITY_PACKAGES_ALLOW_TOOL_USAGE: ${N8N_COMMUNITY_PACKAGES_ALLOW_TOOL_USAGE:-true}
    ports:
      - "5678:5678"
    volumes:
      - n8n_data:/home/node/.n8n
      - ./n8n-credentials:/home/node/.n8n/credentials
    depends_on:
      postgres:
        condition: service_healthy
    networks:
      - n8n-network

volumes:
  postgres_data:
  n8n_data:

networks:
  n8n-network:
    driver: bridge
```

### Example .env file
```
POSTGRES_PASSWORD=<generated-32-character-hex>
DB_POSTGRESDB_PASSWORD=<same-as-above>
WEBHOOK_URL=http://localhost:5678
N8N_HOST=localhost:5678
GENERIC_TIMEZONE=America/New_York
N8N_RUNNERS_ENABLED=true
N8N_ENCRYPTION_KEY=<generated-128-character-hex>
N8N_COMMUNITY_PACKAGES_ALLOW_TOOL_USAGE=true
N8N_ENFORCE_SETTINGS_FILE_PERMISSIONS=true
N8N_PUBLIC_API_DISABLED=false
POSTGRES_PORT=5432  # Optional, defaults to 5432
```

### Example init-dbs.sql
```sql
-- Create databases for n8n and application use
-- Note: n8n database is created by Docker environment, so we skip it
CREATE DATABASE cribops_memory;
CREATE DATABASE cribops_crm;
CREATE DATABASE cribops_rag;

-- Grant all privileges on the databases to postgres user (already superuser, but being explicit)
GRANT ALL PRIVILEGES ON DATABASE n8n TO postgres;
GRANT ALL PRIVILEGES ON DATABASE cribops_memory TO postgres;
GRANT ALL PRIVILEGES ON DATABASE cribops_crm TO postgres;
GRANT ALL PRIVILEGES ON DATABASE cribops_rag TO postgres;
```

---

## Docker Compose Commands

After generating your `compose.yaml` and secret files, you can use Docker Compose to manage your services. Here are some common commands:

- **Run in Foreground:**  
  ```bash
  docker compose up
  ```
  This starts all services and streams the logs to your console.

- **Run in Detached Mode:**  
  ```bash
  docker compose up -d
  ```
  This starts the services in the background.

- **Stop Containers:**  
  ```bash
  docker compose stop
  ```
  This stops all running containers without removing them.

- **Take Down Containers and Network:**  
  ```bash
  docker compose down
  ```
  This stops containers and removes the containers, networks, and any created volumes associated with the project.

- **View Logs:**  
  ```bash
  docker compose logs
  ```
  To follow logs in real time, use:
  ```bash
  docker compose logs -f
  ```

- **Check Cloudflare Tunnel Status:**
  ```bash
  docker compose logs cloudflared
  ```

*Note:* If you're using the legacy Docker Compose (version 1), replace `docker compose` with `docker-compose`.

---

## Troubleshooting & Support

### Common Issues

**Cloudflare Tunnel Issues:**
- If cloudflared is not installed, the CLI will provide platform-specific installation instructions
- For DNS issues, ensure your domain is active in Cloudflare
- Check tunnel status with `docker compose logs cloudflared`
- **Error 1033 Fix:** The CLI now automatically refreshes DNS routes when reusing existing tunnels

**Database Connection Issues:**
- Ensure Docker is running before setup
- Check PostgreSQL logs: `docker compose logs postgres`
- Verify credentials in `.env` file

**TablePlus Connection Issues:**
- If TablePlus doesn't open, install from [tableplus.com](https://tableplus.com/download)
- Manual connection URL is provided as fallback
- Password is stored in `.env` file

**Multiple Instance Setup:**
- Use different ports with `--n8n-port` flag
- Each instance needs its own directory
- Cloudflare tunnels must have unique names

### Getting Help
- Join our community [Crib Ops on Skool](https://skool.com/cribops)
- Check the [Cloudflare Tunnel Guide](CLOUDFLARE_TUNNEL_GUIDE.md) for detailed tunnel setup
- View all options with `cribops-cli --help`

---

## Recent Changes (v2.13.3)

### New Features
- **API-based credential setup:** Create PostgreSQL credentials via n8n API with `db setup-credentials`
- **Custom PostgreSQL port:** Use `--postgres-port` flag for external database access
- **Improved tunnel handling:** Automatic DNS route refresh fixes Error 1033 for existing tunnels
- **Local credential storage:** Tunnel credentials stored locally for better portability

### Bug Fixes
- Fixed tunnel UUID extraction for new Cloudflare output format
- Fixed database initialization preventing container startup
- Fixed tunnel configuration to always use internal port 5678
- Fixed credential path issues by using local storage
- Added automatic database creation when using API setup

### Improvements
- TablePlus integration now respects custom PostgreSQL ports
- Better error messages and recovery for tunnel setup
- Cleaner compose file generation with proper credential mounting

---

By following this guide, you should be able to configure your environment quickly and securely with all the new powerful features. Enjoy using Crib Ops CLI!
