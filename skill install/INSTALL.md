# Git / SSH Agent Skills 安装指南

核验日期：2026-04-29

本包提供两个已脱敏的 `SKILL.md` Agent Skills：

- `git`：本机 Git 调用、依赖安装提示、身份配置、远程登录诊断、安全 Git 操作模板。
- `ssh`：本机 OpenSSH 调用、依赖安装提示、密钥生成、公钥安装、远程登录和文件传输模板。

包内不包含服务器 IP、域名、用户名、密码、token、SSH 私钥、`.codex/auth.json`、IDE 登录文件或 Xshell/Xftp 凭据。

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

## 安装原则

优先使用 IDE/Agent 的原生 `SKILL.md` 目录。只有工具不支持 Agent Skills 时，才降级为 Rules / Memory / Custom Instructions。

如果不确定工具偏好，优先安装到跨工具目录：

```text
.agents/skills/git/SKILL.md
.agents/skills/ssh/SKILL.md
```

或用户级：

```text
~/.agents/skills/git/SKILL.md
~/.agents/skills/ssh/SKILL.md
```

## 通用安装命令

把 `解压目录` 替换为你实际解压后的路径。

Windows PowerShell：

```powershell
$src = "解压目录\skills"
$dst = "$env:USERPROFILE\.agents\skills"
New-Item -ItemType Directory -Force -Path $dst | Out-Null
Copy-Item -Recurse -Force "$src\git" "$dst\git"
Copy-Item -Recurse -Force "$src\ssh" "$dst\ssh"
```

macOS / Linux：

```bash
mkdir -p ~/.agents/skills
cp -R "解压目录/skills/git" ~/.agents/skills/git
cp -R "解压目录/skills/ssh" ~/.agents/skills/ssh
```

项目级安装时，把目标目录换成项目内目录，例如 `.agents/skills`、`.claude/skills`、`.cursor/skills`。

## 支持矩阵

| 工具  | 状态  | 推荐路径 | 备注  |
| --- | --- | --- | --- |
| Codex | 原生支持 | `~/.codex/skills/` | 重启 Codex 后使用 `/git`、`/ssh` |
| Claude Code | 原生支持 | `~/.claude/skills/`、`.claude/skills/` | 新建 skill 目录后建议重启 |
| Cursor | 原生支持 | `.agents/skills/`、`.cursor/skills/`、`~/.agents/skills/`、`~/.cursor/skills/` | Settings -> Rules 可查看，聊天框 `/` 可搜索 |
| Google Antigravity | 支持 Agent Skills，路径随版本可能变化 | `.agents/skills/`、`~/.agents/skills/`、`~/.gemini/antigravity/skills/` | 若未自动触发，在提示中显式要求使用 git/ssh skill |
| Windsurf Cascade | 原生支持 | `.windsurf/skills/`、`~/.codeium/windsurf/skills/`、`.agents/skills/`、`~/.agents/skills/` | 手动调用常用 `@git`、`@ssh` |
| Cline | 原生支持 | `.cline/skills/`、`~/.cline/skills/`、`.clinerules/skills/`、`.claude/skills/` | 先启用 Settings -> Features -> Enable Skills |
| Kiro | 原生支持 | `.kiro/skills/`、`~/.kiro/skills/` | 可在 Agent Steering & Skills 中导入 |
| VS Code / GitHub Copilot | 原生支持 | `.github/skills/`、`.agents/skills/`、`.claude/skills/`、`~/.copilot/skills/`、`~/.agents/skills/` | 可用 `chat.agentSkillsLocations` 添加路径 |
| Gemini CLI | 原生支持 | `.gemini/skills/`、`~/.gemini/skills/`、`.agents/skills/`、`~/.agents/skills/` | 可用 `/skills list`、`/skills reload` |
| OpenCode | 原生支持 | `.opencode/skills/`、`~/.config/opencode/skills/`、`.agents/skills/`、`.claude/skills/` | 多目录兼容 |
| Amp Code | 原生支持 | `amp skill add <skill-source>` 或本地 skill 目录 | 当前版本若不识别 `.agents/skills`，按 Amp 文档目录放置 |
| Warp | 原生支持 | `.agents/skills/` 或 Warp 当前版本配置的 skills 目录 | 在 Warp Agent 中自然语言触发或显式提到 skill |
| Kimi Code CLI | 原生支持 | `.kimi/skills/`、`~/.kimi/skills/` | 安装后用 CLI 的 skills 命令确认 |
| Factory Droid | 原生支持 | `.factory/skills/` | `name: git`、`name: ssh` 对应 `/git`、`/ssh` |
| Goose | 原生支持 | `.agents/skills/`、`~/.agents/skills/`、`.goose/skills/`、`.claude/skills/` | 可用 `/skills` 查看或加载 |
| Replit Agent | 原生支持 | `.agents/skills/` | 也可通过 Workspace 的 Skills pane 上传 |
| Qoder | 原生支持 | `.qoder/skills/`、`~/.qoder/skills/` | 重启后在对话框输入 `/` 查看 |
| JetBrains AI Assistant / Air | 原生支持 | Skills 管理器导入，或 `.agents/skills/` 等本地目录 | Settings -> Tools -> AI Assistant -> Skills |
| Continue | 未核验到原生 `SKILL.md` | `.continue/rules/`、`~/.continue/rules/` | 使用降级方案 |
| Roo Code | 未核验到稳定官方 `SKILL.md` 路径 | 降级方案 | 如使用旧版/分支，按其 Rules/Custom Instructions |
| Trae / Zed / Sourcegraph Cody / Bolt / Lovable / Firebase Studio | 未核验到稳定官方 `SKILL.md` 路径 | 先试 `.agents/skills/`，否则降级 | 部分工具支持项目规则或上传文档 |
| Aider / 通用网页模型 | 未核验到原生 `SKILL.md` | 降级方案 | 粘贴或引用合并后的规则文档 |

