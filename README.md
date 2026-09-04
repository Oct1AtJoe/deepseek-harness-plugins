# DeepSeek Harness 手写插件集

本仓库收录 [DeepSeek Harness](https://github.com/deepseek-ai)（dsh）桌面生态中**自维护的手写插件**（rc.1 适配版），与本地 `C:\dsh-ecosystem\plugins\` 工作副本对应（本地为准，本仓库为备份与发布面）。

## 插件清单（7 个，全部在用）

| 插件 | 端 | 说明 |
|------|----|------|
| `plugins/kanye-pet` | host + client | 桌宠 Kanye 宿主半部：状态/资产/配置 REST 路由、turn 边沿检测、桌宠气泡通知队列、XP 账本持久化。详见其 `AGENTS.md` |
| `plugins/ui-kanye-pet` | client | 桌宠设置面板（尺寸/透明度/角色/音效），挂官方设置注册表 |
| `plugins/ui-deliverables-custom` | client | 回合末「本次产物」行：多行换行、+/− 真实 diff 徽标、展开面板。详见其 `AGENTS.md` |
| `plugins/ui-resend-failed-round` | client | 失败轮尾部「重新发起」按钮（turnTail 链，priority −10） |
| `plugins/ui-subagent-custom` | client | 子代理 composer 机器人按钮 + 运行数角标 |
| `plugins/ui-theme-custom` | client | Aurora/Nebula 主题，注册到官方主题注册表 |
| `plugins/dsh-session-folders-custom` | host | 会话归档/文件夹：uiWorkspace 注入、open-folder 路由、CJK 标题生成 |

## 部署方式

宿主 profile（`~/.dsh/profiles/web`）通过 `link:` 依赖直接链接本地插件目录，产物改动重启 dsh 即生效：

```json
"@deepseek-ai/dsh-kanye-pet": "link:../../dsh-ecosystem/plugins/kanye-pet"
```

## 构建链（client 插件）

生态根没有 `scripts/types`，插件 tsconfig 需覆盖 `typeRoots` 指向 harness 仓库；依赖解析走 harness 仓库的 node_modules：

```sh
env -u NODE_OPTIONS node <harness>/node_modules/typescript/bin/tsc -p tsconfig.json   # 出 lib/types
env -u NODE_OPTIONS node <harness>/node_modules/tsdown/dist/run.mjs --config tsdown.client.ts  # 出 lib/client.js
```

host 插件（kanye-pet、dsh-session-folders-custom）为纯 `.mjs`，改 `lib/` 即生效，无需构建。

## 备注

- 部分插件目录内带 `AGENTS.md`，是该插件的维护文档（架构、坑点清单、修复记录），改动前先读。
- rc.1 迁移要点：`Session.events` 已私有化（读日志用 `session.snapshotEvents()`）；链式槽要显式 `priority`（官方/第三方插件会用负值抢占）。
