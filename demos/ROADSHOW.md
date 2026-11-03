# Agent Harbor · 路演稿（研发赛道）

对齐决赛维度：**商业价值&产品设计 50% · 架构 20% · AI 价值呈现 15% · AI 工程效率 15%**。  
口播只讲能当场指证的；边界写清，不硬吹。

---

## 30 秒电梯稿

> 本机跑 Claude / Codex / dsh 时，**密钥不进沙箱、出站只走 Harbor Gate、越权拦下可三选一扩权**。  
> 副本里改文件，确认后再导回源目录。桌面 App 可下，MIT，SHA256 可校验。

一句问题→方案：

| 痛点 | Harbor |
|---|---|
| Key 进 Agent 环境 / 日志 | Keychain + Gate 代填，沙箱只有 Sentinel |
| Agent 乱连外网 | 白名单主机；无「全网放行」 |
| 工作区被直接改坏 | 默认 clone 副本 + 一键导回 |

---

## 三分钟结构（推荐主路径）

| 段 | 时长 | 讲什么 | 指什么 |
|---|---|---|---|
| 1 场景 | 20s | 安全官 / 个人开发者：想用强 Agent，不敢把 Key 和工作区交给裸 CLI | — |
| 2 产品闭环 | 90s | **录入 Key → 隔离跑 → Gate 挡越权 → 三选一扩权 → 导回** | 桌面 live 或预备录屏 |
| 3 架构一句 | 30s | Seatbelt/Docker Runtime · Gate · Kit（三 Agent）分层；契约 CONTRACT v1.2 | 架构简图（见下） |
| 4 AI 价值 | 20s | 不是「又一个 Chat」：三类真实 Agent CLI + MCP 机制；本机计量可归因 | 记录·计量 Tab |
| 5 工程效率 | 20s | race + 安全 skip 闸门；修 bug 带回归；发版测门禁；覆盖率可归属提交 | CI / evidence |
| 收 | 10s | 边界：未公证、MCP 是机制+清单、陌生机未声称 | 诚实加分 |

---

## 维度 → 亮点映射（秀什么）

### ① 商业价值 & 产品设计（50%）

**主张**：把「敢在本机跑强 Agent」做成可完成的任务，而不是概念页。

上场只秀一条主路径（不要堆功能）：

1. 模型页录入 Key → 提交后输入清空、只留账号引用  
2. 向导：工作区 + clone + Agent → 开始  
3. 工作台有真实输出 / 真实报错（勿用假成功）  
4. `EXPAND-TASK` / 阻拦：弹窗三选一；「本项目」可撤销  
5. 停止 → **导回原文件夹** → toast 含源路径  

素材：`demos/screenshots/`（`03` Key、`12` 工作台、`11` 扩权、`06*` 计量）；live 样张 `demos/screenshots/live-browser/`；口播旁证 [B6-SUCCESS-DEMO](B6-SUCCESS-DEMO.md)、[permission-live-test](permission-live-test.md)。

**减分防御（评审已点）**

- 外部「获取即用」= 作者本机级；**独立陌生机未做 · 不声称**（[stranger-first-run-record](stranger-first-run-record.md)）  
- 未 Apple 公证 → Gatekeeper「仍要打开」+ 预装 CLI，上台主动说一句  

### ② 架构设计（20%）

**主张**：隔离面 / 出站闸 / Agent 适配 **分层清晰**，切换后端不改产品契约。

口播一句话：

> Runtime（Seatbelt 或 Docker）管进程与文件系统；Gate 管出站与凭据代填；Kit 把 Claude/Codex/dsh 接到同一会话面。

可画三框：

```text
[ 前端 / DesktopAPI ]
        ↓ CONTRACT
[ Orchestrator · 会话 ≤5 ]
   ↓              ↓
[ Runtime ]    [ Gate ]
 Seatbelt/Docker   白名单·代填·扩权
   ↓              ↓
[ Kit: Claude | Codex | dsh ] → MCP（远程 HTTP / OAuth / 宿主 stdio）
```

**勿展开成**：三传输并存细节、超长视图函数——评委若追问，答「交付优先主路径收敛，http-live 仅复验桥」。

### ③ AI 价值呈现（15%）

**主张**：AI 是**受控执行面**，不是装饰性聊天框。

必提三点（各 1 句）：

1. **三类主流 Agent CLI** 同沙箱同 Gate，能力边界设置页写明  
2. **MCP**：保证「Key 不进沙箱」的**机制** + [兼容矩阵](mcp-compat-matrix.md)**清单**（非「所有主流开箱」）；公网汇率/油价、OAuth 真 IdP 有 evidence  
3. **本机计量**：按模型 token / 参考单价 / 分模型时序——可归因、不外传  

### ④ AI 工程效率（15%）← 决赛强调维度

**主张**：用 AI 写代码时，**可复验与安全断言先于功能堆砌**。

上场金句（选 3 条，勿念清单）：