## 常用路径速查

```text
Codex:                    ~/.codex/skills/
Claude Code:              ~/.claude/skills/ 或 .claude/skills/
Cursor:                   ~/.cursor/skills/ 或 .cursor/skills/
Windsurf:                 ~/.codeium/windsurf/skills/ 或 .windsurf/skills/
Cline:                    ~/.cline/skills/ 或 .cline/skills/
Kiro:                     ~/.kiro/skills/ 或 .kiro/skills/
VS Code / GitHub Copilot: ~/.copilot/skills/ 或 .github/skills/
Gemini CLI:               ~/.gemini/skills/ 或 .gemini/skills/
OpenCode:                 ~/.config/opencode/skills/ 或 .opencode/skills/
Kimi Code CLI:            ~/.kimi/skills/ 或 .kimi/skills/
Factory Droid:            .factory/skills/
Goose / Replit / 通用:     ~/.agents/skills/ 或 .agents/skills/
Qoder:                    ~/.qoder/skills/ 或 .qoder/skills/
```

每个目标目录下最终都应是：

```text
<目标目录>/git/SKILL.md
<目标目录>/ssh/SKILL.md
```

## 降级方案

用于 Continue、Roo Code、Aider、网页模型，或任何不识别 `SKILL.md` 的工具。

1. 新建 `AI-GIT-SSH-RULES.md`。
2. 将 `skills/git/SKILL.md` 和 `skills/ssh/SKILL.md` 的内容合并进去。
3. 在 IDE 的 Rules / Memory / Custom Instructions 中加入：

```text
当我输入 /git 时，请按 AI-GIT-SSH-RULES.md 中的 Git 规则执行。
当我输入 /ssh 时，请按 AI-GIT-SSH-RULES.md 中的 SSH 规则执行。
不要索要、保存或打印密码、token、SSH 私钥或任何凭据。
```

如果工具支持 rules 文件，可直接复制：

```bash
mkdir -p .continue/rules
cp "解压目录/skills/git/SKILL.md" .continue/rules/git-skill.md
cp "解压目录/skills/ssh/SKILL.md" .continue/rules/ssh-skill.md
```

## 验证提示词

安装后新开会话，分别测试：

```text
/git 检查 Git 是否可用；如果不可用，给我当前系统的安装建议。
```

```text
/git 查看当前仓库状态；如果当前目录不是仓库，不要随机搜索，先询问仓库路径。
```

```text
/ssh 检查 OpenSSH 是否可用；如果不可用，给我当前系统的安装建议。
```

```text
/ssh 生成一把新的 ed25519 公钥，并告诉我如何把公钥写入远程服务器 authorized_keys。
```

## 依赖提示

`git` skill 会在 Git 不存在时提示安装：

- Windows: `winget install --id Git.Git -e`
- macOS: Xcode Command Line Tools 或 Homebrew Git
- Linux: `apt`、`dnf`、`pacman`

`ssh` skill 会在 OpenSSH 不存在时提示安装：

- Windows: `Add-WindowsCapability -Online -Name OpenSSH.Client~~~~0.0.1.0`
- macOS: 系统 OpenSSH 或 Homebrew OpenSSH
- Linux: `openssh-client` / `openssh-clients` / `openssh`

## 安全要求

- 不要把 SSH 私钥、密码、token、`.codex/auth.json`、IDE 登录文件或浏览器 cookie 放进 skill 包。
- SSH skill 只处理公钥、命令模板和本机 OpenSSH 调用。
- 如果远程服务器需要密码，AI 不应接收密码；请通过云厂商控制台、服务器面板或你自己打开的受信终端安装公钥。
- Git token 只应输入到 Git Credential Manager、GitHub CLI、浏览器或 Git 提供商官方登录流程中。
- 对不支持沙箱或权限确认的 IDE，执行命令前先让模型展示命令并解释影响。

## 官方文档

- Claude Code Skills: https://docs.claude.com/en/docs/claude-code/skills
- Google Antigravity Skills: https://antigravity.google/docs/skills
- Cursor Agent Skills: https://cursor.com/docs/context/skills
- Windsurf Cascade Skills: https://docs.windsurf.com/windsurf/cascade/skills
- Cline Skills: https://docs.cline.bot/customization/skills
- Kiro Agent Skills: https://kiro.dev/docs/skills/
- VS Code / GitHub Copilot Agent Skills: https://code.visualstudio.com/docs/copilot/customization/agent-skills
- Gemini CLI Agent Skills: https://geminicli.com/docs/cli/skills/
- OpenCode Agent Skills: https://opencode.ai/docs/skills
- Goose Using Skills: https://goose-docs.ai/docs/guides/context-engineering/using-skills/
- Amp Code Agent Skills: https://ampcode.com/manual#agent-skills
- Warp Skills: https://docs.warp.dev/agent-platform/agent/skills
- Kimi Code CLI Skills: https://www.kimi.com/code/docs/en/kimi-code-cli/customization/skills.html
- Factory Droid Skills: https://docs.factory.ai/cli/configuration/skills
- Replit Agent Skills: https://docs.replit.com/replitai/skills
- Qoder Skills: https://docs.qoder.com/extensions/skills
- JetBrains AI Assistant Skills: https://www.jetbrains.com/help/ai-assistant/agent-skills.html
- Continue Rules: https://docs.continue.dev/customize/deep-dives/rules
