---
name: ssh
description: Use when the user invokes /ssh or asks to access, deploy to, copy files to, install SSH keys on, or transfer files to a remote server via OpenSSH. Provides dependency checks, key generation, public-key installation, and safe connection templates without storing secrets.
metadata:
  short-description: OpenSSH setup and remote access
---

# SSH

## Invocation

- This skill is invoked by `/ssh`.
- Treat all text after `/ssh` as the SSH task.
- Use local OpenSSH tools only: `ssh`, `scp`, `sftp`, `ssh-keygen`.
- Never ask for, store, print, export, or decrypt passwords, private keys, Xshell/Xftp credentials, tokens, or `.codex/auth.json`.

## Required Inputs

If missing, ask only for the non-secret connection fields:

```text
HostName=<server-domain-or-ip>
User=<remote-username>
Port=22
KeyPath=~/.ssh/codex_ed25519
Alias=<optional-host-alias>
```

Do not hard-code real hostnames, IPs, usernames, ports, or private key names into this skill.

## Dependency Check

Run before SSH work:

```powershell
Get-Command ssh,scp,sftp,ssh-keygen -ErrorAction SilentlyContinue
```

If any command is missing, guide installation:

```powershell
# Windows 10/11, run PowerShell as Administrator if needed
Get-WindowsCapability -Online -Name OpenSSH.Client~~~~0.0.1.0
Add-WindowsCapability -Online -Name OpenSSH.Client~~~~0.0.1.0

# If Git for Windows is installed, OpenSSH may also exist under Git\usr\bin.
where.exe ssh
```

```bash
# macOS
xcode-select --install 2>/dev/null || true
brew install openssh

# Debian/Ubuntu
sudo apt update && sudo apt install -y openssh-client

# Fedora/RHEL
sudo dnf install -y openssh-clients

# Arch
sudo pacman -S --needed openssh
```

## Key Setup

Check whether the key exists:

```powershell
$KeyPath = "$HOME\.ssh\codex_ed25519"
Test-Path $KeyPath
```

Create a new key if missing:

```powershell
New-Item -ItemType Directory -Force "$HOME\.ssh" | Out-Null
ssh-keygen -t ed25519 -f "$HOME\.ssh\codex_ed25519" -C "codex@$env:COMPUTERNAME" -N ""
icacls "$HOME\.ssh\codex_ed25519" /inheritance:r
icacls "$HOME\.ssh\codex_ed25519" /grant:r "$($env:USERNAME):R"
```

```bash
mkdir -p ~/.ssh
chmod 700 ~/.ssh
ssh-keygen -t ed25519 -f ~/.ssh/codex_ed25519 -C "codex@$(hostname)" -N ""
chmod 600 ~/.ssh/codex_ed25519
chmod 644 ~/.ssh/codex_ed25519.pub
```

Show only the public key:

```powershell
Get-Content "$HOME\.ssh\codex_ed25519.pub"
```

## Install Public Key On Server

Use one of these non-secret options.

If temporary SSH login already works:

```powershell
$HostName = "<server-domain-or-ip>"
$User = "<remote-username>"
$Port = 22
$PubKey = "$HOME\.ssh\codex_ed25519.pub"
Get-Content $PubKey | ssh -p $Port "${User}@${HostName}" "umask 077; mkdir -p ~/.ssh; cat >> ~/.ssh/authorized_keys"
```

If the remote server is Linux and the user can run a command in its console/panel, paste this command on the server after replacing `<PUBLIC_KEY>`:

```bash
umask 077
mkdir -p ~/.ssh
printf '%s\n' '<PUBLIC_KEY>' >> ~/.ssh/authorized_keys
chmod 700 ~/.ssh
chmod 600 ~/.ssh/authorized_keys
```

If `ssh-copy-id` exists:

```bash
ssh-copy-id -i ~/.ssh/codex_ed25519.pub -p 22 <remote-username>@<server-domain-or-ip>
```

If password login is required and no existing trusted channel exists, stop and ask the user to install the public key manually through the provider console or an already trusted terminal. Do not ask Codex to handle the password.

## Connection Templates

Non-interactive access check:

```powershell
ssh -o BatchMode=yes -o StrictHostKeyChecking=accept-new -i "$HOME\.ssh\codex_ed25519" -p 22 <remote-username>@<server-domain-or-ip> "whoami; hostname; pwd"
```

Interactive login:

```powershell
ssh -i "$HOME\.ssh\codex_ed25519" -p 22 <remote-username>@<server-domain-or-ip>
```

Copy a file:

```powershell
scp -o StrictHostKeyChecking=accept-new -i "$HOME\.ssh\codex_ed25519" -P 22 ".\local-file" <remote-username>@<server-domain-or-ip>:/remote/path/
```

Create or update `~/.ssh/config`:

```text
Host <alias>
    HostName <server-domain-or-ip>
    User <remote-username>
    Port 22
    IdentityFile ~/.ssh/codex_ed25519
    IdentitiesOnly yes
```

Then test:

```powershell
ssh -o BatchMode=yes <alias> "whoami; hostname; pwd"
```

## Troubleshooting Prompts

- `Permission denied (publickey)`: verify the public key is in remote `~/.ssh/authorized_keys`, remote permissions are `700` for `.ssh` and `600` for `authorized_keys`, and the client uses the intended `-i` key.
- `Connection timed out`: check host, port, firewall, cloud security group, and whether SSH server is running.
- `Host key verification failed`: inspect the changed host key with the user before removing `known_hosts` entries.
- `Bad permissions`: fix local private key permissions; never loosen permissions broadly.
- `ssh: command not found`: run the dependency installation section.
