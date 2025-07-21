# Crib Ops CLI Tool Documentation

Welcome to the Crib Ops CLI tool! This guide will help you get started quickly with your new CLI. 

## Downloads

For users of different operating systems, we provide specific builds of CribOps CLI. Choose the one that matches your system:

- **MacOS Version**
  - [Download CribOps CLI for MacOS](https://github.com/cloudbedrock/cribops-docs/raw/main/downloads/cribops-cli-mac-2.5.1.zip)

- **Windows Version**
  - [Download CribOps CLI for Windows](https://github.com/cloudbedrock/cribops-docs/raw/main/downloads/cribops-cli-windows-2.5.1.zip)



The CLI tool now supports N8N docker compose file generation for quick local install of N8N and a postgres image with PGVector for convenience.

The CLI is free to download from this repository in the downloads folder.  Installer for Windows and Mac.  Or just use the docker run commands listed below to run it instantly with no install. 

Need more help?  Join our new community [Crib Ops on Skool](https://skool.com/cribops) 
---

## Table of Contents

- [Prerequisites](#prerequisites)
- [Installation](#installation)
- [Getting Started](#getting-started)
- [Expected Output](#expected-output)
  - [Generated Files Tree](#generated-files-tree)
  - [Example compose.yaml](#example-composeyaml)
- [Docker Compose Commands](#docker-compose-commands)
- [Troubleshooting & Support](#troubleshooting--support)

---

## Prerequisites

- **Installers:** Use the provided macOS or Windows installer which installs the CLI and adds it to your PATH.  

---

## Installation

* Note: references to license.txt install is deprecated.  CribOps is now offered as a hosted SaaS vs requiring installation and hosting youself and works with community cribops node
* See Cribops.com for details.


### N8N Easy Install Windows
[![Watch the video](https://img.youtube.com/vi/XKbNmytgwz4/hqdefault.jpg)](https://youtu.be/XKbNmytgwz4)

### N8N Easy Install macOS
[![Watch the video](https://img.youtube.com/vi/XnsjeuT6LXE/hqdefault.jpg)](https://youtu.be/XnsjeuT6LXE)


1. **Download and Install:**  
   Run the macOS or Windows installer for Crib Ops CLI. The installer adds `cribops-cli` to your system’s PATH. The linux
   version can be placed manually into an appropriate path of your choosing.
   
   You can also run the cribops-cli via docker run if you don't wish to install the cli on your os for linux and mac

   macOS/Linux
   
    ```bash
   docker run --rm -v "$(pwd):/app" -w /app ghcr.io/cloudbedrock/cribops-cli setup
   ```
   Windows Command Prompt

   ```bat
   docker run --rm -v "%cd%:/app" -w /app ghcr.io/cloudbedrock/cribops-cli setup
   ```



---

## Getting Started

### Getting Help on Setup Options
```bash
cribops-cli setup -h
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

By following this guide, you should be able to configure your environment quickly and securely. Enjoy using Crib Ops CLI!
