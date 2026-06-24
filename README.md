# ansible-macos-setup

Ansible playbook for managing a macOS machine lifecycle — not just initial setup, but ongoing configuration management. All tasks are idempotent and safe to re-run.

## Prerequisites

- macOS with [Homebrew](https://brew.sh) installed
- Ansible: `brew install ansible`
- [Task](https://taskfile.dev): `brew install go-task`

## Quick start

```bash
# Install required Ansible collections
task setup

# Create vault password file and encrypt the vault
task vault:init

# Edit variables to match your setup
task vault:edit
vim group_vars/all/vars.yml

# Run the full playbook
task run
```

## Usage

```bash
# Run everything
task run

# Run specific roles using tags
task tags -- homebrew
task tags -- git,ssh
task tags -- macos_defaults

# First-time bootstrap (installs Ansible, then runs playbook)
task bootstrap
```

## Available tags

| Tag              | Role            | Description                                |
|------------------|-----------------|--------------------------------------------|
| `homebrew`       | homebrew        | Taps, formulae, and casks                  |
| `mas`            | mas             | Mac App Store apps                         |
| `git`            | git             | Global git configuration                   |
| `ssh`            | ssh             | SSH key generation and config              |
| `shell`          | shell           | Default shell configuration                |
| `macos_defaults` | macos_defaults  | macOS system preferences                   |
| `dock`           | dock            | Dock layout and appearance                 |
| `dotfiles`       | dotfiles        | Symlink dotfiles into place                |

## Configuration

All variables are in `group_vars/all/vars.yml`. Sensitive values go in the vault-encrypted `group_vars/all/vault.yml`.

### Adding Homebrew packages

Since Homebrew packages are already installed, this playbook uses `state: present` — it won't reinstall or upgrade existing packages, just ensure they're there. Add packages to the lists in `vars.yml`:

```yaml
homebrew_formulae:
  - git
  - ripgrep
  - fzf

homebrew_casks:
  - firefox
  - visual-studio-code
```

### Vault

```bash
task vault:edit    # Edit encrypted vault
task vault:encrypt # Encrypt the vault file
task vault:decrypt # Decrypt (careful — will be in plaintext)
```

## Project structure

```txt
.
├── main.yml                  # Main playbook
├── ansible.cfg               # Ansible configuration
├── inventory/hosts.yml       # Localhost inventory
├── group_vars/all/
│   ├── vars.yml              # All variables
│   └── vault.yml             # Encrypted secrets
├── requirements.yml          # Ansible Galaxy dependencies
├── roles/
│   ├── homebrew/             # Homebrew taps, formulae, casks
│   ├── mas/                  # Mac App Store apps
│   ├── git/                  # Git global config
│   ├── ssh/                  # SSH key + config
│   ├── shell/                # Shell configuration
│   ├── macos_defaults/       # macOS system preferences
│   ├── dock/                 # Dock layout
│   └── dotfiles/             # Dotfile symlinks
└── Taskfile.yml
```
