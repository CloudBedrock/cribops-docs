# Crib Ops CLI Tool Documentation

Welcome to the Crib Ops CLI tool! Deploy N8N locally with Docker or scale to production-ready AWS infrastructure with a single command.

Need more help? Join our new community https://skool.com/cribops

## N8N Easy Install & AWS Cloud Deployment

[![Watch the video](https://img.youtube.com/vi/OEyuTy4KauA/hqdefault.jpg)](https://youtu.be/OEyuTy4KauA?si=F2J7pT4n8iiU0efZ)

## Downloads

For users of different operating systems, we provide specific builds of CribOps CLI. Choose the one that matches your system:

- **MacOS Version**
  - https://github.com/cloudbedrock/cribops-docs/raw/main/downloads/cribops-cli-mac-3.1.7.zip
- **Windows Version**
  - https://github.com/cloudbedrock/cribops-docs/raw/main/downloads/cribops-cli-windows-3.1.7.zip

The CLI tool now supports:

## 🐳 Local Development

- N8N docker compose file generation for quick local install
- PostgreSQL with PGVector support
- Cloudflare Zero Trust tunnel integration for secure webhook access
- Pre-configured CribOps databases (memory, CRM, RAG)
- TablePlus GUI integration for database management
- Custom port support for multiple n8n instances
- Community packages tool usage enabled by default
- API-based credential setup for PostgreSQL databases

## ☁️ NEW: AWS Cloud Production Deployment

- One-command AWS infrastructure setup with `cribops-cli aws init`
- Production-ready Aurora PostgreSQL with automatic backups
- Auto-scaling ECS containers with load balancer
- Private database access via secure SSM tunneling
- Real-time deployment monitoring with live status updates
- Zero-downtime operations including scaling and redeployments
- Automatic SSL certificates and DNS management
- Cost-optimized architecture starting from ~$112/month *OnDemand

The CLI is free to download from this repository in the downloads folder. Installer for Windows and Mac. Or just use the docker run commands listed below to run it instantly with no install.

---
## Table of Contents

- [Prerequisites](#prerequisites)
- [Installation](#installation)
- [🚀 NEW: AWS Cloud Deployment](#-new-aws-cloud-deployment)
- [🐳 Local Development Setup](#-local-development-setup)
- [Getting Started Locally](#getting-started-locally)
- [Cloudflare Tunnel Setup](#cloudflare-tunnel-setup)
- [Database Management](#database-management)
- [Expected Output](#expected-output)
- [Docker Compose Commands](#docker-compose-commands)
- [Troubleshooting & Support](#troubleshooting--support)
- [Recent Changes](#recent-changes)

---
## Prerequisites

- **Docker**: Ensure Docker is installed and running (for local development)
- **AWS CLI**: For cloud deployments, install and configure AWS CLI with appropriate permissions
- **Installers**: Use the provided macOS or Windows installer which installs the CLI and adds it to your PATH
- **Cloudflare Account** (Optional): For tunnel setup, you'll need a Cloudflare account with a domain

---
## Installation

1. **Download and Install:** Run the macOS or Windows installer for Crib Ops CLI. The installer adds `cribops-cli` to your system's PATH.

2. You can also run the cribops-cli via docker run if you don't wish to install the cli on your OS:

3. **macOS/Linux:**
```bash
docker run --rm -v "$(pwd):/app" -w /app ghcr.io/cloudbedrock/cribops-cli setup -h
```

4. **Windows Command Prompt:**
```cmd
docker run --rm -v "%cd%:/app" -w /app ghcr.io/cloudbedrock/cribops-cli setup -h
```

---
## 🚀 NEW: AWS Cloud Deployment

Deploy production-ready N8N infrastructure to AWS with enterprise-grade security and scalability!

### ☁️ One-Command Deployment

```bash
# Deploy complete AWS infrastructure
cribops-cli aws init

# Check deployment status
cribops-cli aws status

# Connect to database securely
cribops-cli aws db connect
```

### 🏗️ What Gets Deployed

**Complete AWS Infrastructure:**
- VPC with public/private subnets across availability zones
- Application Load Balancer with SSL termination
- ECS Fargate auto-scaling container service
- Aurora PostgreSQL with automated backups and encryption
- Route 53 DNS management with your custom domain
- Bastion instance for secure database access
- CloudWatch logging and monitoring

**Enterprise Security:**
- Private database in isolated subnets
- No direct internet access to database
- SSM tunneling for secure administrative access
- AWS Secrets Manager for credential rotation
- IAM roles with least privilege access

### 📊 Real-Time Management

```bash
# View complete deployment status
cribops-cli aws status
# Shows: Stack info, resource counts, service health, URLs

# Scale your deployment
cribops-cli aws scale 2
# Scales to 2 N8N instances for high availability

# Zero-downtime deployments
cribops-cli aws redeploy
# Rolling update with no service interruption

# Secure database access
cribops-cli aws db connect --auto-open
# Opens TablePlus with secure tunnel automatically
```

### 💰 Cost-Optimized Architecture

Starting from approximately $50-80/month for production workloads:
- Aurora PostgreSQL
- ECS Fargate containers (pay per use)
- Application Load Balancer
- Minimal data transfer costs
- No NAT gateway fees (optimized routing)

### 🔒 Secure Database Access

No more IP whitelisting! Access your private database securely:

```bash
# Connect with TablePlus auto-launch
cribops-cli aws db connect --auto-open

# Connect on custom port
cribops-cli aws db connect --port 5433

# View connection details
cribops-cli aws status
```

**Features:**
- Private database (no internet access)
- SSM tunneling through bastion host
- Works with TablePlus, pgAdmin, or any PostgreSQL client
- Automatic credential retrieval from AWS Secrets Manager
- Session Manager plugin auto-installation guide

### 🎯 Production Operations

```bash
# Infrastructure management
cribops-cli aws init      # Create infrastructure
cribops-cli aws status    # Monitor deployment
cribops-cli aws destroy   # Clean teardown

# Service operations  
cribops-cli aws scale 0   # Scale down (maintenance)
cribops-cli aws scale 3   # Scale up (high traffic)
cribops-cli aws redeploy  # Update with new versions

# Database access
cribops-cli aws db connect              # Secure tunnel
cribops-cli aws db connect --auto-open  # Auto-launch TablePlus
```

---
## 🐳 Local Development Setup

Perfect for development, testing, and small-scale deployments.

## Getting Started Locally

```bash
# Basic local setup
cribops-cli setup

# Setup with custom ports (multiple instances)
cribops-cli setup --n8n-port 5679 --postgres-port 5433

# Setup with Cloudflare tunnel (public webhooks)
cribops-cli setup --cloudflare-tunnel --tunnel-hostname n8n.yourdomain.com
```

---
## Cloudflare Tunnel Setup

Expose your local n8n instance securely to the internet for webhooks without port forwarding!

### Prerequisites

1. **Install cloudflared:**
   - **macOS:** `brew install cloudflared`
   - **Windows:** Download from https://github.com/cloudflare/cloudflared/releases
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

---
## Database Management

CribOps CLI creates 4 pre-configured PostgreSQL databases with n8n credentials ready to use:

### Available Databases

- **n8n** - System database for workflows and credentials
- **cribops_memory** - Long-term memory storage for AI agents
- **cribops_crm** - Customer relationship management data
- **cribops_rag** - Vector storage for RAG (Retrieval Augmented Generation)

### Local Database Access (TablePlus Integration)

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

### AWS Database Access (Secure SSM Tunneling)

```bash
# Connect to AWS database with auto-open TablePlus
cribops-cli aws db connect --auto-open

# Connect on specific port
cribops-cli aws db connect --port 5433

# Connect to specific stack
cribops-cli aws db connect my-stack-name
```

### API-Based Credential Setup

```bash
# Create credentials using n8n API key
cribops-cli db setup-credentials YOUR_API_KEY
```

---
## Expected Output

### AWS Deployment Output

```
🚀 AWS Infrastructure Deployment
================================
Domain: n8n.yourdomain.com
Region: us-east-1

⚡ Deployment Progress:
✅ CloudFormation template generated: n8n-infrastructure-yourdomain-com.yaml
✅ Template validation passed
✅ Infrastructure deployment started
✅ VPC and networking created
✅ Database cluster provisioned
✅ ECS services launched
✅ Load balancer configured
✅ DNS records updated

🔐 Database Security:
  • Database deployed in private subnets (VPC only access)
  • Access via secure SSM tunneling through bastion instance
  • No public internet access to database
  • Perfect for TablePlus and other database tools

📝 Next steps:
   1. Configure your domain DNS to point to the load balancer
   2. Access your N8N instance at: https://n8n.yourdomain.com
   3. Use 'cribops-cli aws db connect' to access the database securely via SSM tunneling

✅ AWS infrastructure deployed successfully!
```

### Local Development Files

```
.
├── compose.yaml                      # Docker Compose configuration
├── .env                              # Environment variables
├── .gitignore                        # Updated with secrets/, .env, and local files
├── init-dbs.sql                      # Database initialization (creates 4 databases)
├── secrets/                          # Directory containing secrets
│   └── .postgrespassword             # PostgreSQL password file
└── (Optional with Cloudflare tunnel)
    ├── [tunnel-name]-config.yml      # Tunnel configuration
    └── .cloudflared-[tunnel-name].json  # Local tunnel credentials
```

---
## Docker Compose Commands

After generating your compose.yaml and secret files, manage your local services:

- **Run in Foreground:**
```bash
docker compose up
```
- **Run in Detached Mode:**
```bash
docker compose up -d
```
- **Stop Containers:**
```bash
docker compose stop
```
- **Take Down Containers:**
```bash
docker compose down
```
- **View Logs:**
```bash
docker compose logs -f
```

---
## Troubleshooting & Support

### AWS Deployment Issues

**Missing AWS Session Manager Plugin:**
```bash
# macOS
brew install --cask session-manager-plugin

# The CLI will provide installation instructions for other platforms
```

**Database Connection Issues:**
- Ensure the Session Manager plugin is installed
- Verify AWS credentials have appropriate permissions
- Check stack status: `cribops-cli aws status`

**Deployment Failures:**
- Review CloudFormation events in AWS Console
- Ensure domain is configured in Route 53
- Verify AWS service limits and quotas

### Local Development Issues

**Database Connection Issues:**
- Ensure Docker is running before setup
- Check PostgreSQL logs: `docker compose logs postgres`
- Verify credentials in .env file

**Cloudflare Tunnel Issues:**
- Ensure cloudflared is installed and authenticated
- Check tunnel status: `docker compose logs cloudflared`
- Verify domain is active in Cloudflare

### Getting Help

- Join our community https://skool.com/cribops
- View all options with `cribops-cli --help`
- For AWS issues, check `cribops-cli aws status`

---
## Recent Changes

### 🆕 Major New Features (v3.0.0)

- 🏗️ **Simplified CLI Structure**: Removed the `deploy` command level - AWS commands are now top-level (`cribops-cli aws init` instead of `cribops-cli deploy aws init`)
- 📊 **Real-time AWS Monitoring**: Live deployment status with `cribops-cli aws status`
- 🔒 **Secure Database Access**: Private database with SSM tunneling via `cribops-cli aws db connect`
- ⚡ **Production Operations**: Zero-downtime scaling and redeployments
- 💰 **Cost-Optimized Architecture**: Enterprise-grade infrastructure starting ~$50/month

### Previous Features (v2.31.0)

- 🏗️ Complete AWS Cloud Deployment: One-command infrastructure setup
- API-based credential setup: Create PostgreSQL credentials via n8n API
- Custom PostgreSQL port: External database access with --postgres-port
- Improved tunnel handling: Automatic DNS route refresh fixes Error 1033
- Local credential storage: Better tunnel credential portability

---

**Ready to scale to production?** Try `cribops-cli aws init` and have your enterprise N8N infrastructure running in minutes!

**Starting with local development?** Use `cribops-cli setup` for the fastest local installation.

By following this guide, you can deploy N8N anywhere from local development to production AWS infrastructure. Enjoy using Crib Ops CLI!