| 姿势 | 证据一句 |
|---|---|
| 安全相关测试 **禁止用 skip 冒充通过** | CI `ci_skip_gate.py` 与 race 同 run |
| 修缺陷 **自带回归断言** | 中文化 `No such tool`、扩权/计量等带 `*.test.js` / Go test |
| 发版 **不能跳过测试** | `release.yml`：gofmt + `go test -race` + `npm test` 再打包 |
| 覆盖率 **可归属提交** | `coverage-source-sha`；旁证见 `demos/evidence/coverage-*` |
| 评委反馈 **当 sprint 关单** | gofmt 红灯 → 修对齐 + 计量 UX 抽测 + 发版门禁（PR #35） |

**主动认的缺口（比被问更稳）**

- 曾有固定提交 gofmt 红 → 已修；macOS Actions runner 若秒取消，覆盖率以本机/历史绿 run 旁证说明，不装成「永远绿」  
- 不把「AI 写了很多行」当效率；效率 = **断言打在题面上 + 闸门防自欺**

---

## 现场 Demo 清单（8 分钟版）

**开场前 2 分钟自检**

- [ ] `bin/AgentHarbor.app` 或 Release zip；侧栏隔离就绪（Seatbelt 即可，Docker 作加分镜）  
- [ ] 已有模型账号（Key 已在钥匙串）；工作区 `demos/workspace`  
- [ ] 演示开关：需要扩权镜再开 `harbor_demo_ask`；阻拦用 `BLOCK-TASK`  
- [ ] 网络可达模型上游；勿投屏 Key / `.env`

**镜头顺序**

| # | 操作 | 停顿讲一句 |
|---|---|---|
| 1 | HOME → 新会话向导 | 「CLI 开局就检，缺了当场知道」 |
| 2 | 开始 → running 输出 | 「沙箱内真 Agent，不是 mock 字幕」 |
| 3 | 触发越权 / `EXPAND-TASK` | 「挡下来，三选一，没有全网按钮」 |
| 4 | 导回 | 「副本与源分离；你点头才写回」 |
| 5 | 记录 → 计量 | 「本机用量按模型，可对单价」 |
| 6（可选） | 设置 → Docker / 隔离检测 | 「同一产品面，换隔离后端」 |

备用：预录 `agent-harbor-demo` 视频；卡壳切截图 `12`/`11`/`06b`，不现场 debug。

---

## 评委追问速答

| 问 | 答 |
|---|---|
| 和 Cursor/沙箱 CLI 差在哪？ | 桌面产品化：Keychain、Gate 扩权 UX、导回、多 Agent 统一会话面；不是编辑器插件 |
| 所有 MCP 都行吗？ | **不行**。机制保证 Key 不出宿主；清单见矩阵；私有方言 / 乱 PATH stdio 不承诺 |
| 别人下载就能用吗？ | 本机/harness 已复验；**陌生机未声称**；未公证 + 需 CLI |
| AI 写的占比？ | 不谈比例；谈门禁：race、skip 闸、发版测试、回归测、评委单当天合入 |
| 容器真隔离吗？ | Seatbelt 主路径实证；Docker P2 API+本机 GUI 有证据；「密闭 VM / 白名单外不可达」不做无攻击测试的夸张字幕 |
| 商业化？ | 本轮交付是安全本机运行面；遥测/付费不在承诺范围，不硬编 ARR |

---

## 一页「别说」清单

- ❌ 「所有主流 MCP 开箱」  
- ❌ 「他人获取即用已验证」  
- ❌ 「已公证 / 企业级零门槛」  
- ❌ 「AI 自动写完全部架构」  
- ❌ 把 fail-closed `UNIMPLEMENTED` 口播成「容器隔离功能名」  

---

## 上场包（文件）

| 用途 | 路径 |
|---|---|
| 本页 | `demos/ROADSHOW.md` |
| **完整功能录屏步骤** | [FULL-FEATURE-DEMO-RECORDING](FULL-FEATURE-DEMO-RECORDING.md)（≈12 分钟分镜 / 精简 7 分钟） |
| 人工录屏主线（任务/MCP/导回） | [RECORDING-GUIDE](RECORDING-GUIDE.md) |
| 成功态 60s | [B6-SUCCESS-DEMO](B6-SUCCESS-DEMO.md) |
| 扩权/阻拦 | [permission-live-test](permission-live-test.md) |
| 首跑口径 | [FIRST-RUN](FIRST-RUN.md) · [stranger](stranger-first-run-record.md) |
| MCP 边界 | [mcp-compat-matrix](mcp-compat-matrix.md) |
| 截图 | `demos/screenshots/` |
| 工程完成度 | [ENG-STATUS](../docs/ENG-STATUS.md) |
| 验收 | [ACCEPTANCE](../docs/ACCEPTANCE.md) |

**固定话术收尾**：

> 我们做成的是：**本机密钥、隔离执行、闸口扩权、可导回、可复验**。  
> 工程上用测试闸门和发版门禁约束 AI 交付，而不是用演示视频代替证据。
