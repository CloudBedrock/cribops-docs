# Crib Ops CLI Tool Documentation

Welcome to the Crib Ops CLI tool! Deploy N8N locally with Docker or scale to production-ready AWS infrastructure with a single command.

Need more help? Join our new community https://skool.com/cribops

## N8N Easy Install & AWS Cloud Deployment

[![Watch the video](https://img.youtube.com/vi/OEyuTy4KauA/hqdefault.jpg)](https://youtu.be/OEyuTy4KauA?si=F2J7pT4n8iiU0efZ)

## Downloads

For users of different operating systems, we provide specific builds of CribOps CLI. Choose the one that matches your system:

- **MacOS Version**
  - https://github.com/cloudbedrock/cribops-docs/raw/main/downloads/cribops-cli-mac-3.7.6.zip
- **Windows Version**
  - https://github.com/cloudbedrock/cribops-docs/raw/main/downloads/cribops-cli-windows-3.7.6.zip

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
- **🆕 RDS Database Setup**: Automated creation of memory/CRM/RAG databases with pgvector support
- **🆕 N8N Credential Integration**: Automatic PostgreSQL credential setup in n8n for all databases
- **🆕 Smart DNS Management**: Automatic hosted zone detection with manual DNS setup guidance
- **🆕 Accurate ALB Information**: Real-time load balancer DNS name retrieval for CNAME setup
- Cost-optimized architecture starting from ~$112/month *OnDemand - Significant savings using reserved pricing from AWS (Note No Need for Postgres Database or Vector as included)

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
- **AWS Session Manager Plugin**: For secure database access (auto-installation guide provided)
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

# Check deployment status with DNS configuration info
cribops-cli aws status

# Connect to database securely
cribops-cli aws db connect
```

### 🆕 NEW: Automated Database Setup with pgvector

Transform your RDS Aurora PostgreSQL into a complete workflow powerhouse:

```bash
# Setup all databases and n8n credentials automatically
cribops-cli aws db setup-credentials https://n8n.yourdomain.com

# With specific stack and region
cribops-cli aws db setup-credentials --stack-name my-n8n --region us-west-2
```

**What this creates:**
- **cribops_memory**: Caching, sessions, and temporary data storage
- **cribops_crm**: Empty database for custom CRM schema (you design it!)
- **cribops_rag**: Vector database with pgvector extension for AI/RAG operations
- **N8N Credentials**: Automatically creates "CribOps - [Database]" credentials in n8n

**Perfect for:**
- AI agent memory and context retention
- Custom CRM implementations
- Vector search and embeddings
- RAG (Retrieval Augmented Generation) workflows
- Multi-database n8n workflows

### 🆕 NEW: Smart DNS Management

The CLI now provides intelligent DNS management with multiple options:

#### **Automatic Route 53 Setup (Recommended)**
If you have a Route 53 hosted zone:
```bash
# Automatic detection and DNS record creation
cribops-cli aws init --domain n8n.example.com
# CLI detects hosted zone automatically and creates DNS records
```

#### **Multiple Hosted Zone Selection**
When multiple hosted zones are found:
```
🌐 DNS Configuration Options
============================
Multiple hosted zones found for your domain:

📋 Available Hosted Zones:
  1. example.com (Z1D633PJN98FT9) - 🔧 Active, 4 records
  2. dev.example.com (Z3P5QSUBK4POTI) - 🔧 Active, 2 records

Please select a hosted zone for DNS configuration:
Enter your choice (1-2): 1

✅ Selected: example.com (Z1D633PJN98FT9)
🎯 Creating DNS alias record for n8n.example.com...
✅ DNS record created successfully!
```

#### **External DNS Provider Setup**
For Cloudflare, Namecheap, GoDaddy, etc.:
```
📋 DNS Record Configuration:
   Record Type: CNAME
   Name/Host: n8n.example.com
   Value/Target: n8n-in-LoadBa-ABC123DEF456-789012345.us-east-1.elb.amazonaws.com
   TTL: 300 (5 minutes)

🌐 Provider-Specific Instructions:

📊 Cloudflare Setup:
   1. Log into Cloudflare dashboard
   2. Select your domain: example.com
   3. Go to DNS → Records
   4. Click 'Add record'
   5. Select 'CNAME' as record type
   6. Name: n8n (for n8n.example.com)
   7. Target: n8n-in-LoadBa-ABC123DEF456-789012345.us-east-1.elb.amazonaws.com
   8. Proxy status: DNS only (gray cloud)
   9. Save record

🔧 Namecheap Setup:
   1. Log into Namecheap account
   2. Go to Domain List → Manage
   3. Advanced DNS tab
   4. Add new record
   5. Type: CNAME Record
   6. Host: n8n
   7. Value: n8n-in-LoadBa-ABC123DEF456-789012345.us-east-1.elb.amazonaws.com
   8. Save changes
```

### 🏗️ What Gets Deployed

**Complete AWS Infrastructure:**
- VPC with public/private subnets across availability zones
- Application Load Balancer with SSL termination
- ECS Fargate auto-scaling container service
- Aurora PostgreSQL with automated backups and encryption
- Route 53 DNS management with your custom domain (or manual DNS instructions)
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
# View complete deployment status with DNS information
cribops-cli aws status
# Shows: Stack info, resource counts, service health, URLs, and CNAME setup details

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

Starting from approximately $112-179/month for production workloads:
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

# View connection details with passwords
cribops-cli aws db connect --show-password

# Support for multiple GUI clients
# Works with: TablePlus, Navicat, pgAdmin, DBeaver
```

**Features:**
- Private database (no internet access)
- SSM tunneling through bastion host
- Works with TablePlus, pgAdmin, Navicat, DBeaver, or any PostgreSQL client
- Automatic credential retrieval from AWS Secrets Manager
- Session Manager plugin auto-installation guide
- Connection string generation for JDBC and other tools

### 🎯 Production Operations

```bash
# Infrastructure management
cribops-cli aws init      # Create infrastructure
cribops-cli aws status    # Monitor deployment with DNS info
cribops-cli aws destroy   # Clean teardown

# Service operations  
cribops-cli aws scale 0   # Scale down (maintenance)
cribops-cli aws scale 3   # Scale up (high traffic)
cribops-cli aws redeploy  # Update with new versions

# Database operations
cribops-cli aws db connect                          # Secure tunnel
cribops-cli aws db connect --auto-open              # Auto-launch TablePlus
cribops-cli aws db setup-credentials                # Create databases + n8n creds
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
- **cribops_memory** - Long-term memory storage for AI agents and caching
- **cribops_crm** - Customer relationship management data (empty, design your schema!)
- **cribops_rag** - Vector storage for RAG (Retrieval Augmented Generation) with pgvector

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

# 🆕 Setup complete database environment
cribops-cli aws db setup-credentials https://n8n.yourdomain.com
```

### API-Based Credential Setup

```bash
# Local development
cribops-cli db setup-credentials YOUR_API_KEY

# AWS production (creates memory/CRM/RAG databases + credentials)
cribops-cli aws db setup-credentials https://n8n.yourdomain.com YOUR_API_KEY
```

---
## Expected Output

### 🆕 AWS Status Output with DNS Configuration

```
📊 AWS Deployment Status
========================
✅ Status retrieved

🏗️  CloudFormation Stack:
  Name: n8n-infrastructure-n8n-example-com
  Status: CREATE_COMPLETE
  Region: us-east-1
  Created: 2025-08-06T03:45:56Z
  Last Updated: Never
  Resource Count: 38

📋 Resource Summary:
  VPCs: 1
  Subnets: 4
  Security Groups: 5
  Load Balancers: 1
  ECS Clusters: 1
  ECS Services: 1
  RDS Instances: 2
  Route 53 Zones: 1

🔧 N8N Instances (1):
  ✅ N8N Main: Running
    URL: https://n8n.example.com

🔗 DNS Configuration:
  📋 For CNAME record setup:
    Record Type: CNAME
    Name: n8n.example.com (or subdomain)
    Value: n8n-in-LoadBa-ABC123DEF456-789012345.us-east-1.elb.amazonaws.com
    TTL: 300 (5 minutes)

💡 Tips:
  • Use 'cribops-cli deploy aws add-node' to add more instances
  • Check CloudWatch logs for detailed service information
  • Update DNS records if domains are not resolving
```

### 🆕 AWS Database Setup Output

```
🚀 Setting up AWS RDS databases and n8n credentials...
📍 Finding AWS resources...
   Using stack: n8n-infrastructure-n8n-example-com
🔐 Getting database credentials...
🗃️  Creating additional databases...
   Creating database: cribops_memory
   ✅ Created database: cribops_memory
   Creating database: cribops_crm
   ✅ Created database: cribops_crm
   Creating database: cribops_rag
   🔧 Enabling pgvector extension for cribops_rag...
   ✅ pgvector enabled for cribops_rag
🔑 Setting up n8n credentials...
   🔄 Rotating existing CribOps credentials...
   💡 No existing CribOps credentials found to rotate
   Setting up credential: CribOps - N8N
   ✅ Created credential: CribOps - N8N
   Setting up credential: CribOps - Memory
   ✅ Created credential: CribOps - Memory
   Setting up credential: CribOps - CRM
   ✅ Created credential: CribOps - CRM
   Setting up credential: CribOps - RAG
   ✅ Created credential: CribOps - RAG

✅ AWS RDS database setup completed successfully!

📋 Created databases:
   • n8n (system database)
   • cribops_memory (caching/sessions)  
   • cribops_crm (empty, for custom schema)
   • cribops_rag (vector database with pgvector)

🔑 N8N credentials configured:
   • CribOps - N8N
   • CribOps - Memory
   • CribOps - CRM
   • CribOps - RAG

💡 You can now use these PostgreSQL credentials in your n8n workflows!
```

### AWS Deployment Output with DNS Options

```
🚀 AWS Infrastructure Deployment
================================
Domain: n8n.example.com
Region: us-east-1

⚡ Deployment Progress:
✅ CloudFormation template generated: n8n-infrastructure-n8n-example-com.yaml
✅ Template validation passed
✅ Infrastructure deployment started
✅ VPC and networking created
✅ Database cluster provisioned
✅ ECS services launched
✅ Load balancer configured
✅ SSL certificate validated

🌐 DNS Configuration Options
============================
Multiple hosted zones found for your domain:

📋 Available Hosted Zones:
  1. example.com (Z1D633PJN98FT9) - 🔧 Active, 4 records
  2. dev.example.com (Z3P5QSUBK4POTI) - 🔧 Active, 2 records

Please select a hosted zone for DNS configuration:
Enter your choice (1-2): 1

✅ Selected: example.com (Z1D633PJN98FT9)
🎯 Creating DNS alias record for n8n.example.com...
✅ DNS record created successfully!

🔐 Database Security:
  • Database deployed in private subnets (VPC only access)
  • Access via secure SSM tunneling through bastion instance
  • No public internet access to database
  • Perfect for TablePlus and other database tools

📝 Next steps:
   1. Access your N8N instance at: https://n8n.example.com
   2. Use 'cribops-cli aws db connect' to access the database securely via SSM tunneling
   3. Use 'cribops-cli aws db setup-credentials' to create memory/CRM/RAG databases

✅ AWS infrastructure deployed successfully!
```

### External DNS Provider Instructions Output

```
🌐 External DNS Provider Setup Required
=======================================
No Route 53 hosted zone found for example.com
Please configure DNS manually with your provider.

📋 DNS Record Configuration:
   Record Type: CNAME
   Name/Host: n8n.example.com
   Value/Target: n8n-in-LoadBa-ABC123DEF456-789012345.us-east-1.elb.amazonaws.com
   TTL: 300 (5 minutes)

🌐 Provider-Specific Instructions:

📊 Cloudflare Setup:
   1. Log into Cloudflare dashboard
   2. Select your domain: example.com
   3. Go to DNS → Records
   4. Click 'Add record'
   5. Select 'CNAME' as record type
   6. Name: n8n (for n8n.example.com)
   7. Target: n8n-in-LoadBa-ABC123DEF456-789012345.us-east-1.elb.amazonaws.com
   8. Proxy status: DNS only (gray cloud)
   9. Save record

🔧 Namecheap Setup:
   1. Log into Namecheap account
   2. Go to Domain List → Manage
   3. Advanced DNS tab
   4. Add new record
   5. Type: CNAME Record
   6. Host: n8n
   7. Value: n8n-in-LoadBa-ABC123DEF456-789012345.us-east-1.elb.amazonaws.com
   8. Save changes

🌍 GoDaddy Setup:
   1. Log into GoDaddy account
   2. Go to My Products → DNS
   3. Add new record
   4. Type: CNAME
   5. Name: n8n
   6. Value: n8n-in-LoadBa-ABC123DEF456-789012345.us-east-1.elb.amazonaws.com
   7. Save record

💡 DNS propagation typically takes 1-5 minutes but can take up to 24 hours.
💡 You can check propagation status with: dig n8n.example.com
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
- Try different local port: `cribops-cli aws db connect --port 5433`

**Database Setup Issues:**
- Ensure n8n is accessible at the provided URL
- Verify n8n API key has admin permissions
- Check n8n logs for credential creation errors
- Use `cribops-cli aws db setup-credentials --help` for examples

**DNS Resolution Issues:**
- Verify DNS records are configured correctly using `cribops-cli aws status`
- Check DNS propagation: `dig your-domain.com`
- For external DNS providers, ensure CNAME record points to exact ALB DNS name shown in status
- Wait 1-5 minutes for DNS propagation (up to 24 hours for some providers)

**Deployment Failures:**
- Review CloudFormation events in AWS Console
- Ensure domain is configured in Route 53 (or manual DNS setup completed)
- Verify AWS service limits and quotas
- Check certificate validation status in ACM console

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

### 🆕 Major New Features (v3.2.1)

- 🗃️ **AWS RDS Database Setup**: Automated creation of memory, CRM, and RAG databases in Aurora PostgreSQL
- 🧠 **pgvector Support**: Automatic pgvector extension enablement for AI/vector operations in RAG database  
- 🔑 **N8N Credential Integration**: Automatic PostgreSQL credential creation in n8n for all databases
- 🔄 **Credential Rotation**: Smart detection and rotation of existing CribOps credentials
- 🛡️ **SSL Certificate Handling**: Proper RDS SSL connection handling with allowUnauthorizedCerts
- 💾 **API Key Storage**: Encrypted local storage of n8n API keys for future use
- 📊 **Multi-Client Support**: Enhanced database connection info for Navicat, pgAdmin, DBeaver, TablePlus
- 🌐 **Smart DNS Management**: Automatic hosted zone detection with provider-specific setup instructions
- 🎯 **Accurate ALB Information**: Real-time load balancer DNS name retrieval for precise CNAME setup
- 📋 **Enhanced Status Display**: Deployment status now shows correct DNS configuration details

### Previous Features (v3.0.0)

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

**Ready for AI workflows?** Use `cribops-cli aws db setup-credentials` to add memory, CRM, and vector databases with pgvector!

**Need help with DNS?** The CLI now provides step-by-step instructions for all major DNS providers and automatically detects the correct load balancer information.

**Starting with local development?** Use `cribops-cli setup` for the fastest local installation.

By following this guide, you can deploy N8N anywhere from local development to production AWS infrastructure with complete database management and intelligent DNS setup. Enjoy using Crib Ops CLI!
