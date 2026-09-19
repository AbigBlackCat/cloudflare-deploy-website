<div align="center">

# 网站部署到 Cloudflare Skill

> 把你电脑里的现有网站，安全部署到 Cloudflare Workers，并拿到可以公开访问的 `workers.dev` 地址。

[![Agent Skills](https://img.shields.io/badge/Agent%20Skills-Compatible-16a34a)](https://agentskills.io)
[![skills CLI](https://img.shields.io/badge/skills%20CLI-Compatible-2563eb)](https://github.com/vercel-labs/skills)
[![Cloudflare Workers](https://img.shields.io/badge/Cloudflare-Workers-f38020?logo=cloudflare&logoColor=white)](https://developers.cloudflare.com/workers/)
[![Multi Agent](https://img.shields.io/badge/Agent-Codex%20·%20Claude%20Code%20·%20Cursor%20·%20Gemini-7c3aed)](#按不同-agent-安装)

**不要求你先买域名。先让网站上线，域名可以以后再接。**

[它能做什么](#它能做什么) · [安装](#安装) · [怎么使用](#怎么使用) · [执行流程](#skill-会怎样工作) · [常见问题](#常见问题)

</div>

---

## 它能做什么

这个 Skill 是给 AI 编程 Agent 使用的部署工作流。安装后，你可以直接让 Agent 检查当前项目并完成：

- 判断项目是静态网站、SPA、SSR 还是全栈应用。
- 识别 npm、pnpm、yarn、bun 等包管理器和已有构建脚本。
- 选择适合项目的 Cloudflare Workers 部署方式。
- 使用项目自己的 Wrangler 版本和配置，不随意升级依赖。
- 检查 Cloudflare 登录账号，创建项目真正需要的资源。
- 安全设置 secrets，避免把密钥写进 Git。
- 构建、dry run、部署并获得 `*.workers.dev` 地址。
- 验证首页、深层路由、静态资源、SSR/API 和必要的数据路径。
- 最后明确告诉你：部署了什么、网址是什么、哪些步骤还需要你操作。

它不会因为 Cloudflare 有 D1、KV、R2 就全部创建。项目不用的资源不会增加。

## 适合哪些情况

| 你的情况 | 是否适合 |
| --- | --- |
| 已经有一个本地网站，想发布到公网 | 适合 |
| Vite、React、Vue、静态 HTML 等前端项目 | 适合 |
| 支持 Cloudflare Workers 的 SSR/全栈项目 | 适合 |
| 网站还没有域名，先用免费测试地址 | 非常适合 |
| 网站已经部署，只想绑定自己的域名 | 请使用 [cloudflare-connect-domain](https://github.com/AbigBlackCat/cloudflare-connect-domain) |
| 想购买域名 | 本 Skill 不负责购买 |
| 想把已有 Pages 项目迁移到 Workers | 只有你明确要求迁移时才处理 |

## 使用前准备

小白只需要先准备四样东西：

1. **一个能在本地运行的网站项目**。
2. **Cloudflare 账号**：没有可以到 [Cloudflare](https://dash.cloudflare.com/sign-up) 注册。
3. **Node.js**：建议安装当前 LTS 版本；终端运行 `node -v` 能看到版本号即可。
4. **一个支持 Agent Skills 的 AI 编程工具**，例如 Codex、Claude Code、Cursor 或 Gemini CLI。

你不需要提前安装 Wrangler。项目已有 Wrangler 时会使用项目版本；没有时，Agent 会根据项目情况说明是否需要添加。

> Cloudflare 登录、账号选择、付款方式确认等网页步骤，可能需要你本人点击。Skill 会停在准确位置告诉你怎么做，不会假装已经完成。

---

## 安装

### 方式一：自动识别 Agent（最适合小白）

打开终端，复制下面一行：

```bash
npx skills add AbigBlackCat/cloudflare-deploy-website -g
```

- `npx` 第一次运行时可能询问是否安装 `skills`，输入 `y` 并回车。
- `-g` 表示全局安装：以后打开其他网站项目也能使用。
- 安装器会寻找你电脑上的兼容 Agent，并让你选择安装目标。

如果只想给当前项目使用，去掉 `-g`：

```bash
npx skills add AbigBlackCat/cloudflare-deploy-website
```

### 按不同 Agent 安装

已经知道自己使用哪个工具时，可以直接运行对应命令：

| Agent | 一键安装命令 | 全局安装位置 |
| --- | --- | --- |
| Codex | `npx skills add AbigBlackCat/cloudflare-deploy-website -g -a codex -y` | `~/.codex/skills/` |
| Claude Code | `npx skills add AbigBlackCat/cloudflare-deploy-website -g -a claude-code -y` | `~/.claude/skills/` |
| Cursor | `npx skills add AbigBlackCat/cloudflare-deploy-website -g -a cursor -y` | `~/.cursor/skills/` |
| Gemini CLI | `npx skills add AbigBlackCat/cloudflare-deploy-website -g -a gemini-cli -y` | `~/.gemini/skills/` |
| OpenCode | `npx skills add AbigBlackCat/cloudflare-deploy-website -g -a opencode -y` | `~/.config/opencode/skills/` |
| OpenClaw | `npx skills add AbigBlackCat/cloudflare-deploy-website -g -a openclaw -y` | `~/.openclaw/skills/` |
| GitHub Copilot | `npx skills add AbigBlackCat/cloudflare-deploy-website -g -a github-copilot -y` | `~/.copilot/skills/` |
| Hermes Agent | `npx skills add AbigBlackCat/cloudflare-deploy-website -g -a hermes-agent -y` | `~/.hermes/skills/` |

同时安装到多个 Agent：

```bash
npx skills add AbigBlackCat/cloudflare-deploy-website -g -a codex claude-code cursor -y
```

### 手动安装

如果你的 Agent 不支持 `npx skills`，可以把仓库克隆到它的 skills 目录。以 Codex 为例：

```bash
git clone https://github.com/AbigBlackCat/cloudflare-deploy-website.git ~/.codex/skills/cloudflare-deploy-website
```

Claude Code 把末尾路径换成 `~/.claude/skills/cloudflare-deploy-website`；Cursor 换成 `~/.cursor/skills/cloudflare-deploy-website`。其他 Agent 请查它自己的 skills 目录。

### 不安装，临时使用一次

支持 `skills use` 的环境可以生成一次性提示词：

```bash
npx skills use AbigBlackCat/cloudflare-deploy-website@cloudflare-deploy-website
```

如果你的 AI 工具完全不支持 Skills，也可以打开仓库中的 [`SKILL.md`](SKILL.md)，把内容连同你的部署请求一起粘贴给 AI。

### 检查是否安装成功

```bash
npx skills list -g
```

列表里看到 `cloudflare-deploy-website` 即表示安装成功。看不到时，先确认是否误装成项目级：

```bash
npx skills list
```

---

## 怎么使用

先用终端进入网站项目目录，再打开你的 AI Agent。然后直接说：

```text
请使用 $cloudflare-deploy-website，把当前网站部署到 Cloudflare。
先检查项目和未提交修改，优先使用现有构建脚本。
先部署到 workers.dev，部署后帮我验证首页和主要功能。
```

也可以更口语化：

```text
帮我把这个网站部署到 Cloudflare。我没有域名，先用免费地址。
```

```text
这个 React/Vite 项目本地已经能运行，请部署到 Cloudflare Workers，遇到需要我登录时再叫我。
```

```text
检查这个全栈项目是否适合 Cloudflare Workers；适合的话直接完成部署和线上验证。
```

Agent 能自动发现 Skill 时不一定要写 `$cloudflare-deploy-website`，但显式写出最稳妥。

## Skill 会怎样工作

```text
检查项目与 Git 状态
        ↓
识别静态 / SPA / SSR / 全栈
        ↓
确认构建命令、Wrangler 版本和 Cloudflare 账号
        ↓
只配置项目需要的 Worker 与资源
        ↓
本地构建 + dry run
        ↓
设置密钥 / 执行必要迁移 / 部署
        ↓
访问 workers.dev 地址并验证真实功能
        ↓
交付网址、资源清单、验证结果和待办
```

### 哪些事情可能需要你操作

- 登录或注册 Cloudflare。
- 在浏览器授权 Wrangler。
- 从多个 Cloudflare 账号中选择正确账号。
- 确认 R2、付费计划或其他可能收费的能力。
- 提供项目自己的第三方服务密钥。

Agent 应该先完成所有可以安全自动完成的工作，再让你处理这些无法代办的步骤。

## 安全边界

- 不覆盖你未确认的本地修改。
- 不把 API Token、密码或 secret 写入 Git。
- 不对未确认的远程数据库执行迁移。
- 不为了“配置齐全”创建项目不需要的 Cloudflare 产品。
- 不把上传成功当成上线成功，必须访问线上地址验证。
- 同一个错误反复出现时停止盲目重试，告诉你准确原因和下一步。

## 最终你会拿到什么

一次完整执行应该交付：

- Cloudflare Worker 名称与目标环境。
- 可访问的 `workers.dev` 正式网址。
- 创建或绑定的资源名称，例如 D1、KV、R2；没有使用则明确说明。
- 配置过的 secret 名称，但绝不显示 secret 值。
- 执行过的 migrations 和构建检查。
- 首页、路由、资源、API 等线上验证结果。
- 仍需你手动完成的事项。

---

## 常见问题

### 需要先买域名吗？

不需要。Cloudflare Workers 会提供 `*.workers.dev` 地址。网站确认正常后，再安装 [cloudflare-connect-domain](https://github.com/AbigBlackCat/cloudflare-connect-domain) 接入域名。

### Cloudflare 一定免费吗？

不是。小型网站通常可以从免费额度开始，但实际费用取决于请求量和使用的产品。R2、数据库、邮件或付费计划需要按 Cloudflare 当前定价确认，Skill 不会承诺永久免费。

### 为什么不直接使用 Cloudflare Pages？

Cloudflare 目前建议新网站优先使用 Workers + Static Assets。已有 Pages 项目不会被无故迁移。

### 网站部署后打开是空白页怎么办？

常见原因包括构建目录错误、SPA 深层路由没有回退、静态资源基础路径错误或运行时异常。Skill 会结合构建产物、浏览器请求和 Worker 日志定位，而不是重复部署碰运气。

### 如何更新 Skill？

```bash
npx skills update cloudflare-deploy-website
```

如果它是全局安装，也可以更新所有全局 Skills：

```bash
npx skills update -g
```

### 如何卸载？

```bash
npx skills remove -g cloudflare-deploy-website
```

## 仓库结构

```text
cloudflare-deploy-website/
├── README.md
├── SKILL.md
├── agents/
│   └── openai.yaml
└── references/
    └── deployment-workflow.md
```

- `README.md`：给人阅读的中文安装和使用说明。
- `SKILL.md`：Agent 激活 Skill 后读取的核心规则。
- `agents/openai.yaml`：Codex 等客户端使用的界面信息。
- `references/deployment-workflow.md`：执行首次部署时按需读取的详细流程。

## 依据与致谢

工作流来自真实网站先上线 `workers.dev`、再接入域名的实践，并依据以下资料持续校准：

- [Cloudflare Workers Static Assets](https://developers.cloudflare.com/workers/static-assets/)
- [Cloudflare Framework Guides](https://developers.cloudflare.com/workers/framework-guides/)
- [Wrangler 文档](https://developers.cloudflare.com/workers/wrangler/)
- [Agent Skills 标准](https://agentskills.io)
- [Vercel Labs skills CLI](https://github.com/vercel-labs/skills)
