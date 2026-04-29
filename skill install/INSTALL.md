# Codex Skill 安装说明

本压缩包包含两个已脱敏的 Codex 本地 skill：

- `git`：通过 `/git` 触发，使用本机 Git，包含缺依赖安装提示、身份配置、HTTPS/SSH 登录诊断和安全 Git 操作模板。
- `ssh`：通过 `/ssh` 触发，使用本机 OpenSSH，包含缺依赖安装提示、密钥生成、公钥安装、远程登录和文件传输模板。

包内不包含服务器 IP、域名、用户名、密码、token、SSH 私钥、`.codex/auth.json` 或 Xshell/Xftp 凭据。

## 目录结构

```text
skill install/
├─ INSTALL.md
└─ skills/
   ├─ git/
   │  └─ SKILL.md
   └─ ssh/
      └─ SKILL.md
```

## 安装到 Windows Codex

1. 关闭正在运行的 Codex。
2. 解压压缩包。
3. 在 PowerShell 中执行：

```powershell
$src = "解压目录\skills"
$dst = "$env:USERPROFILE\.codex\skills"
New-Item -ItemType Directory -Force -Path $dst | Out-Null
Copy-Item -Recurse -Force "$src\git" "$dst\git"
Copy-Item -Recurse -Force "$src\ssh" "$dst\ssh"
```

示例：

```powershell
$src = "C:\Users\YourName\Desktop\skill install\skills"
$dst = "$env:USERPROFILE\.codex\skills"
New-Item -ItemType Directory -Force -Path $dst | Out-Null
Copy-Item -Recurse -Force "$src\git" "$dst\git"
Copy-Item -Recurse -Force "$src\ssh" "$dst\ssh"
```

4. 重新打开 Codex。

## 安装到 macOS 或 Linux Codex

```bash
mkdir -p ~/.codex/skills
cp -R "解压目录/skills/git" ~/.codex/skills/git
cp -R "解压目录/skills/ssh" ~/.codex/skills/ssh
```

然后重新打开 Codex。

## 验证

新开 Codex 会话后输入：

```text
/git 查看当前仓库状态
```

或：

```text
/ssh 帮我检查 OpenSSH 是否可用，并生成一把新的部署公钥
```

## 依赖

`git` skill 会在 Git 不存在时提示安装：

- Windows: `winget install --id Git.Git -e`
- macOS: Xcode Command Line Tools 或 Homebrew Git
- Linux: `apt`、`dnf`、`pacman`

`ssh` skill 会在 OpenSSH 不存在时提示安装：

- Windows: `Add-WindowsCapability -Online -Name OpenSSH.Client~~~~0.0.1.0`
- macOS: 系统 OpenSSH 或 Homebrew OpenSSH
- Linux: `openssh-client` / `openssh-clients` / `openssh`

## 安全注意事项

- 不要把 SSH 私钥、密码、token、`.codex/auth.json` 放进 skill 包。
- SSH skill 只处理公钥、命令模板和本机 OpenSSH 调用。
- 如果远程服务器需要密码，Codex 不应接收密码；请通过云厂商控制台、服务器面板或你自己打开的受信终端安装公钥。
- Git token 只应输入到 Git Credential Manager、GitHub CLI、浏览器或 Git 提供商官方登录流程中。
