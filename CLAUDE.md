# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is an Ansible playbook for provisioning development environments on remote Linux servers. It installs and configures Zsh, Oh My Zsh, Neovim (built from source with LazyVim), and Zellij terminal multiplexer.

## Common Commands

```bash
# Run playbook on all servers
ansible-playbook -i hosts.ini setup-dev-env.yml

# Target specific server
ansible-playbook -i hosts.ini setup-dev-env.yml --limit minion20

# Target a group
ansible-playbook -i hosts.ini setup-dev-env.yml --limit minions

# Dry run (check mode)
ansible-playbook -i hosts.ini setup-dev-env.yml --check

# Test connectivity
ansible -i hosts.ini servers -m ping

# Run ad-hoc command on a server
ansible minion20 -i hosts.ini -m shell -a "command" -b
```

## Architecture

- **setup-dev-env.yml**: Single playbook containing all tasks, organized by sections (Zsh, Oh My Zsh, Neovim, LazyVim, Zellij)
- **hosts.ini**: Inventory file with server groups (minions, other, thi, herhoffer) and a parent group `servers` that includes all
- **.gitconfig**: Git configuration deployed to target servers

## Key Details

- Targets the `servers` host group by default (includes all child groups)
- Uses `become: yes` for privilege escalation
- Neovim is built from source (stable branch) to avoid GLIBC compatibility issues
- Build skips if `/usr/local/bin/nvim` already exists
- Existing Neovim configs are backed up with timestamps before LazyVim installation
- First run takes 5-10 minutes per server due to Neovim compilation
