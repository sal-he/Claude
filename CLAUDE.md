# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Repository Purpose

This repository contains automation scripts and slash commands for managing remote servers via SSH. The primary focus is server maintenance automation for Raspberry Pi servers and production web servers.

## Architecture

### Custom Slash Commands

The repository uses Claude Code's slash command system (`.claude/commands/`) to provide automation workflows:

- `/update-pis` - Sequential and parallel update workflow for three Pi servers
- `/update-salcomputersupport` - Single server update workflow for production web server

Each slash command corresponds to a shell script in the repository root with the same name.

### Server Management Pattern

The scripts follow a consistent pattern:
1. SSH into remote server(s)
2. Execute `apt-updater` function that:
   - Updates package lists (`apt-get update`)
   - Performs dist-upgrade
   - Cleans up packages
   - Reboots the server
3. Monitor server availability using ping loops
4. Confirm when servers are back online

### Server Inventory

**Raspberry Pi Servers (Local Network):**
- `zabbix` - 192.168.86.166 (updated first, sequentially)
- `raspberrypi` - 192.168.86.36 (updated in parallel with ubuntu-server)
- `ubuntu-server` - 192.168.86.25 (updated in parallel with raspberrypi)

**Production Server:**
- `salcomputersupport.com` - External web server

### Update Sequencing Strategy

The Pi update workflow uses a specific sequencing strategy:
1. Update `zabbix` first and wait for it to come back online
2. Only after `zabbix` is confirmed online, update the other two Pi servers in parallel
3. This ensures the monitoring server (zabbix) is available to track the other updates

## Running Commands

Execute server updates using slash commands:
```
/update-pis
/update-salcomputersupport
```

Or run scripts directly:
```bash
./update-pis.sh
./update-salcomputersupport.sh
```

## SSH Configuration

All scripts assume:
- SSH key-based authentication is configured for user `sal`
- Passwordless sudo is configured on remote servers for `apt-get` commands
- SSH connections use default ports
