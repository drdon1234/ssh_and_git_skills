---
name: git
description: Use when the user invokes /git or asks Codex to run local Git operations, configure Git, authenticate Git remotes, or troubleshoot missing Git dependencies. Provides stable git -C usage and safe setup guidance.
metadata:
  short-description: Local Git workflow and setup
---

# Git

## Invocation

- This skill is invoked by `/git`.
- Treat all text after `/git` as the requested Git task.
- Use the local `git` executable on `PATH`; do not invent wrapper commands.

## Dependency Check

Run first if Git availability is uncertain:

```powershell
Get-Command git -ErrorAction SilentlyContinue
git --version
```

If Git is missing, guide installation:

```powershell
# Windows
winget install --id Git.Git -e
# Then open a new terminal/Codex session and verify:
git --version
```

```bash
# macOS
xcode-select --install 2>/dev/null || true
brew install git

# Debian/Ubuntu
sudo apt update && sudo apt install -y git

# Fedora/RHEL
sudo dnf install -y git

# Arch
sudo pacman -S --needed git
```

## Stable Call Pattern

Always prefer:

```powershell
git -C "<repo-or-worktree-path>" <subcommand> <args>
```

Why: it does not depend on shell `cd`, works from Codex scratch folders, and makes each command auditable.

## Repository Detection

1. If the user gives a repo path, use that path.
2. Otherwise try the current workspace:

```powershell
git -C "<cwd>" rev-parse --show-toplevel
```

3. If that fails, ask for the repo path or use an obvious path already present in the conversation. Do not keep guessing through random directories.
4. Once known:

```powershell
$repo = "<absolute-repo-root>"
git -C $repo status --porcelain=v1 --branch
```

## Identity Setup

Check:

```powershell
git config --global --get user.name
git config --global --get user.email
```

Set if missing, using user-provided values:

```powershell
git config --global user.name "<Your Name>"
git config --global user.email "<you@example.com>"
git config --global init.defaultBranch main
```

## Authentication Setup

Inspect remotes without exposing secrets:

```powershell
git -C $repo remote -v
```

For HTTPS remotes on Windows, prefer Git Credential Manager:

```powershell
git credential-manager version
git config --global credential.helper manager
```

If Git Credential Manager is missing, reinstall Git for Windows with credential manager enabled.

For GitHub CLI if installed:

```powershell
gh auth status
gh auth login
```

For SSH remotes, generate a Git key if needed:

```powershell
Get-Command ssh,ssh-keygen -ErrorAction SilentlyContinue
New-Item -ItemType Directory -Force "$HOME\.ssh" | Out-Null
ssh-keygen -t ed25519 -f "$HOME\.ssh\git_ed25519" -C "<you@example.com>" -N ""
Get-Content "$HOME\.ssh\git_ed25519.pub"
```

If `ssh` or `ssh-keygen` is missing, use the `/ssh` skill dependency installation section first.

Ask the user to add the public key to their Git provider account, then test:

```powershell
ssh -T git@github.com
ssh -T git@gitlab.com
```

Do not ask for or store personal access tokens. If a token is required, tell the user to enter it only into the provider's official CLI/browser/credential prompt.

## Default Inspection Commands

```powershell
git -C $repo status --porcelain=v1 --branch
git -C $repo branch --show-current
git -C $repo log --oneline -5
git -C $repo diff --stat
```

If `git log` fails because the repository has no commits yet, treat that as normal for a new repository.

## Safe Write Rules

- Before any write operation, run `git -C $repo status --porcelain=v1 --branch`.
- Never use `git reset --hard`, `git clean -fd`, `git checkout -- <path>`, `git restore <path>`, or force push unless the user explicitly asks for that exact destructive operation.
- Do not revert, discard, or overwrite user changes.
- Stage only intended files:

```powershell
git -C $repo add -- "<path1>" "<path2>"
git -C $repo commit -m "<message>"
```

## Common Tasks

```powershell
git -C $repo switch -c "<branch-name>"
git -C $repo switch "<branch-name>"
git -C $repo pull --ff-only
git -C $repo push -u origin HEAD
```

## Troubleshooting Prompts

- `git: command not found`: run the dependency installation section and restart the terminal/Codex.
- `not a git repository`: ask for the repo path; do not scan random directories.
- `Author identity unknown`: run the identity setup section.
- HTTPS auth failed: use Git Credential Manager or the provider CLI; do not collect tokens in chat.
- SSH auth failed: verify the public key is added to the Git provider, the remote URL uses SSH, and `ssh -T git@<provider>` works.
- Push rejected: fetch first, inspect divergence, and avoid force push unless explicitly requested.
