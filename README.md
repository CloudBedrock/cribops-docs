# Crib Ops CLI Tool Documentation

Welcome to the Crib Ops CLI tool! This guide will help you get started quickly with your new CLI. Once you subscribe to Crib Ops, you’ll receive an email with your **license.txt** file. With our macOS and Windows installers, the CLI is automatically added to your PATH. All you need to do is save your license file to a directory of your choosing and run the setup.

We also have a docker version of the cli.  This can be run like:

```bash
docker run -v "$(pwd):/app" -w /app ghcr.io/cloudbedrock/cribops-cli
```

---

## Table of Contents

- [Prerequisites](#prerequisites)
- [Installation](#installation)
- [Getting Started](#getting-started)
  - [Using the License File](#using-the-license-file)
  - [Passing the License Key as an Argument](#passing-the-license-key-as-an-argument)
- [Expected Output](#expected-output)
  - [Generated Files Tree](#generated-files-tree)
  - [Example compose.yaml](#example-composeyaml)
- [Troubleshooting & Support](#troubleshooting--support)

---

## Prerequisites

- **License File:** Upon purchase, you will receive an email with your `license.txt`.  
- **Installers:** Use the provided macOS or Windows installer which installs the CLI and adds it to your PATH.  
- **Working Directory:** Save your `license.txt` file into your preferred directory before running the setup.

---

## Installation

1. **Download and Install:**  
   Run the macOS or Windows installer for Crib Ops CLI. The installer adds `cribops-cli` to your system’s PATH.

2. **Prepare License File:**  
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

## Troubleshooting & Support

- **First-Time Setup Only:** Credentials are retrieved only on the first setup. If you have already run the setup and lost your files, please contact [support@cloudbedrock.com](mailto:support@cloudbedrock.com) with your license key.
- **License Issues:** Ensure that your `license.txt` file is placed correctly or that you pass the valid license key as an argument.
- **Further Help:** For any issues or further assistance, please visit [cribops.com](https://cribops.com) or reach out to our support team.

---

By following this guide, you should be able to configure your environment quickly and securely. Enjoy using Crib Ops CLI!
