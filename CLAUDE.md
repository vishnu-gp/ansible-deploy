# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Overview

This is an Ansible-based deployment repository for provisioning a Raspberry Pi with multiple containerized services. The main playbook configures a Pi as a multi-purpose home server running Docker containers for various services.

## Key Commands

### Deployment
- `ansible-playbook -i inventory.ini provision-dos-pi-os.yml` - Deploy all services to Raspberry Pi
- `ansible-playbook -i inventory.ini provision-dos-pi-os.yml --tags docker` - Deploy only Docker-related roles
- `ansible-playbook -i inventory.ini provision-dos-pi-os.yml --limit pis` - Run on specific host group

### Configuration
- Copy `.env.example` to `.env` and configure environment variables before deployment
- Update `inventory.ini` with correct Pi IP address and SSH details

## Architecture

### Main Playbook Structure
- `provision-dos-pi-os.yml` - Main playbook that orchestrates all role deployments
- `inventory.ini` - Ansible inventory file defining target Pi hosts
- `.env` - Environment variables file (not committed, use `.env.example` as template)

### Role-Based Architecture
The deployment is organized into modular Ansible roles under the `roles/` directory:

1. **common** - Base system setup, package installation, environment variable loading
2. **docker** - Docker CE installation and configuration for ARM64
3. **portainer** - Docker management UI container
4. **pihole** - DNS ad-blocking service with web interface
5. **nas-server** - Samba file server with optional AWS S3 sync backup
6. **coder** - VS Code server for remote development
7. **flight-feeder** - FlightRadar24 data feeder service
8. **vpn-server** - WireGuard VPN server using wg-easy

### Service Dependencies
- All containerized services depend on the `docker` role
- The `common` role must run first to install base packages and load environment variables
- Services are deployed in the order specified in the main playbook

### Environment Configuration
Services are configured via environment variables defined in `.env`:
- Pi-hole: timezone and admin password
- NAS server: Samba share name, credentials, and optional S3 sync settings
- FlightRadar24: email and sharing key for data contribution
- VPN server: external URL, ports, and admin password

### Network and Storage
- Services expose different ports (Pi-hole: 80/443, Portainer: 9000, Coder: 8443, etc.)
- NAS server creates Samba shares at `/srv/{NAS_NAME}` with guest access
- VPN server uses WireGuard protocol with web-based client management
- Optional automated S3 backup for NAS data via cron job

## Important Files
- `roles/*/tasks/main.yml` - Primary task definitions for each role
- `roles/nas-server/tasks/aws-s3-sync.yml` - S3 backup configuration
- `.env.example` - Template for required environment variables