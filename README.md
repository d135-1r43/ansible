# Development Environment Setup with Ansible

This Ansible playbook automatically installs and configures:
- **Zsh** - Modern shell
- **Oh My Zsh** - Zsh configuration framework
- **Neovim** with **LazyVim** - Modern Neovim configuration
- **Zellij** - Terminal multiplexer

## Prerequisites

1. **Ansible installed** on your system:
   ```bash
   # macOS
   brew install ansible

   # Ubuntu/Debian
   sudo apt update && sudo apt install ansible

   # Fedora/RHEL
   sudo dnf install ansible
   ```

2. **Sudo privileges** (for package installation)

## Usage

### Local Installation

Run the playbook on your local machine:

```bash
ansible-playbook setup-dev-env.yml --ask-become-pass
```

The `--ask-become-pass` flag will prompt you for your sudo password.

### Remote Server Installation

To install on remote servers, you'll need to configure an inventory file.

1. **Create an inventory file** (`hosts.ini`):
   ```ini
   [dev_servers]
   server1 ansible_host=192.168.1.10 ansible_user=your_username
   server2 ansible_host=192.168.1.11 ansible_user=your_username

   [dev_servers:vars]
   ansible_python_interpreter=/usr/bin/python3
   ```

2. **Update the playbook** to target your inventory group:

   Edit `setup-dev-env.yml` and change:
   ```yaml
   hosts: localhost
   ```
   to:
   ```yaml
   hosts: dev_servers
   ```

3. **Run the playbook against remote servers**:
   ```bash
   # Using password authentication
   ansible-playbook -i hosts.ini setup-dev-env.yml --ask-become-pass -k

   # Using SSH key authentication (recommended)
   ansible-playbook -i hosts.ini setup-dev-env.yml --ask-become-pass

   # Target specific server
   ansible-playbook -i hosts.ini setup-dev-env.yml --ask-become-pass --limit server1
   ```

   **Flags explained:**
   - `-i hosts.ini` - Specifies the inventory file
   - `--ask-become-pass` - Prompts for sudo password on remote server
   - `-k` or `--ask-pass` - Prompts for SSH password (if not using SSH keys)
   - `--limit server1` - Run only on specific host(s)

4. **For multiple servers with different users**, create a more detailed inventory:
   ```ini
   [webservers]
   web1 ansible_host=10.0.0.1 ansible_user=ubuntu
   web2 ansible_host=10.0.0.2 ansible_user=admin

   [databases]
   db1 ansible_host=10.0.0.10 ansible_user=centos

   [dev_servers:children]
   webservers
   databases
   ```

### Tips for Remote Installation

- **SSH Key Setup**: For easier remote access, set up SSH key authentication:
  ```bash
  ssh-copy-id your_username@server_ip
  ```

- **Test Connectivity**: Before running the playbook, test your connection:
  ```bash
  ansible -i hosts.ini dev_servers -m ping
  ```

- **Dry Run**: Test what would be changed without making changes:
  ```bash
  ansible-playbook -i hosts.ini setup-dev-env.yml --check
  ```

## What Gets Installed

### Zsh + Oh My Zsh
- Installs Zsh shell
- Sets Zsh as your default shell
- Installs Oh My Zsh framework with default configuration
- Location: `~/.oh-my-zsh`

### Neovim + LazyVim
- Installs Neovim and required dependencies (git, curl, ripgrep, fd)
- Clones LazyVim starter configuration
- Backs up any existing Neovim configuration with timestamps
- Location: `~/.config/nvim`

### Zellij
- Installs Zellij terminal multiplexer
- Ready to use with `zellij` command

## Post-Installation Steps

1. **Log out and log back in** for shell changes to take effect

2. **Launch Neovim** to complete LazyVim setup:
   ```bash
   nvim
   ```
   LazyVim will automatically install plugins on first launch.

3. **Start using Zellij**:
   ```bash
   zellij
   ```

## Supported Platforms

- macOS (with Homebrew)
- Ubuntu/Debian
- Fedora/RHEL/CentOS

## Backup Policy

If you have existing Neovim configurations, they will be automatically backed up to:
- `~/.config/nvim.backup.[timestamp]`
- `~/.local/share/nvim.backup.[timestamp]`
- `~/.local/state/nvim.backup.[timestamp]`
- `~/.cache/nvim.backup.[timestamp]`

## Customization

To customize Oh My Zsh:
```bash
nvim ~/.zshrc
```

To customize LazyVim:
```bash
nvim ~/.config/nvim/lua/config/
```

## Troubleshooting

**Shell not changed:**
```bash
chsh -s $(which zsh)
```

**LazyVim plugins not installing:**
Run `:Lazy` inside Neovim and press `I` to install plugins manually.

**Permission issues:**
Make sure you run the playbook with `--ask-become-pass` flag.
