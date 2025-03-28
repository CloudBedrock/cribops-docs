# Crib Ops CLI Tool Documentation

Welcome to the Crib Ops CLI tool! This guide will help you get started quickly with your new CLI. The CLI tool now supports N8N docker compose file generation for quick local install of N8N.  The CLI is free to download from this repository in the downloads folder.  Installer for Windows and Mac.  Our next step is adding the option to install a local LLM along with Quadrant similar to the N8N local ai starter kit but with CLI generation.

It's also the tool to manage activating a Crib Ops License.  Crib Ops provides a hosted SaaS offering that provides an easy to use front door to your webhooks for N8N or other webhook endpoints for automation.  It provides durable queues for enhancing availability and scalability of your automation services.

Once you subscribe to [cribops.com](https://cribops.com), you’ll receive an email with your **license.txt** file. With our macOS and Windows installers, the CLI is automatically added to your PATH. All you need to do is save your license file to a directory of your choosing and run the setup.

Need more help?  Join our new community [Crib Ops on Skool](https://skool.com/cribops) 
---

## Table of Contents

- [Prerequisites](#prerequisites)
- [Installation](#installation)
- [Getting Started](#getting-started)
  - [Using the License File](#using-the-license-file) (Crib Ops Only)
  - [Passing the License Key as an Argument](#passing-the-license-key-as-an-argument)
- [Expected Output](#expected-output)
  - [Generated Files Tree](#generated-files-tree)
  - [Example compose.yaml](#example-composeyaml)
- [Docker Compose Commands](#docker-compose-commands)
- [n8n Installation & Configuration](#n8n-installation--configuration)
- [Troubleshooting & Support](#troubleshooting--support)

---

## Prerequisites

- **License File:** Upon purchase, you will receive an email with your `license.txt`.  
- **Installers:** Use the provided macOS or Windows installer which installs the CLI and adds it to your PATH.  
- **Working Directory:** Save your `license.txt` file into your preferred directory before running the setup.

---

## Installation


### N8N Easy Install Windows
[![Watch the video](https://img.youtube.com/vi/XKbNmytgwz4/hqdefault.jpg)](https://youtu.be/XKbNmytgwz4)

### N8N Easy Install macOS
[![Watch the video](https://img.youtube.com/vi/XnsjeuT6LXE/hqdefault.jpg)](https://youtu.be/XnsjeuT6LXE)


### Crib Ops macOS Installation
[![Watch the video](https://i.vimeocdn.com/video/1993245817-f626d8e14774e83f7af84d3152f9c14d2cc7e0b7c233beae30c2b59fe9f73c0e-d_640)](https://vimeo.com/1065686377)


### Crib Ops Windows Installation
[![Watch the video](https://i.vimeocdn.com/video/1993258988-b4bdf0e30dc03497db8d2524b86edb46659d7875ee26a476f717b0a861a04f9b-d_640)](https://vimeo.com/1065697602?share=copy)

### Using N8N with HighLevel at Scale - Integration On Steroids
[![Watch the video](https://img.youtube.com/vi/JoWJaDgFPAE/hqdefault.jpg)](https://youtu.be/JoWJaDgFPAE?si=34DpfgC-tIaeVycA)




1. **Download and Install:**  
   Run the macOS or Windows installer for Crib Ops CLI. The installer adds `cribops-cli` to your system’s PATH. The linux
   version can be placed manually into an appropriate path of your choosing.
   
   You can also run the cribops-cli via docker run if you don't wish to install the cli on your os for linux and mac

   ```bash
   docker run --rm -v "$(pwd):/app" -w /app ghcr.io/cloudbedrock/cribops-cli
   ```
   And for Windows Command Prompt

   ```bat
   docker run --rm -v "%cd%:/app" -w /app ghcr.io/cloudbedrock/cribops-cli
   ```
   

3. **Prepare License File:**  
   Save the attached `license.txt` file (from your purchase email) in the directory where you will run the CLI.

---

## Getting Started

### Using the License File

Simply place your `license.txt` in your working directory and run the following command in your terminal:

```bash
cribops-cli setup
```

The tool will:
- Read your `license.txt` file.
- Contact the API at `https://api.cribops.com/credentials/{licenseKey}` to retrieve your credentials.
- Generate secret files and a Docker Compose file for your environment.

### Passing the License Key as an Argument

Alternatively, you can pass your license key directly:

```bash
cribops-cli setup YOUR_LICENSE_KEY_HERE
```

This bypasses the need for a local `license.txt` file.

---

## Expected Output

After running the setup, you should see messages similar to:

```
Secret ACCESS_KEY_ID written to secrets/.accesskeyid
Secret SECRET_ACCESS_KEY written to secrets/.secretaccesskey
Secret CUSTOMER_UUID written to secrets/.customeruuid
Secret CRIBOPS_LICENSE_KEY written to secrets/.cribopslicensekey
Secret CRIBOPS_ITEM_ID written to secrets/.cribopsitemid
Secret SECRET_KEY_BASE written to secrets/.secretkeybase
Secret POSTGRES_PASSWORD written to secrets/.postgrespassword
Secrets successfully written to the secrets directory.
Use these files as secrets in your Docker Compose configuration.
Generated compose.yaml successfully.
```

If credentials have already been retrieved, you’ll receive a prompt informing you that they can only be retrieved once. In that case, follow the instructions to contact support if needed.

### Generated Files Tree

Once setup is complete, your project directory will include files similar to the following tree:

```plaintext
.
├── license.txt
├── compose.yaml
├── secrets
│   ├── .accesskeyid
│   ├── .cribopsitemid
│   ├── .cribopslicensekey
│   ├── .customeruuid
│   ├── .databaseurl
│   ├── .postgrespassword
│   ├── .secretaccesskey
│   └── .secretkeybase
└── .gitignore
```

### Example compose.yaml

Here’s an example of the generated **compose.yaml** file:

```yaml
services:
  db:
    image: postgres:17.4
    healthcheck:
      test: ["CMD", "pg_isready", "--username=postgres"]
      interval: 10s
      timeout: 5s
      retries: 5
    deploy:
      restart_policy:
        condition: on-failure
        delay: 5s
        max_attempts: 3
        window: 120s
    environment:
      POSTGRES_PASSWORD_FILE: /run/secrets/postgrespassword
      PGDATA: /var/lib/postgresql/data/pgdata
    volumes:
      - db_data:/var/lib/postgresql/data
    ports:
      - ${POSTGRES_PORT:-5432}
    secrets:
      - postgrespassword

  cribops:
    image: ${WEB_IMAGE:-ghcr.io/cloudbedrock/cribops:latest}
    deploy:
      restart_policy:
        condition: on-failure
        delay: 5s
        max_attempts: 3
        window: 120s
    environment:
      DATABASE_URL_FILE: /run/secrets/databaseurl
      SECRET_KEY_BASE_FILE: /run/secrets/secretkeybase
      ACCESS_KEY_ID_FILE: /run/secrets/accesskeyid
      SECRET_ACCESS_KEY_FILE: /run/secrets/secretaccesskey
      CRIBOPS_LICENSE_KEY_FILE: /run/secrets/cribopslicensekey
      CRIBOPS_ITEM_ID_FILE: /run/secrets/cribopsitemid
      CUSTOMER_UUID_FILE: /run/secrets/customeruuid
      PHX_HOST: localhost
      PORT: 4000
      CHECK_ORIGIN: "//localhost:4000"
    ports:
      - "4000:4000"
    command: >
      bash -c "bin/migrate && \
               bin/server"
    depends_on:
      - db
    secrets:
      - secretkeybase
      - databaseurl
      - accesskeyid
      - secretaccesskey
      - cribopslicensekey
      - cribopsitemid
      - customeruuid

volumes:
  db_data:

secrets:
  postgrespassword:
    file: ./secrets/.postgrespassword
  secretkeybase:
    file: ./secrets/.secretkeybase
  databaseurl:
    file: ./secrets/.databaseurl
  accesskeyid:
    file: ./secrets/.accesskeyid
  secretaccesskey:
    file: ./secrets/.secretaccesskey
  cribopslicensekey:
    file: ./secrets/.cribopslicensekey
  cribopsitemid:
    file: ./secrets/.cribopsitemid
  customeruuid:
    file: ./secrets/.customeruuid
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

*Note:* If you’re using the legacy Docker Compose (version 1), replace `docker compose` with `docker-compose`.

---
### n8n-installation--configuration
In addition to the Crib Ops setup, the CLI now supports installing n8n with Postgres using Docker Compose. During n8n setup, the CLI will prompt you (or use the defaults) for the following dynamic settings:

WEBHOOK_URL:
The URL at which n8n will be accessible.
Default: http://localhost:5678
Example (FQDN with HTTPS): https://n8n.example.com

N8N_HOST:
The hostname used by n8n.
Default: localhost:5676
Example: n8n.example.com

GENERIC_TIMEZONE:
The timezone setting for cron and scheduling nodes within n8n.
Default: America/New_York
Example: Europe/Berlin

Setup Process for n8n
Run the CLI:
Execute the following command in the directory where your license.txt resides:

```bash
cribops-cli n8n-setup
```
This will generate:

A .env file containing the provided values.
A compose.yaml file with the necessary Docker Compose configuration for n8n and Postgres.
Review the Generated Files:
Your directory should now include:

compose.yaml
.env
A secrets directory with your secret files.
Launch n8n:
Simply run:

```bash
docker compose -f compose.yaml up -d
```

Docker Compose will automatically load the .env file, and your n8n service will be configured with the values you provided.

Access n8n:
Open your browser and navigate to the URL specified by WEBHOOK_URL (default is http://localhost:5678).

## Troubleshooting & Support

- **First-Time Setup Only:** Credentials are retrieved only on the first setup. If you have already run the setup and lost your files, please contact [support@cloudbedrock.com](mailto:support@cloudbedrock.com) with your license key.
- **Self Help License Key Reset will be added shorlty as well as other license management features.
- **License Issues:** Ensure that your `license.txt` file is placed correctly or that you pass the valid license key as an argument.
- **Further Help:** For any issues or further assistance, please visit [cribops.com](https://cribops.com) or reach out to our support team.

---

By following this guide, you should be able to configure your environment quickly and securely. Enjoy using Crib Ops CLI!
