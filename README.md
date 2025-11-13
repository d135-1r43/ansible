# Development Environment Setup with Ansible

This Ansible playbook automatically installs and configures:
- **Zsh** - Modern shell
- **Oh My Zsh** - Zsh configuration framework
- **Neovim** with **LazyVim** - Modern Neovim configuration
- **Zellij** - Terminal multiplexer

## Prerequisites

1. **Ansible installed** on your local control machine:
   ```bash
   # Ubuntu/Debian
   sudo apt update && sudo apt install ansible

   # Fedora/RHEL
   sudo dnf install ansible

   # macOS (for running Ansible locally)
   brew install ansible
   ```

2. **SSH access to target servers**
3. **Root privileges** on target servers

## Usage

### Remote Server Installation

The playbook is configured to run against remote servers defined in `hosts.ini`.

1. **Configure your inventory file** (`hosts.ini`):
   ```ini
   [servers]
   server1 ansible_host=server1.example.com ansible_user=root
   server2 ansible_host=192.168.1.10 ansible_user=root
   ```

2. **Run the playbook against all servers**:
   ```bash
   # Run on all servers
   ansible-playbook -i hosts.ini setup-dev-env.yml

   # Target specific server
   ansible-playbook -i hosts.ini setup-dev-env.yml --limit server1
   ```

   **Flags explained:**
   - `-i hosts.ini` - Specifies the inventory file
   - `--limit server1` - Run only on specific host(s)

3. **For multiple server groups**, create a more detailed inventory:
   ```ini
   [webservers]
   web1 ansible_host=10.0.0.1 ansible_user=root
   web2 ansible_host=10.0.0.2 ansible_user=root

   [databases]
   db1 ansible_host=10.0.0.10 ansible_user=root

   [servers:children]
   webservers
   databases
   ```

### Tips for Remote Installation

- **SSH Key Setup**: For easier remote access, set up SSH key authentication:
  ```bash
  ssh-copy-id root@server_ip
  ```

- **Test Connectivity**: Before running the playbook, test your connection:
  ```bash
  ansible -i hosts.ini servers -m ping
  ```

- **Dry Run**: Test what would be changed without making changes:
  ```bash
  ansible-playbook -i hosts.ini setup-dev-env.yml --check
  ```

- **Build Time**: First run will take longer (5-10 minutes per server) as Neovim is built from source. Subsequent runs will be faster.

## What Gets Installed

### Zsh + Oh My Zsh
- Installs Zsh shell
- Sets Zsh as your default shell
- Installs Oh My Zsh framework with default configuration
- Location: `~/.oh-my-zsh`

### Neovim + LazyVim
- Builds Neovim from source (stable branch) for maximum compatibility across different distributions and glibc versions
- Installs required dependencies (git, curl, ripgrep, fd-find, build tools)
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

**Neovim build takes a long time:**
Building Neovim from source can take 5-10 minutes depending on your system. This is normal and ensures compatibility with your system's libraries.

**GLIBC version errors:**
The playbook now builds Neovim from source on Linux systems, which eliminates GLIBC compatibility issues that occur with pre-built binaries.
