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

Run the playbook:

```bash
ansible-playbook setup-dev-env.yml --ask-become-pass
```

The `--ask-become-pass` flag will prompt you for your sudo password.

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
