
# n8n Docker Setup (Local / Self-Hosted)

This repository provides a clean, Docker-based setup for running **n8n** locally or in a self-hosted environment with **persistent storage** and **secure, environment-based configuration**.

The setup is intentionally **minimal, portable, and Git-safe**, making it suitable for local development, PoCs, and controlled internal deployments.

---

## Prerequisites

Ensure the following are installed and available on your system:

* Docker Desktop (Windows / macOS / Linux)
* Docker Compose (v2+)
* Git

> **Important:** Docker Desktop must be running before proceeding.

---

## Project Structure

```text
n8n-docker/
├── docker-compose.yml
├── .env.example     # ✅ committed (safe template)
├── .env             # ❌ ignored (contains secrets)
├── .gitignore
└── data/            # ❌ ignored (persistent volumes)
```

---

## Environment Configuration

This project uses a `.env` file to manage sensitive configuration such as encryption keys and authentication credentials.

### Key Principles

* `.env` is **intentionally excluded** from version control
* Secrets must **never** be committed to GitHub
* `.gitignore` already protects `.env` and `data/`

---

## Step 1: Create the `.env` File

From the project root:

```bash
cp .env.example .env
```

Edit the newly created `.env` file and **replace all placeholder values** with secure, real values.

---

## Step 2: Configure Environment Variables

Below is the **safe, commit-ready template** (`.env.example`).

```env
# =========================================================
# n8n Environment Configuration (Example)
# =========================================================
# This file is SAFE to commit.
# Copy this file to `.env` and replace all placeholder values.
#
# NEVER commit the real `.env` file.
# =========================================================


# ---------------------------------------------------------
# Core n8n Settings
# ---------------------------------------------------------
N8N_HOST=localhost
N8N_PORT=5678
N8N_PROTOCOL=http


# ---------------------------------------------------------
# Security & Encryption
# ---------------------------------------------------------
# Used to encrypt credentials stored by n8n.
# Generate a strong random value (32+ characters recommended).
N8N_ENCRYPTION_KEY=replace_with_a_strong_random_key


# ---------------------------------------------------------
# Authentication (Optional)
# ---------------------------------------------------------
# Enables HTTP Basic Auth before accessing the n8n UI.
# Strongly recommended for non-local or shared environments.
N8N_BASIC_AUTH_ACTIVE=false
N8N_BASIC_AUTH_USER=admin
N8N_BASIC_AUTH_PASSWORD=change_me


# ---------------------------------------------------------
# User Management
# ---------------------------------------------------------
# When true, the owner account is created via UI on first launch.
N8N_USER_MANAGEMENT_DISABLED=true


# ---------------------------------------------------------
# Execution & Runtime Behavior
# ---------------------------------------------------------
EXECUTIONS_PROCESS=main


# ---------------------------------------------------------
# Timezone (Optional)
# ---------------------------------------------------------
GENERIC_TIMEZONE=UTC
```

---

## Step 3: Start n8n

Start the container in detached mode:

```bash
docker compose up -d
```

On first startup, n8n will prompt you to create an **Owner account** in the browser.

### Access the UI

```
http://localhost:5678
```

---

## Data Persistence

All workflows, credentials, and execution data are stored using Docker volumes mapped to the `data/` directory.

### What This Means

* Stopping containers **does not** delete data
* Restarting Docker **does not** delete data
* Removing volumes **resets n8n completely**

To reset everything (destructive):

```bash
docker compose down -v
```

---

## Common Commands

### Start n8n

```bash
docker compose up -d
```

### View Logs

```bash
docker compose logs -f n8n
```

### Stop n8n

```bash
docker compose down
```

### Update n8n

```bash
docker compose pull n8n
docker compose up -d
```

---

## Security Notes (Read This Carefully)

* **Rotate `N8N_ENCRYPTION_KEY` only before production use**

  * Rotating it later will make existing credentials unreadable
* **Never expose this setup publicly** without:

  * HTTPS
  * Reverse proxy (Nginx / Traefik / Caddy)
  * Strong authentication
* If `.env` is **ever committed by mistake**:

  * Assume full compromise
  * Rotate all secrets immediately

