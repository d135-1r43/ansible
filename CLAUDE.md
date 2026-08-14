# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is an Ansible playbook for provisioning development environments on remote Linux servers. It installs and configures Zsh, Oh My Zsh, Neovim (built from source with LazyVim), and Zellij terminal multiplexer.

## Common Commands

```bash
# Run playbook on all servers
ansible-playbook -i hosts.ini setup-dev-env.yml

# Update Neovim/Zellij on already-provisioned servers
ansible-playbook -i hosts.ini update-dev-tools.yml                        # Zellij updates, Neovim drift report
ansible-playbook -i hosts.ini update-dev-tools.yml -e update_neovim=true  # also rebuild Neovim
ansible-playbook -i hosts.ini update-dev-tools.yml --check                # drift report only

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

- **setup-dev-env.yml**: Provisioning playbook, organized by sections (Zsh, Oh My Zsh, dependencies, Neovim/Zellij, LazyVim)
- **update-dev-tools.yml**: Update-only playbook for already-provisioned hosts
- **tasks/update-tools.yml**: Version-aware install/update logic for Neovim and Zellij, imported by both playbooks
- **tasks/zellij-config.yml**: Deploys the Zellij keybinding overrides, imported by both playbooks
- **hosts.ini**: Inventory file with server groups (minions, other, thi, herhoffer) and a parent group `servers` that includes all
- **.gitconfig**: Git configuration deployed to target servers
- **files/zellij-config.kdl**: Partial Zellij config deployed to `~/.config/zellij/config.kdl`

## Key Details

- Targets the `servers` host group by default (includes all child groups)
- Uses `become: yes` for privilege escalation
- Neovim is built from source (stable branch) to avoid GLIBC compatibility issues
- Installed versions are compared against the GitHub releases API; the API is queried
  once per run via `delegate_to: localhost` + `run_once` to avoid rate limits
- Zellij auto-updates on version drift; Neovim only reports drift unless `update_neovim=true`
- Existing Neovim configs are backed up with timestamps before LazyVim installation
- The Zellij config is a *partial* config that merges with the built-in keymap, so it
  survives upstream keybinding changes; it binds `Alt b`/`Alt f` (what macOS xterm.js
  terminals actually send for Option+Left/Right) to tab switching, and moves
  `ToggleFloatingPanes` from `Alt f` to `Alt w`
- First run takes 5-10 minutes per server due to Neovim compilation
- Mutating tasks are gated on `not ansible_check_mode` so `--check` runs work as drift reports
