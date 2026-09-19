# 首次部署工作流

首次把网站部署到 Cloudflare 时读取本文。具体配置字段和命令参数可能变化，执行前同时检查当前 Cloudflare 官方文档和项目本地 Wrangler 帮助。

## 选择部署方式

| 项目证据 | 推荐方式 |
| --- | --- |
| 只有静态文件，不需要服务端运行时 | Workers Static Assets 指向真实构建目录 |
| 客户端 SPA | Workers Static Assets，加框架适用的 SPA 回退配置 |
| SSR 或全栈框架 | 使用该框架当前支持的 Cloudflare Workers 适配器和生成配置 |
| 已经是 Pages 项目 | 除非用户明确要求迁移，否则继续维护 Pages |
| 已有 Worker 配置和部署脚本 | 保留现有方式，只修正必要部分 |

不要默认产物一定叫 `dist` 或 `build`，也不要默认应用位于仓库根目录。应从脚本和框架配置中确认。

## 经过验证的执行顺序

1. 记录 `git status`、包管理器、锁文件、构建/部署脚本、框架、Wrangler 版本和现有 Cloudflare 文件。
2. 运行项目本地 Wrangler 身份命令，确认 Cloudflare 账号。
3. 完成本地构建，只修复阻塞部署的问题，不做无关的大规模清理。
4. 创建或调整源 Wrangler 配置。新静态项目通常需要 Worker 名称、当前 compatibility date 和静态产物目录；全栈项目可能在构建时生成可部署配置。
5. 只添加代码需要的绑定。如果应用确实使用 D1、KV 或 R2，再创建资源、把核实后的标识写入配置，并在依赖数据库结构前执行远程 D1 migrations。
6. 通过安全输入设置生产密钥，不把密钥复制进受 Git 管理的文件。
7. 支持时先 dry run，再执行仓库自己的部署命令。项目命令可能依次完成资源检查、构建、迁移、Worker 部署和内容同步，不应随意打乱。
8. 保存部署返回的网址，并从本地开发服务器之外验证网站。

## 命令选择

使用项目本地包管理器，不依赖全局 Wrangler。以下只是命令形态示例，不能替代对项目脚本的检查：

```sh
<包管理器> exec wrangler whoami
<包管理器> exec wrangler deploy --dry-run --config <配置文件>
<包管理器> exec wrangler deploy --config <配置文件>
```

使用锁定版本的 `wrangler <command> --help` 核对资源、迁移、环境和配置参数。如果仓库已有等价脚本，优先运行脚本。

## 上线验收

- 部署输出中的 Worker 和环境与目标一致。
- `https://<worker>.<账号子域>.workers.dev` 返回预期网站。
- 直接访问非首页的应用路由时行为正确。
- JS、CSS、图片和哈希资源正常加载，没有混合内容或 MIME 错误。
- SSR/API 路由返回正确状态与内容，日志没有启动异常。
- 只有应用使用 D1/KV/R2 时，才测试相应远程读写路径。
- Git 中没有密钥、本地状态、临时凭据或数据库导出。

## 权威资料

- Workers Static Assets：<https://developers.cloudflare.com/workers/static-assets/>
- 框架指南：<https://developers.cloudflare.com/workers/framework-guides/>
- Wrangler 命令：<https://developers.cloudflare.com/workers/wrangler/commands/>
- Wrangler 配置：<https://developers.cloudflare.com/workers/wrangler/configuration/>
- Secrets：<https://developers.cloudflare.com/workers/configuration/secrets/>
- 版本与部署：<https://developers.cloudflare.com/workers/configuration/versions-and-deployments/>
