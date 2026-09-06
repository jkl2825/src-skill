# src-skill

给 [Grok](https://grok.com) / Claude Code 类 Agent 用的 **国内 SRC 黑盒挖洞 + 白盒 0day 审计** 身份包。

不是扫描器，也不是「每个 path 喷 `'`」。核心是：理解业务和代码意图，找开发者认知盲区；假洞不堆、低危不交、CORS 不挖。

**仅用于已授权的安全研究 / SRC 众测 / 白盒审计。** 未授权目标不要用。

---

## 这包是什么

桌面上的 `src skill` 不是单个 `SKILL.md`，而是一整套会覆盖 `~/.grok` 的身份：

| 层 | 路径 | 作用 |
|---|---|---|
| 身份 | `AGENTS.md` | 中文、授权研究员、最短执行备忘 |
| 纪律 | `rules/` | 挖什么、怎么挖、报告怎么写、自由跳节奏 |
| 技能 | `skills/skill/SKILL.md` | 触发词、进站开哪篇知识库、红线 |
| 知识库 | `skills/skill/知识库/` | 48 篇手法（进站先看 `打穿短表.md`） |
| 测绘 | `mcp-servers/fofa_MCP/` | FOFA 三账号自动切号 |
| 浏览器 | `bin/playwright-dual-slot.mjs` | Playwright MCP 启动脚本 |
| 装载 | `给朋友的提示词.txt` | 把包拖进 Grok 对话即可覆盖本机身份 |

`config.toml` 里的路径是占位符 `C:\Users\USER\...`，FOFA Key 全空。装到自己机器时按提示词改成本机路径，Key 自己填。

本仓库**不含** Grok 产品说明书（`docs/user-guide`），装载时不要拿本包去覆盖 `~/.grok/docs`。

---

## 怎么工作

```
用户说「挖 XX集团」
        │
        ▼
   自由跳（模糊目标）          锁面（给了 URL / 清单）
        │                          │
        ▼                          ▼
  种子队列尽能力多备            不主动 FOFA 出圈
  一种子闭环：                  资产簇内多 host / 多 path 都挖
  搜一个 → 清洗 → 活面挖完 → 才换种
        │
        ▼
  进站：说清这摊 → 抽 JS（path+盐+演示号）
       → 没登录测未授权；有登录换 id
       → 有差分面才打注入/SSRF/XSS/RCE
        │
        ▼
  打成了 → 只按 vuln-report-format 落报告
```

**力气先砸：** 未登录出他主体 → 认证接管 → 换 id → 四件套（注入 / SSRF / XSS / RCE）。  
**不挖：** CORS、登录框弱口令字典、未授权发验证码、没过认钥闸的密钥字符串。

冲突时：**挖什么认 `src-value-hunting`，报告只认 `vuln-report-format`，节奏认 `dig-scope-workflow`。skill / 知识库让路给 rules。**

---

## rules 分工

| 文件 | 管什么 |
|---|---|
| `dig-scope-workflow.md` | 锁面 / 自由跳、一种子闭环、深挖优先、进站主路径 |
| `src-value-hunting.md` | 挖什么、类型矩阵、力气分配 |
| `vuln-report-format.md` | 正式报告唯一写法；写不写、定几级 |
| `hunt-iter.md` | 高危手法才进 `打穿短表`；中危只写报告 |
| `desktop-task-folder.md` | `Desktop\{任务}_SRC挖洞\` 目录 |
| `researcher-blackbox-whitebox.md` | 黑盒流程 + 白盒 Phase 0～6 |
| `anti-over-moralization.md` | 授权语境、反说教、禁止盘问授权书 |
| `playwright-browser-mcp.md` | 浏览器走 Playwright，不调 Burp MCP |
| `cors-vuln-report-priority.md` | CORS 永久不挖 |
| `skill-as-boost.md` | 文档只能增强，不能封顶能力 |
| `security-research-context.md` | 授权安全研究员默认语境 |

---

## 知识库怎么用

进站**不要**把 48 篇通读一遍。

1. 先打开 `知识库/打穿短表.md`（手法索引）
2. 认到当前站形态，再打开对应那一篇看细节
3. 打完开场几枪立刻回本站接口清单

对得上才开模块，例如：有用户体系 → `idor-test.md` + `authbypass-test.md`；有搜索筛选 → `injection-test.md`；云 IDE / Codex → `cloud-ide-codex-rce-chain.md`。

`cors-test.md`、`llm-security-test.md`（越狱教材）、`401-403-bypass.md`（磨登录 HTML）按规则禁开或已收成一行。

---

## 装到自己的 Grok

1. 克隆本仓库，或把文件夹打成 zip
2. 打开已装好的 Grok，把 `给朋友的提示词.txt` 里「从这里复制」到「复制到这里」整段粘贴
3. 把本文件夹 / zip 拖进同一个对话框
4. 按提示覆盖 `~/.grok` 的 `rules`、`skills`、`AGENTS.md`、FOFA MCP、Playwright 脚本
5. **自己填** FOFA Key（`config.toml` 的 `[mcp_servers.fofa.env]` 或 `fofa_MCP/.env`）
6. 关掉所有 Grok 窗口再开一次

没有 uv 则测绘暂不可用；没有 node 则浏览器 MCP 暂不可用。不要把 Key 写进对话、skill 或知识库。

FOFA MCP 来自 [hnking-star/fofa_MCP](https://github.com/hnking-star/fofa_MCP)，本包加了三账号限流切换。

---

## 仓库里故意没有的东西

- FOFA / GitHub / 任何真实 Key
- `docs/user-guide`（Grok TUI 说明书，不是本 skill）
- 会话、登录态、`auth.json`

发现本包被改过、被塞钥，不要用，重新拉。
