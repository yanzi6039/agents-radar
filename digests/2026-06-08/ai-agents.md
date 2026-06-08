# OpenClaw 生态日报 2026-06-08

> Issues: 299 | PRs: 500 | 覆盖项目: 13 个 | 生成时间: 2026-06-08 01:03 UTC

- [OpenClaw](https://github.com/openclaw/openclaw)
- [NanoBot](https://github.com/HKUDS/nanobot)
- [Hermes Agent](https://github.com/nousresearch/hermes-agent)
- [PicoClaw](https://github.com/sipeed/picoclaw)
- [NanoClaw](https://github.com/qwibitai/nanoclaw)
- [NullClaw](https://github.com/nullclaw/nullclaw)
- [IronClaw](https://github.com/nearai/ironclaw)
- [LobsterAI](https://github.com/netease-youdao/LobsterAI)
- [TinyClaw](https://github.com/TinyAGI/tinyagi)
- [Moltis](https://github.com/moltis-org/moltis)
- [CoPaw](https://github.com/agentscope-ai/CoPaw)
- [ZeptoClaw](https://github.com/qhkm/zeptoclaw)
- [ZeroClaw](https://github.com/zeroclaw-labs/zeroclaw)

---

## OpenClaw 项目深度报告

# OpenClaw 项目动态日报 (2026-06-08)

## 1. 今日速览
OpenClaw 项目今日保持极高的社区活跃度与开发迭代速度。过去 24 小时内，Issue 活动达 299 条（新开/活跃 180，关闭 119），PR 更新高达 500 条（待合并 374，合并/关闭 126），显示出核心团队与社区正在密集处理积压与新增反馈。当前开发重心明显聚焦于**多渠道消息投递稳定性**（尤其是飞书、Telegram、iMessage）、**会话状态与上下文管理**（SQLite 迁移与 compaction 机制），以及**子智能体权限与安全边界**的加固。虽然今日无新版本发布，但大量 P1/P2 级别的修复 PR 已进入 Maintainer Review 阶段，预计将在下一个版本集中释放。

## 2. 版本发布
今日无新版本发布。

## 3. 项目进展
尽管没有新版本发布，今日仍有 126 个 PR 被合并或关闭，大量底层修复与架构优化正在落地，主要推进如下：
- **子智能体权限与安全边界完善**：PR [#78441](https://github.com/openclaw/openclaw/pull/78441) 实现了 `sessions_spawn` 的 `toolsAllow` 转发与持久化，完善了子智能体的工具调用白名单机制；PR [#91286](https://github.com/openclaw/openclaw/pull/91286) 修复了 `minSecurity` 优先级反转的关键逻辑错误，确保 `full` 权限不再是限制最严格的级别。
- **渠道消息投递稳定性提升**：PR [#89045](https://github.com/openclaw/openclaw/pull/89045) 修复了群聊会话进入 `failed` 状态后静默丢弃后续消息的严重问题；PR [#89659](https://github.com/openclaw/openclaw/pull/89659) 为飞书 API 增加了 230020/230006 限流错误的自动重试逻辑；PR [#90858](https://github.com/openclaw/openclaw/pull/90858) 重构了 iMessage 的分片发送合并机制。
- **运行时状态与上下文感知**：PR [#90101](https://github.com/openclaw/openclaw/pull/90101) 引入了运行时自上下文配置与工具，为后续的 scale/cost-awareness 奠定基础；PR [#91287](https://github.com/openclaw/openclaw/pull/91287) 修复了 Cron 心跳模型输入中的重复系统事件问题。

## 4. 社区热点
今日讨论最热烈的问题集中在智能体输出可见性、会话架构迁移及全局状态污染上：
- **[Issue #25592](https://github.com/openclaw/openclaw/issues/25592)** (27 评论，🦞 diamond lobster)：工具调用间的内部文本（如错误处理、思考过程）被错误路由到 Slack/iMessage 等消息渠道。这暴露了 Agent 内部推理与用户可见输出的隔离设计缺陷，严重影响 UX 甚至引发安全担忧。
- **[Issue #88838](https://github.com/openclaw/openclaw/issues/88838)** (18 评论，🌊 off-meta tidepool)：核心会话/转录本向 SQLite 迁移的架构讨论。社区与维护者正致力于通过抽象接缝分支避免大规模重写带来的高风险，这标志着 OpenClaw 底层状态存储的重大演进。
- **[Issue #90991](https://github.com/openclaw/openclaw/issues/90991)** (13 评论，🐚 platinum hermit)：Cron 定时触发器污染全局运行时状态，导致系统级瞬态过载崩溃。社区反馈该问题在特定 Provider 下极易复现，亟需运行时隔离机制。
- **[Issue #29387](https://github.com/openclaw/openclaw/issues/29387)** (14 评论，🦞 diamond lobster)：`agentDir` 中的引导文件（如 SOUL.md）被静默忽略，仅加载 workspace 目录，严重影响了多 Agent 场景下的个性化配置。

## 5. Bug 与稳定性
今日报告了多起影响核心流程的 Bug 与回归问题，按严重程度排列如下：

**🔥 严重 / P1 级别**
- **会话状态与消息丢失**：
  - [Issue #88312](https://github.com/openclaw/openclaw/issues/88312)：2026.5.27 版本回归，Codex app-server 多工具回合完成时停滞（回归自 #84076），**暂无修复 PR**。
  - [Issue #91212](https://github.com/openclaw/openclaw/issues/91212)：网关重启后 delivery-recovery 在通道传输就绪前启动，导致消息静默丢失，**暂无修复 PR**。
  - [Issue #90639](https://github.com/openclaw/openclaw/issues/90639)：Safeguard 压缩模式下，会话可增长至上下文天花板导致 Slack 通道卡死且无恢复手段，**暂无修复 PR**。
- **环境与安全隔离破坏**：
  - [Issue #31583](https://github.com/openclaw/openclaw/issues/31583) (回归)：`exec` 工具不继承 `skills.entries.*.env` 环境变量，导致密钥注入失败，**已有链接 PR**。
  - [Issue #37634](https://github.com/openclaw/openclaw/issues/37634)：沙箱 `workspaceAccess: "none"` 模式下工作区被挂载为只读，导致工具写入失败，**暂无修复 PR**。
- **系统级崩溃**：
  - [Issue #90428](https://github.com/openclaw/openclaw/issues/90428)：WSL2 环境下 `exec` 工具触发网关 SIGTERM 重启，**暂无修复 PR**。

**⚠️ 中等 / P2 级别**
- [Issue #87136](https://github.com/openclaw/openclaw/issues/87136)：绝对 Token 阈值在切换不同上下文窗口模型时导致压缩机制崩溃（如 GLM-5.1 200K 上下文被误判为 200% 占用）。
- [Issue #87326](https://github.com/openclaw/openclaw/issues/87326)：Telegram 渠道流式输出时，工具调用间的中间文本被静默丢弃。

## 6. 功能请求与路线图信号
- **多上下文通道**：[Issue #90916](https://github.com/openclaw/openclaw/issues/90916) 提出 Topic-session families，允许一个助手跨多个命名上下文通道隔离近期记录，仅通过显式规则共享持久记忆。结合 PR [#90101](https://github.com/openclaw/openclaw/pull/90101) 的运行时自上下文感知，这可能是 OpenClaw 向多线程/多工作流记忆隔离演进的重要信号。
- **轻量级网关模式**：[Issue #86881](https://github.com/openclaw/openclaw/issues/86881) 请求提供无 AI Harness 的 Gateway-lite 部署模式，仅用于 Webhook、Cron 和确定性插件。反映出社区在复杂业务中将 AI 推理与网关路由解耦的强烈诉求。
- **Cron 与写入安全**：[Issue #40001](https://github.com/openclaw/openclaw/issues/40001) 要求 Write 工具增加 Append 模式；[Issue #90354](https://github.com/openclaw/openclaw/issues/90354) 请求为预压缩内存刷写增加边界校验。这指向未来版本中 Agent 自动化写入操作将引入更严格的防破坏护栏。

## 7. 用户反馈摘要
- **跨端状态同步痛点**：用户频繁在多客户端（WebChat, TUI, Telegram）切换，遇到在一端重置会话后，另一端 UI 不刷新或状态不同步的问题（[#38966](https://github.com/openclaw/openclaw/issues/38966), [#38091](https://github.com/openclaw/openclaw/issues/38091)），WebSocket 重连机制备受诟病。
- **内部推理外溢困扰**：Agent 的内部思考、重试或错误处理文本直接暴露给用户（[#25592](https://github.com/openclaw/openclaw/issues/25592), [#87326](https://github.com/openclaw/openclaw/issues/87326)），尤其在 Slack/Telegram 场景下产生大量"刷屏"，用户强烈呼吁区分内部日志与用户可见消息。
- **国产化模型与渠道适配**：社区对飞书、MiniMax、GLM 等国产渠道的适配反馈集中，包括飞书分页获取失败（[#37626](https://github.com/openclaw/openclaw/issues/37626)）、打字机效果实现错误（[#69572](https://github.com/openclaw/openclaw/issues/69572)）、以及国产模型上下文长度与 OpenClaw 压缩阈值不兼容等问题。

## 8. 待处理积压
当前有 374 个 PR 待合并，且存在多个高优先级但长期未决的 Issue，建议维护者重点关注：
- **关键安全与状态问题**：[Issue #25592](https://github.com/openclaw/openclaw/issues/25592) (文本泄漏至渠道)、[Issue #29387](https://github.com/openclaw/openclaw/issues/29387) (agentDir 配置失效) 和 [Issue #37634](https://github.com/openclaw/openclaw/issues/37634) (沙箱只读问题) 均为 P1 且带有 `clawsweeper:no-new-fix-pr` 标签，亟需排期解决。
- **过时但高影响的回归**：[Issue #31583](https://github.com/openclaw/openclaw/issues/31583) (exec 环境变量丢失)、[Issue #38439](https://github.com/openclaw/openclaw/issues/38439) (Webchat 头像 404) 长期处于 Stale 状态但影响日常使用。
- **大型重构 PR 阻塞**：PR [#90101](https://github.com/openclaw/openclaw/pull/90101) (运行时自上下文) 和 PR [#90480](https://github.com/openclaw/openclaw/pull/90480) (WhatsApp QA 扩展) 体积为 XL，且涉及兼容性与安全边界变更，目前处于等待作者或需更多验证状态，可能成为后续发布的瓶颈。

---

## 横向生态对比

# 2026-06-08 个人 AI 助手与智能体开源生态横向对比分析报告

## 1. 生态全景
个人 AI 助手与自主智能体开源生态正经历从“单次对话工具”向“多模型协同、多渠道触达的企业级生产力基础设施”的关键跃迁。当前各核心项目普遍处于底层基建夯实期，核心焦点高度集中于**状态持久化、沙箱安全隔离与长上下文治理**。多渠道（IM）消息投递的稳定性与国产化模型适配仍是普遍痛点，而多智能体路由（A2A协议）、异构模型组合调度及精细化上下文管理，正成为下一阶段技术突破的明确高地。

## 2. 各项目活跃度对比

| 项目名 | Issues (活跃/关闭) | PRs (待处理/合并关闭) | Release | 健康度评估 |
| :--- | :--- | :--- | :--- | :--- |
| **OpenClaw** | 299 (180/119) | 500 (374/126) | 无 | **极高** (体量最大，但积压严重，回归Bug多) |
| **ZeroClaw** | 50 (50/17) | 50 (50/11) | v0.8.0 前夕 | **高** (大版本重构期，社区热度高) |
| **IronClaw** | 50 (42/8) | 38 (22/16) | 无 | **高** (Reborn架构演进，安全基建加强) |
| **Hermes Agent**| 50 (活跃为主) | 50 (50/6) | 无 | **中高** (功能拓展快，但P1级Bug缺乏修复) |
| **PicoClaw** | 17 (关闭为主) | 12 (全合并) | Nightly | **高** (治理极佳，防御性编程清理债务) |
| **NanoBot** | 7 | 18 (14/4) | 无 | **高** (响应极快，当天提出当天修复) |
| **CoPaw** | 5 (5/0) | 2 (2/0) | 无 | **中** (存在阻断性本地部署Bug) |
| **NanoClaw** | 3 (2/0) | 9 (6/3) | 无 | **中** (有高危权限越界漏洞) |
| **Moltis** | 1 | 3 (3/0) | 无 | **中** (稳步打磨细节) |
| **LobsterAI** | 15 (14 stale) | 0 | 无 | **低** (严重停滞，核心问题长期无回应) |
| **Null/Tiny/Zepto**| 0 | 0 | 无 | **不活跃** |

## 3. OpenClaw 在生态中的定位
- **体量与规模标杆**：OpenClaw 是当前生态中绝对的活动中心，单日 Issue/PR 数量级远超同类（甚至达十倍以上），其社区规模和功能覆盖面最广，是多渠道（Slack/iMessage/飞书）适配的事实标准参照系。
- **技术路线差异**：相较于 PicoClaw（轻量/边缘设备）或 IronClaw（强安全契约架构），OpenClaw 采取了**广度优先的实用主义路线**，快速铺开渠道与子智能体功能，但目前正承受由此带来的复杂性反噬（如严重的消息路由错乱、上下文压缩死锁），正被迫向 SQLite 底层重构和运行时隔离回撤。
- **优势与挑战**：优势在于生态引力强、场景覆盖全；挑战在于严重的回归问题（P1级 Bug 多数无修复 PR）和内部状态外溢，正消耗大量社区信任，急需下个版本的大规模集中修复来稳住阵脚。

## 4. 共同关注的技术方向
- **沙箱安全与隔离边界**（*OpenClaw, NanoBot, NanoClaw, IronClaw, ZeroClaw*）：普遍面临沙箱逃逸、文件写入静默失败、MCP工具权限越界（NanoClaw的create_agent无门控）及 SSRF 风险。项目均在加强环境变量隔离和只读挂载校验。
- **上下文与状态持久化治理**（*OpenClaw, NanoBot, Hermes, Moltis, ZeroClaw*）：长对话压缩后的指令遗忘（Hermes）、孤立工具结果导致历史丢失（NanoBot）、Cron定时任务污染全局状态（OpenClaw），以及重载时上下文溢出（Moltis），表明**动态精细化上下文治理（如 ContextGovernor）**已成为刚需。
- **多智能体/多模型路由调度**（*Hermes, NanoBot, ZeroClaw, CoPaw*）：A2A协议互通（Hermes/ZeroClaw）、子智能体指定模型以降本（NanoBot）、异构视觉模型解耦（CoPaw）及模型级故障转移链（ZeroClaw），标志着单一模型包打天下的时代结束，**异构调度**成核心演进方向。
- **内部推理与用户输出隔离**（*OpenClaw, NanoBot, ZeroClaw*）：Agent的思考重试过程直接刷屏暴露至 IM 渠道，严重破坏 UX，社区强烈要求建立内部日志与对外消息的严格路由隔离。

## 5. 差异化定位分析
- **OpenClaw / ZeroClaw**：定位为企业级/重度用户的**全渠道中枢**，强调多端触达与复杂工作流，但面临沉重的架构债务。
- **IronClaw**：定位为**安全敏感型/多租户架构**，其 "Reborn" 架构专注严格的权限契约与审批租约，适合金融或高合规场景。
- **PicoClaw**：定位为**极简/边缘计算节点**，专注防御性编程和低资源设备，适合个人开发者在旧手机/Termux 上私有化部署。
- **Hermes Agent**：定位为**分布式/自进化集群**，探索 Mesh 组网与 GRPO 奖励函数，适合极客与多节点协同场景。
- **CoPaw (QwenPaw)**：定位为**国产化/多模态中转**，侧重 vLLM 本地模型兼容与异构视觉模型挂载，贴近国内私有化诉求。

## 6. 社区热度与成熟度
- **快速迭代爆发期**：**OpenClaw, ZeroClaw**。体量激增但泥沙俱下，PR 积压严重，Bug 回归频繁，处于功能交付与稳定性博弈的深水区。
- **架构重构与质量巩固期**：**IronClaw, PicoClaw, NanoBot**。I/O 与类型校验等防御性代码密集落地，重构底层状态机，项目健康度实质提升。
- **功能拓展与探索期**：**Hermes Agent, NanoClaw, CoPaw**。积极回应新需求（A2A、多模型、新渠道），但工程成熟度不足，常现阻断级 Bug 或安全漏洞。
- **停滞与预警期**：**LobsterAI**。核心交互 Bug 长期无修复，Issue 大量 Stale，社区信心流失；NullClaw 等已处于休眠状态。

## 7. 值得关注的趋势信号
1. **异构模型路由成为降本核心范式**：CoPaw 的独立视觉模型配置、NanoBot 的 spawn 指定廉价模型、ZeroClaw 的 Provider 级别故障转移，揭示出生产环境中“强模型决策+弱模型执行/感知”的复合调度正成为标配开发模式。
2. **“推理-输出”隔离成为 UX 分水岭**：OpenClaw 与 ZeroClaw 暴露的内部思考刷屏问题说明，Agent 的核心竞争力不仅在于推理能力，更在于**信息呈现的节制与编排**。未来网关层必须具备过滤/摘要内部 ReAct 过程的能力。
3. **上下文治理从“截断”走向“动态压力调节”**：NanoBot 引入 ContextGovernor 基于压力动态微压缩，取代粗暴的计数截断，这是 Agent 长期自主运行避免“失忆”或“崩溃”的关键技术解法。
4. **Skill/指令编译压缩**：ZeroClaw 提出的 Skill Compilation（将冗长 Markdown 编译为紧凑指令），直指当前 Agent 框架 Token 浪费的痛点，有望成为下一波 Agent 框架性能优化的突破口。
5. **A2A 协议催生多智能体局域网**：Hermes 与 ZeroClaw 社区对 A2A 的高票呼声，意味着智能体正在从“孤岛式工具”向“可互操作的网格节点”演进，跨框架的 Agent 发现与通信将是下半场的基础设施。

---

## 同赛道项目详细报告

<details>
<summary><strong>NanoBot</strong> — <a href="https://github.com/HKUDS/nanobot">HKUDS/nanobot</a></summary>

# NanoBot 项目动态日报 (2026-06-08)

## 1. 今日速览
NanoBot 项目今日保持高度活跃，过去24小时内 PR 更新达 18 条（14 条待合并），Issue 更新 7 条。项目重心明显向**底层稳定性、安全性与会话状态管理**倾斜，多笔 PR 聚焦沙箱环境修复、会话历史边界处理及 API 重试逻辑优化。社区互动极为高效，多个今日新提的 Bug 和 Feature Request（如 bwrap 沙箱问题、WebUI 版本展示）在当天即获得了相应的修复/实现 PR 响应，展现出极高的维护响应率与项目健康度。

## 2. 版本发布
本日无新版本发布。

## 3. 项目进展
今日共关闭/合并 4 项 PR，主要推进了多渠道适配、UI 展示与特定模型兼容性修复，项目在跨平台通道（飞书/WhatsApp）和前端体验上迈出了坚实的一步：
- **[PR #4227](HKUDS/nanobot PR #4227)** [CLOSED]: 修复了 Custom provider 下空字符串 `reasoning_content` 被强制转为 None 的问题，增强了与 DeepSeek、Kimi 等推理模型的兼容性（关联 [Issue #4105](HKUDS/nanobot Issue #4105)）。
- **[PR #2885](HKUDS/nanobot PR #2885)** [CLOSED]: 修复了飞书渠道的 mention 数据解析及 access token 初始化问题，提升了飞书群聊中 Bot 的交互稳定性（关联 [Issue #2256](HKUDS/nanobot Issue #2256)）。
- **[PR #4240](HKUDS/nanobot PR #4240)** [CLOSED]: WebUI 支持在代码块中渲染 ANSI 彩色输出，大幅提升了终端命令执行结果的可读性。
- **[PR #2663](HKUDS/nanobot PR #2663)** [CLOSED]: 修复了 WhatsApp 群聊中 LID 格式的 Bot 提及检测问题，完善了群组消息策略。

## 4. 社区热点
今日社区讨论主要围绕会话历史管理逻辑和渠道交互体验展开：
- **[Issue #2256](HKUDS/nanobot Issue #2256)**（4 条评论）：关于飞书话题群 Bot 回复逻辑的讨论。用户期望 Bot 在特定话题下回复而非群聊全局，该诉求反映了企业 IM 场景下对上下文隔离的强需求，现已通过 [PR #2885](HKUDS/nanobot PR #2885) 修复并关闭。
- **[Issue #4203](HKUDS/nanobot Issue #4203)**（2 条评论）：关于孤立工具结果导致历史消息全部丢失的严重 Bug 讨论。这触及了 Agent 会话状态管理的核心痛点，引发了关于会话边界裁剪逻辑的探讨，目前已有对应修复 PR [PR #4219](HKUDS/nanobot PR #4219)。

## 5. Bug 与稳定性
今日报告了多个涉及底层执行和会话状态的 Bug，严重程度较高，但多数已获积极修复响应：
- 🔴 **严重（数据丢失/状态异常）**：
  - **[Issue #4203](HKUDS/nanobot Issue #4203)**: 孤立工具结果导致 `find_legal_message_start` 丢弃所有会话历史。👉 **已有修复 [PR #4219](HKUDS/nanobot PR #4219)**。
  - **[PR #4234](HKUDS/nanobot PR #4234)**: OpenAI 兼容 API 空响应重试导致用户轮次被重复记录，造成会话污染。👉 **已提修复 PR**。
- 🟠 **高（沙箱与执行环境）**：
  - **[Issue #4237](HKUDS/nanobot Issue #4237)**: bwrap 沙箱未重置 `$HOME` 环境变量，导致工具写入失败。👉 **已有修复 [PR #4239](HKUDS/nanobot PR #4239)**。
  - **[Issue #4236](HKUDS/nanobot Issue #4236)**: bwrap 在 Ubuntu 24.04 上因受限用户命名空间而静默失败。⚠️ **暂无明确 Fix PR**。
  - **[PR #4230](HKUDS/nanobot PR #4230)**: streamableHttp MCP 连接缺少超时设置，可能导致 MCP 启动无限等待。👉 **已提修复 PR**。
- 🟡 **中（安全与校验）**：
  - **[PR #4119](HKUDS/nanobot PR #4119)**: 修复相对符号链接可能导致工作区逃逸的问题。
  - **[PR #4123](HKUDS/nanobot PR #4123)**: 修复 MCP SSE/HTTP URL 未进行 SSRF 校验的问题。

## 6. 功能请求与路线图信号
从今日的 Issue 和 PR 来看，项目正在向**多模态统一、多模型调度与上下文精细化治理**方向演进：
- **UI 与可观测性**：[Issue #4233](HKUDS/nanobot Issue #4233) 请求在 WebUI 显示版本号及更新提示，当天即被 [PR #4235](HKUDS/nanobot PR #4235) 实现（含 PyPI 缓存检测）。该功能强烈依赖 UI 展示，极可能被下个版本收纳。
- **多模型/子智能体调度**：[Issue #4231](HKUDS/nanobot Issue #4231) 请求为 `spawn` 工具增加 `model` 参数，以支持子 Agent 使用不同模型（如便宜模型做初筛，强模型做决策）。这是 Multi-Agent 架构演进的关键信号。
- **全局语音输入**：[PR #4232](HKUDS/nanobot PR #4232) 将语音转录从渠道级提升为全局共享能力，并适配 WebUI/Desktop，标志着 NanoBot 向多模态输入统一架构迈进。
- **上下文动态治理**：[PR #4238](HKUDS/nanobot PR #4238) 引入 `ContextGovernor`，将微压缩与上下文压力挂钩，替代原先的固定计数，标志着 Agent 上下文管理走向动态精细化。

## 7. 用户反馈摘要
- **沙箱体验痛点**：用户在较新的 Linux 发行版（如 Ubuntu 24.04）上使用 bwrap 沙箱频繁遭遇权限或静默失败问题（[Issue #4236](HKUDS/nanobot Issue #4236)），说明沙箱的默认兼容性及错误提示仍需优化。
- **多模型成本控制**：用户希望主 Agent 和子 Agent 能使用不同模型来平衡效果与成本（[Issue #4231](HKUDS/nanobot Issue #4231)），反映出生产环境中对 Token 消耗的敏感与降本诉求。
- **版本更新感知弱**：用户希望直观看到当前版本及是否有新版（[Issue #4233](HKUDS/nanobot Issue #4233)），说明项目迭代较快，用户有跟进需求但缺乏感知通道。
- **企业 IM 场景要求苛刻**：飞书/WhatsApp 等群聊中，Bot 的 @识别和话题隔离依然是核心痛点（[Issue #2256](HKUDS/nanobot Issue #2256)），用户需要 Bot 具备更强的上下文归属感知。

## 8. 待处理积压
当前有高达 14 条待合并 PR，其中部分安全与核心逻辑修复/测试 PR 长期滞留，建议维护者重点关注：
- **安全类积压**：[PR #4123](HKUDS/nanobot PR #4123) (MCP SSRF 防护) 和 [PR #4119](HKUDS/nanobot PR #4119) (符号链接逃逸拦截) 涉及底层安全，对生产环境影响重大，需尽快排期合并。
- **核心逻辑类积压**：[PR #4190](HKUDS/nanobot PR #4190) (工具调用校验严格化) 和 [PR #4053](HKUDS/nanobot PR #4053) (只读路径写保护) 影响工具执行稳定性。
- **测试基建积压**：开发者 `yu-xin-c` 提交了多项测试脚手架 PR（如 [PR #3982](HKUDS/nanobot PR #3982), [PR #3983](HKUDS/nanobot PR #3983), [PR #4193](HKUDS/nanobot PR #4193)），这些是提升项目工程化质量的关键，需推进 Review 流程以防后续代码冲突。

</details>

<details>
<summary><strong>Hermes Agent</strong> — <a href="https://github.com/nousresearch/hermes-agent">nousresearch/hermes-agent</a></summary>

# Hermes Agent 项目动态日报 (2026-06-08)

## 1. 今日速览
今日 Hermes Agent 项目保持高度活跃，过去 24 小时内 Issues 与 PR 更新均达到 50 条，社区参与度维持高位。尽管无新版本发布，但项目当前的重心明显向**系统稳定性与跨平台兼容性**倾斜，尤其是 Windows 环境下的网关重启、DNS 解析及 RPC 端口耗尽等问题得到了密集修复。社区热点则聚焦于**Agent 互操作性（A2A 协议）与企微多账号支持**，反映出用户对 Hermes 从单机工具走向企业级多智能体协作生态的强烈诉求。

## 2. 版本发布
今日无新版本发布。

## 3. 项目进展
今日共有 6 个 PR 被合并或关闭，另有大量针对性修复 PR 进入待合并状态，项目在底层健壮性上取得实质进展：
- **测试与 CI 修复**：PR [#41334](https://github.com/NousResearch/hermes-agent/pull/41334) 修复了因鉴权逻辑加固导致的 Discord 组件测试夹具失效问题，保障了主分支的稳定性。
- **性能优化**：PR [#40607](https://github.com/NousResearch/hermes-agent/pull/40607) 修复了 yuanbao 适配器 `ws.close()` 导致网关关机卡顿 5 秒的问题，提升了网关生命周期管理的流畅度。
- **文档国际化**：PR [#40578](https://github.com/NousResearch/hermes-agent/pull/40578) 合并了乌尔都语 README 翻译，持续扩大社区的全球覆盖面。
- **批量修复推进**：PR [#41651](https://github.com/NousResearch/hermes-agent/pull/41651) 提交了 10 个低风险 Bug 的批量挽救修复（含 501 项测试通过），显著提升了问题清偿速度。

## 4. 社区热点
- **A2A 协议支持**：Issue [#514](https://github.com/NousResearch/hermes-agent/issues/514) 以 19 条评论和 18 个 👍 成为今日最热。社区对 Google A2A 协议的呼声极高，期待 Hermes 能够回答 "Who can help me?"，实现与 MCP 互补的跨 Agent 通信与发现能力。
- **纯客户端桌面版**：Issue [#38602](https://github.com/NousResearch/hermes-agent/issues/38602) 获得 8 个 👍，用户强烈希望 Desktop 应用能作为瘦客户端连接远端运行时，摆脱本地强制引导安装的束缚。
- **企微多账号网关架构**：Issue [#29144](https://github.com/NousResearch/hermes-agent/issues/29144) 提出当前微信网关单账号硬阻塞了企业多成员协作场景，亟需重构适配器架构。

## 5. Bug 与稳定性
今日报告了多个关键 Bug，部分已有对应修复 PR，按严重程度排列如下：

**P1 - 严重问题 (尚无明确 Fix PR)**：
- **会话状态静默丢失**：Issue [#41386](https://github.com/NousResearch/hermes-agent/issues/41386) 指出当 `state.db` 不可用时，CLI/Desktop 仍可正常运行，但聊天记录不会持久化，导致用户事后发现会话被截断。
- **配置迁移致工具失效**：Issue [#38798](https://github.com/NousResearch/hermes-agent/issues/38798) 显示 v25->v26 的配置迁移会将 `hermes-cli` 错误重写为不存在的 `hermes`，导致所有工具静默失效。
- **安全扫描器致 Agent 假死**：Issue [#41400](https://github.com/NousResearch/hermes-agent/issues/41400) 报告 Tirith 扫描器路径错误引发 SIGSEGV 崩溃，Agent 重试机制导致用户面临 20 分钟无响应。

**P2 - 重要问题 (部分已有 Fix PR)**：
- **Systemd 退出码异常**：Issue [#41631](https://github.com/NousResearch/hermes-agent/issues/41631) 正常停止网关时退出码为 1 导致单元 `failed` -> 已有修复 PR [#41642](https://github.com/NousResearch/hermes-agent/pull/41642)。
- **WSL 意外断开致会话崩溃**：Issue [#41645](https://github.com/NousResearch/hermes-agent/issues/41645) `wsl --shutdown` 后恢复会话触发 `MarkupError` -> 已有修复 PR [#41652](https://github.com/NousResearch/hermes-agent/pull/41652)。
- **桌面端图片发送仅传递路径**：Issue [#41556](https://github.com/NousResearch/hermes-agent/issues/41556) 粘贴图片时模型只收到文件路径，无法识别图像内容。
- **TUI 模式 MCP 工具未暴露**：Issue [#41625](https://github.com/NousResearch/hermes-agent/issues/41625) MCP 工具测试连接成功，但在 TUI 会话中不可见。

## 6. 功能请求与路线图信号
- **Mesh 集群编排**：PR [#29460](https://github.com/NousResearch/hermes-agent/pull/29460) 正在实现 `hermes mesh` CLI，基于 MQTT 进行多节点集群的配置与监控，标志着 Hermes 正式向多节点分布式演进。
- **统一插件路由选择器**：Issue [#41190](https://github.com/NousResearch/hermes-agent/issues/41190) 请求为插件提供单一 Hook 以覆盖每次 LLM 调用的 Provider/Model，解决当前路由逻辑碎片化问题，对多模型调度场景至关重要。
- **GRPO 奖励函数库**：PR [#41649](https://github.com/NousResearch/hermes-agent/pull/41649) 为技能系统添加了 29 个 GRPO 奖励函数示例，强化了 Agent 的微调与自我进化能力，可能在下个版本作为官方 Skill 模板释放。

## 7. 用户反馈摘要
- **跨平台痛点 (Windows/Linux)**：Windows 用户受到 DNS 解析失败 (Issue [#41597](https://github.com/NousResearch/hermes-agent/issues/41597))、RPC 端口耗尽 (PR [#41638](https://github.com/NousResearch/hermes-agent/pull/41638)) 和 Git-bash 媒体路径不兼容 (PR [#41643](https://github.com/NousResearch/hermes-agent/pull/41643)) 的多重困扰；Linux 原生 Wayland 用户则受困于界面闪烁 (Issue [#38015](https://github.com/NousResearch/hermes-agent/issues/38015))。
- **上下文压缩后遗症**：用户反馈 Agent 在长对话触发上下文压缩后，容易去执行摘要中的旧指令而非回应用户新输入，PR [#41650](https://github.com/NousResearch/hermes-agent/pull/41650) 正尝试解决此问题。
- **企业级部署诉求**：多账号需求不仅限于微信，也有子 Agent 角色归属记录的需求 (Issue [#41554](https://github.com/NousResearch/hermes-agent/issues/41554))，表明 Hermes 正在被应用于更复杂的团队协作场景。

## 8. 待处理积压
- **P1 状态管理缺陷**：Issue [#41386](https://github.com/NousResearch/hermes-agent/issues/41386) (state.db 不可用导致静默丢数据) 和 Issue [#38798](https://github.com/NousResearch/hermes-agent/issues/38798) (配置迁移破坏工具集) 影响核心可用性，目前尚未见官方提交修复 PR，建议维护者高优介入。
- **A2A 协议规划**：Issue [#514](https://github.com/NousResearch/hermes-agent/issues/514) 自 3 月创建至今热度不减，尚无核心成员明确表态排期，需给出明确路线图以引导社区预期。
- **大型 Feature PR 搁置**：PR [#29460](https://github.com/NousResearch/hermes-agent/pull/29460) (Mesh 集群 CLI) 提交已近 20 天，仍处于 Open 状态，需推动 Review 以免产生合并冲突。

</details>

<details>
<summary><strong>PicoClaw</strong> — <a href="https://github.com/sipeed/picoclaw">sipeed/picoclaw</a></summary>

# PicoClaw 项目动态日报 (2026-06-08)

## 1. 今日速览
PicoClaw 项目今日保持极高的维护活跃度，24 小时内关闭了 17 个 Issue 并合并了 12 个 PR，Issue 清理速度远超新增速度，展现出健康的项目治理能力。项目发布了 `v0.2.9-nightly.20260607` 版本，今日代码提交重心明显向**“防御性编程与系统健壮性”**倾斜，集中修复了多处类型断言、文件 I/O 及错误处理的盲区。同时，社区对渠道适配（Telegram、Matrix）和移动端部署（Android Termux）的关注度持续走高。

## 2. 版本发布
- **nightly: Nightly Build `v0.2.9-nightly.20260607.7d2b0c2a`**
  - **更新内容**：包含今日合并的所有防御性修复（类型断言安全检查、文件 Close 错误处理、Goroutine 泄露修复等）、Anthropic 模型 ID 修正、新增 Kagi 搜索 Provider 以及 Android Termux 文档。
  - **迁移注意**：此为自动构建的 Nightly 版本，可能存在不稳定现象，不建议生产环境直接升级，需等待 Stable 发布。
  - **完整变更日志**：[v0.2.9...main](https://github.com/sipeed/picoclaw/compare/v0.2.9...main)

## 3. 项目进展
今日合并的 12 个 PR 极大提升了系统底层的稳定性与异常处理能力，项目在健壮性方向迈出了坚实的一步：
- **防御性编程与错误处理集中落地**：合并了 6 个由 @chengzhichao-xydt 提交的修复 PR，涵盖了文件写入后 `Close()` 错误忽略导致的潜在数据损坏（[#3033](https://github.com/sipeed/picoclaw/pull/3033), [#3034](https://github.com/sipeed/picoclaw/pull/3034), [#3035](https://github.com/sipeed/picoclaw/pull/3035)）、类型断言缺乏 `ok` 检查引发的潜在 Panic（[#3046](https://github.com/sipeed/picoclaw/pull/3046), [#3040](https://github.com/sipeed/picoclaw/pull/3040)）、以及重载时的 Goroutine 泄露（[#3016](https://github.com/sipeed/picoclaw/pull/3016)）。
- **Provider 适配优化**：修复了 Anthropic 默认模型 ID 使用点号导致 API 404 的问题（[#3036](https://github.com/sipeed/picoclaw/pull/3036)）；新增了原生 Kagi 网页搜索 Provider（[#3037](https://github.com/sipeed/picoclaw/pull/3037)），丰富了 Web 搜索能力。
- **运行时与文档**：修复了消息总线背压与健康检测问题（[#2906](https://github.com/sipeed/picoclaw/pull/2906)）；增加了跳过缺失依赖二进制文件的 Skill 机制（[#2936](https://github.com/sipeed/picoclaw/pull/2936)）；正式合入了 Android Termux 部署指南（[#2902](https://github.com/sipeed/picoclaw/pull/2902)）。

## 4. 社区热点
- [Issue #2674](https://github.com/sipeed/picoclaw/issues/2674)（👍 4，💬 8）：关于 ChatGPT 后端 Codex OAuth 流式响应解析导致空回复的 Bug 讨论热烈，该问题现已关闭，表明核心 Provider 的流式解析逻辑已得到修复。
- [Issue #286](https://github.com/sipeed/picoclaw/issues/286)（👍 2，💬 8）：在 Android Termux 上运行 PicoClaw 的文档需求呼声极高，随着 PR [#2902](https://github.com/sipeed/picoclaw/pull/2902) 的合并，该长尾需求今日已圆满解决。
- [Issue #2952](https://github.com/sipeed/picoclaw/issues/2952)（💬 4）：用户集中反馈了“发版间隔长”、“QQ 频道重启循环”以及“API Key 管理界面需优化”等痛点，反映了社区对稳定发版节奏和更好 UI 交互的强烈期待。

## 5. Bug 与稳定性
**高严重度**：
- **Matrix 渠道权限失效**：`allow_from` 无法处理包含冒号的标准 Matrix ID（如 `@localpart:domain`），导致合法消息被静默拒绝。已有修复 PR [#3045](https://github.com/sipeed/picoclaw/pull/3045) 待合并。（[Issue #3044](https://github.com/sipeed/picoclaw/issues/3044)）
- **MCP 命令参数解析异常**：`mcp add` 使用了 `DisableFlagParsing`，导致全局 flag（如 `--no-color`）被误认为位置参数，破坏 http/sse 配置添加。已有修复 PR [#3048](https://github.com/sipeed/picoclaw/pull/3048) 待合并。（[Issue #3041](https://github.com/sipeed/picoclaw/issues/3041)）

**中严重度**：
- **Telegram 位置消息无响应**：Bot 完全忽略位置共享消息，且无日志输出，影响多模态交互体验。目前尚无修复 PR。（[Issue #3049](https://github.com/sipeed/picoclaw/issues/3049)）

**已修复**：
- Anthropic 模型 ID 格式错误（点号与连字符）导致 404，已通过 PR [#3036](https://github.com/sipeed/picoclaw/pull/3036) 修复。

## 6. 功能请求与路线图信号
- **Provider 生态扩充**：用户请求添加 [Omniroute](https://github.com/diegosouzapw/OmniRoute) 作为新 Provider（[Issue #2978](https://github.com/sipeed/picoclaw/issues/2978)），表明社区对多路由聚合 API 的需求在增加。
- **渠道交互体验优化**：PR [#2975](https://github.com/sipeed/picoclaw/pull/2975) 提议在 Telegram 群聊中，将“回复 Bot 消息”等同于 @提及，这符合用户自然对话直觉，有望被纳入下个版本。
- **历史记录可观测性**：PR [#3047](https://github.com/sipeed/picoclaw/pull/3047) 修复了 Web UI 会话详情无法展示已归档 JSONL 历史的问题，将大幅提升调试与回溯体验，是增强运维能力的积极信号。

## 7. 用户反馈摘要
- **移动端/边缘设备部署是刚需**：Android Termux 运行指南的极高关注度证明，将 PicoClaw 部署在廉价 ARM 设备（如树莓派、旧手机）作为常驻 AI 助手是核心用例之一。
- **QQ 渠道稳定性存疑**：用户反馈 QQ 渠道重启时会出现“无限重启循环”，必须清空上下文才能停止（[Issue #2952](https://github.com/sipeed/picoclaw/issues/2952)），该问题严重影响中文用户使用体验。
- **API 管理体验待提升**：用户希望 Web UI 能够保存已测试的 Provider Key、支持 Key 复用，并实现一键拉取 `/models` 列表添加模型，而非当前繁琐的手动填写（[Issue #2952](https://github.com/sipeed/picoclaw/issues/2952)）。
- **Agent 遵循指令能力**：有用户指出 PicoClaw 有时不太严格遵循 `agent.md` 的设定，这可能是底层 Prompt 注入优先级或上下文截断导致的退化。

## 8. 待处理积压
- **[PR #2904](https://github.com/sipeed/picoclaw/pull/2904) - Agent 循环稳定性修复**：该 PR 修复了重载时的 Goroutine 泄露与 Panic 清理问题，自 5 月 20 日开启至今处于 Stale 状态。鉴于 Agent 核心循环稳定性至关重要，建议维护者优先 Review 并推进合入。
- **[Issue #3049](https://github.com/sipeed/picoclaw/issues/3049) - Telegram 位置消息支持**：影响多模态交互的完整性，当前无人认领，需排期。
- **[Issue #2978](https://github.com/sipeed/picoclaw/issues/2978) - 增加 Omniroute Provider**：社区需求，目前仅停留在讨论阶段，需确认是否接受此类第三方路由聚合服务进入官方主分支。

*(注：今日 Issue 列表中出现大量由 @jcafeitosa 提交的关于 Binance 交易所、ClawHub 等的无效/错乱 Issue，如 [#3024-#3032](https://github.com/sipeed/picoclaw/issues/3024)，疑似自动化脚本跑偏或跨项目误提交，已全部关闭，未纳入上述分析。)*

</details>

<details>
<summary><strong>NanoClaw</strong> — <a href="https://github.com/qwibitai/nanoclaw">qwibitai/nanoclaw</a></summary>

# NanoClaw 项目动态日报 (2026-06-08)

## 1. 今日速览
NanoClaw 项目今日保持高度活跃，社区贡献与核心代码演进并行推进。过去 24 小时内，项目共处理了 9 个 PR（3 个已合并/关闭，6 个待合并）和 3 个活跃 Issue（2 个新增，1 个历史激活），无新版本发布。当前项目重心明显聚焦于**系统健壮性与安全性**：核心贡献者正着力解决账号轮换状态漂移、违规升级导致的静默损坏等底层问题；同时，社区暴露出权限越界（未设门控的 MCP 工具）与新手引导失效等痛点，亟待官方回应。整体来看，项目处于稳步迭代的基建夯实期，健康度良好但需注意 Bug 积压风险。

## 2. 版本发布
本日无新版本发布。

## 3. 项目进展
今日共有 3 个 PR 被合并或关闭，主要推进了多模型调度稳定性与升级安全机制：
- **PR [#2706](https://github.com/qwibitai/nanoclaw/pull/2706) [CLOSED]** fix(账号轮换): 限制模式并校准切换状态。修复了多模型（Codex/Gemini/Anthropic）混用时的额度通知错乱，解决了 DB 游标漂移问题，并优化了 `killGroup` 的进程清理逻辑（增加 SIGKILL 兜底）。此 PR 大幅提升了多模型账号池的调度可靠性。
- **PR [#2707](https://github.com/qwibitai/nanoclaw/pull/2707) [CLOSED]** feat(upgrade): startup tripwire + upgrade marker。引入了升级“绊线”机制，拒绝未经合法路径（如裸 `git pull`）的启动，防止因跳过迁移脚本导致的静默损坏，增强了版本升级的安全性。
- **PR [#2710](https://github.com/qwibitai/nanoclaw/pull/2710) [CLOSED]** docs(ollama): allow prompt caching。优化了 Ollama 本地化部署的文档，指导用户过滤破坏缓存的 hash，提升了本地模型接入的响应速度。

## 4. 社区热点
今日讨论最活跃的 Issue 是关于代码仓库与运行时逻辑的冲突：
- **Issue [#2312](https://github.com/qwibitai/nanoclaw/issues/2312)** [👍 0 | 评论 2]：`groups/global/CLAUDE.md` 在每次启动时被 `migrateGroupsToClaudeLocal()` 无条件删除，导致拉取代码并重启后工作树永远处于 dirty 状态。该问题虽在 5 月初就已提出，但至今仍未解决，今日再度引发社区对“代码库默认配置与启动迁移逻辑自相矛盾”的讨论，诉求是清理仓库中不该被提交的文件或修正迁移脚本。

## 5. Bug 与稳定性
今日报告的 Bug 按严重程度排列如下：

- **🔴 高危：权限越界漏洞** — **Issue [#2711](https://github.com/qwibitai/nanoclaw/issues/2711)**：`create_agent` MCP 工具虽标注为 "admin-only"，但实际上未做任何权限校验，任何容器均可创建 agent groups。这是一个严重的沙箱逃逸隐患，**目前尚无对应 fix PR**。
- **🟠 中危：新手启动挂起** — **Issue [#2703](https://github.com/qwibitai/nanoclaw/issues/2703)**：按推荐路径安装后，`cli/local` 未连通，但安装向导仍提示运行 `pnpm run chat hi`，导致命令挂起 120 秒后超时，严重影响新手首次体验。**目前尚无对应 fix PR**。
- **🟡 低危：Git 工作树污染** — **Issue [#2312](https://github.com/qwibitai/nanoclaw/issues/2312)**：启动时误删已追踪文件，影响开发体验。**暂无 fix PR**。
- **🟢 已修复：凭证代理静默回退** — **PR [#2705](https://github.com/qwibitai/nanoclaw/pull/2705)**：修复了 `use-native-credential-proxy` 在真实环境中无法绕过 OneCLI 网关的问题，待合并。
- **🟢 已修复：消息重复发送** — **PR [#2531](https://github.com/qwibitai/nanoclaw/pull/2531)**：修复了 `send_message` 在轮询中触发时产生重复文本的 Bug，待合并。

## 6. 功能请求与路线图信号
- **容器环境与网络隔离**：**PR [#2709](https://github.com/qwibitai/nanoclaw/pull/2709)** 为 `container_configs` 增加 DB 支持的 `env` 和 `blocked_hosts` 字段。该 PR 实现了维护者之前提出的 Issue #1867，属于核心架构增强，**极有可能被纳入下一版本**。
- **孤儿容器清理**：**PR [#2708](https://github.com/qwibitai/nanoclaw/pull/2708)** 增加了服务停止时 reap 孤儿容器的机制，符合当前项目专注提升容器生命周期稳定性的路线。
- **Telegram 深度集成**：**PR [#1626](https://github.com/qwibitai/nanoclaw/pull/1626)** 提出 Telegram topic isolation 与自动注册，虽非今日新增，但一直保持活跃，表明多平台多租户隔离是社区强需求方向。

## 7. 用户反馈摘要
- **新手体验割裂感强**：用户反馈官方推荐的安装路径存在断链（[#2703](https://github.com/qwibitai/nanoclaw/issues/2703)），终端提示与实际可用状态不符，导致初次尝试因无报错提示的挂起而非常受挫。
- **多模型混用状态混乱**：核心开发者反馈在同时使用 Codex/Gemini 与 Claude 时，容易收到跨模型的额度通知，且底层账号游标容易漂移（[#2706](https://github.com/qwibitai/nanoclaw/pull/2706)），说明多租户/多模型场景下的状态机管理仍需打磨。
- **本地化部署性能痛点**：Ollama 用户发现 Claude-Code-CLI 的交互机制会击穿本地提示词缓存，导致响应缓慢（[#2710](https://github.com/qwibitai/nanoclaw/pull/2710)），反映出项目在对接非 Anthropic 后端时缺乏针对性优化。

## 8. 待处理积压
- **Issue [#2711](https://github.com/qwibitai/nanoclaw/issues/2711)**：`create_agent` 权限越界是高危安全问题，需维护者立即确认并干预。
- **PR [#2531](https://github.com/qwibitai/nanoclaw/pull/2531)**：修复 poll-loop 重复文本的 PR 已提交近 20 天（05-18），今日虽有更新但迟迟未合并，建议维护者评估冲突风险后尽快推进。
- **PR [#1626](https://github.com/qwibitai/nanoclaw/pull/1626)**：Telegram topic isolation 功能 PR 已积压超 2 个月，鉴于社区对通讯渠道集成的持续关注，建议维护者给出明确路线图或合并排期。

</details>

<details>
<summary><strong>NullClaw</strong> — <a href="https://github.com/nullclaw/nullclaw">nullclaw/nullclaw</a></summary>

过去24小时无活动。

</details>

<details>
<summary><strong>IronClaw</strong> — <a href="https://github.com/nearai/ironclaw">nearai/ironclaw</a></summary>

# IronClaw 项目动态日报 (2026-06-08)

## 1. 今日速览
IronClaw 项目今日保持高度活跃，过去 24 小时内 Issues 更新达 50 条（新开/活跃 42，关闭 8），PR 更新 38 条（待合并 22，合并/关闭 16）。项目核心动能持续聚焦于 **"Reborn" 架构的深度演进与 WebUI v2 的功能补全**，安全与沙箱机制亦有多项实质性推进。今日有 16 个 PR 顺利合并或关闭，开发节奏稳健，但尚未触发新版本的正式发布。

## 2. 版本发布
今日无新版本发布。
*注：PR [#3708](https://nearai/ironclaw/pull/3708) 正在准备 `0.29.1` 版本（含 `ironclaw_common` 和 `ironclaw_skills` 的 API 破坏性变更），目前仍处于待合并状态，预计近期将正式发布。*

## 3. 项目进展
今日合并/关闭的 PR 主要推进了 WebChat v2 交互闭环、Slack 渠道基建及底层安全/CI机制的完善，项目整体向 Reborn 架构的生产可用性迈进了坚实一步：
*   **WebChat v2 功能补全**：[#4516](https://nearai/ironclaw/pull/4516) 增加了线程删除路由，补齐了基础交互功能；[#4530](https://nearai/ironclaw/pull/4530) 引入结构化的模型可见工具观察结果，优化了 Tool 调用的反馈机制。
*   **Slack 渠道基建落地**：[#4463](https://nearai/ironclaw/pull/4463) 接入了 Slack host-beta 的持久化存储，确保会话与出站状态的可靠性；[#4532](https://nearai/ironclaw/pull/4532) 增加了 Slack 允许频道选择器。
*   **Reborn 架构契约确立**：[#4511](https://nearai/ironclaw/pull/4511) 增加了出站偏好外观契约，为后续产品级工作流打下基础。
*   **工程化与稳定性**：[#3298](https://nearai/ironclaw/pull/3298) 引入 Hermetic 本地门禁，强化了代码提交前的格式/安全/正确性检查。

## 4. 社区热点
今日讨论最活跃的 Issues 集中在 Reborn 架构的核心抽象层与开发者体验上：
*   **核心架构外观设计**：[#3280](https://nearai/ironclaw/issue/3280)（7 评论）讨论在 ProductAdapters 和宿主层服务之间添加面向产品的 Reborn 工作流外观，标志着架构解耦进入深水区。
*   **声明式配置诉求**：[#3036](https://nearai/ironclaw/issue/3036)（5 评论，1 👍）作为 EPIC 级 Issue，强烈呼吁提供 Configuration-as-Code，以替代目前混乱的 `.env` 和 JSON 配置模式，反映了多租户和复杂部署场景下的真实痛点。
*   **本地开发者体验**：[#3044](https://nearai/ironclaw/issue/3044)（3 评论）探讨如何让开发者以最简命令（如 `ironclaw dev`）启动本地编码 Agent，免去繁琐的授权和挂载配置。
*   **生产环境补齐**：[#3333](https://nearai/ironclaw/issue/3333)（3 评论）指出当前 Reborn 栈仍存在内存模拟或空操作接缝，亟待补齐生产级组件和布线。

## 5. Bug 与稳定性
今日报告的问题更侧重于**架构级安全与边界防护**，传统运行时崩溃较少，多属于 P0/P1 级别的生产阻断项：
*   **[P0] 无暴露安全保障缺失**：[#3032](https://nearai/ironclaw/issue/3032) 阻断 Reborn 上线，指出当前缺乏防止敏感数据跨越公共/模型可见边界的防护层。暂无对应 fix PR。
*   **[P0] 审批租约越权风险**：[#3609](https://nearai/ironclaw/issue/3609) 指出 WebUI Beta 中的审批租约解析过于信任 UI 提供的衰减值，可能被恶意扩大权限。
*   **[P0] 调度权限未密封**：[#3608](https://nearai/ironclaw/issue/3608) 指出当前调度边界仍依赖代码审查，缺乏不透明的权限证明。
*   **[修复中] 扩展凭证暂存问题**：[#4492](https://nearai/ironclaw/pull/4492) 正在修复本地开发环境下配置的扩展凭证暂存问题，涉及 DB 迁移，属于高风险修复。

## 6. 功能请求与路线图信号
从最新 Issues 和活跃 PRs 中，可以识别出以下明确的路线图信号：
*   **技能系统增强**：PR [#4527](https://nearai/ironclaw/pull/4527) 增加了用户范围的技能设置 UI，PR [#4531](https://nearai/ironclaw/pull/4531) 优化了技能的渐进式披露机制，预计“可发现与可管理技能”将成为下一版本的核心卖点。
*   **WebUI SSO 对等**：Issue [#4116](https://nearai/ironclaw/issue/4116) 要求将 v1 的 Google/GitHub/NEAR SSO 登录引入 WebChat v2，这是 WebUI Beta 达到生产可用的必经之路。
*   **OpenAI API 兼容层迁移**：Issue [#3283](https://nearai/ironclaw/issue/3282) 计划将 OpenAI 兼容 API 迁移至 Reborn 模型，将极大便利现有第三方应用无缝接入 IronClaw Reborn。

## 7. 用户反馈摘要
从 Issue 描述与评论中提炼出真实痛点如下：
*   **配置体验割裂**：运营人员和开发者目前必须混合手动编辑 `.env`、工作区文档、设置 JSON 和运行时标志，缺乏 Schema、Diff 和审计追踪（来源：[#3036](https://nearai/ironclaw/issue/3036)）。
*   **本地启动门槛高**：工程师在本地运行 IronClaw 作为编码 Agent 时，需要手动配置授权、挂载、网络策略等，过程繁琐（来源：[#3044](https://nearai/ironclaw/issue/3044)）。
*   **错误信息过于保守**：Reborn 运行时错误目前出于安全考虑输出极为保守，导致模型和开发者难以进行有效的故障恢复（来源：[#4059](https://nearai/ironclaw/issue/4059)）。

## 8. 待处理积压
以下重要 Issue/PR 长期悬而未决或需维护者重点关注：
*   **停滞的发布 PR**：[#3708](https://nearai/ironclaw/pull/3708) 自 5 月 16 日创建以来一直 OPEN，包含多个 crate 的破坏性变更（0.29.1 版本），可能阻塞了下游依赖更新。
*   **大规模依赖更新待审**：[#4503](https://nearai/ironclaw/pull/4503) 涉及 38 个依赖项更新（含 `agent-client-protocol` 大版本升级），风险较高，需核心贡献者尽快 Review。
*   **关键安全 Hook 跟进**：由 @zmanian 提出的多个安全相关 Issue（如 [#3956](https://nearai/ironclaw/issue/3956) FS-hardening 跟进、[#3957](https://nearai/ironclaw/issue/3957) 第三方激活加固）目前仅 1 评论，缺乏明确的排期与认领，可能在多租户生产环境中留下隐患。

</details>

<details>
<summary><strong>LobsterAI</strong> — <a href="https://github.com/netease-youdao/LobsterAI">netease-youdao/LobsterAI</a></summary>

# LobsterAI 项目动态日报 (2026-06-08)

## 1. 今日速览
过去 24 小时，LobsterAI 项目的社区讨论保持一定活跃度（15 条 Issue 更新），但开发端处于停滞状态，无任何 PR 合并、关闭或新版本发布。值得注意的是，今日活跃的 14 条 Issue 均带有 `[stale]` 标签且源自两个月前（4月7日），仅 1 条为今日新发，且 0 个 Issue 被关闭。项目当前呈现出“社区痛点持续积压、核心功能维护迟缓”的状态，健康度亟待提升，维护者需尽快介入处理积压问题。

## 2. 版本发布
无新版本发布。

## 3. 项目进展
今日无任何已合并或已关闭的 PR，项目代码库与功能向前推进度为 0。这表明项目近期缺乏代码层面的维护动作，大量 Bug 修复与功能迭代仍处于悬而未决的状态。

## 4. 社区热点
今日讨论最活跃的是 [#1509](https://github.com/netease-youdao/LobsterAI/issues/1509)（2 条评论），核心诉求集中在 **AI 执行过程的“黑盒”体验**与**模型理解能力**上。用户反馈在使用 Skill 生成时遭遇长时间阻塞，且无中间思考过程展示，导致用户无法感知系统状态；同时指出同模型在竞品中理解能力更佳。
此外，大量 `[stale]` Issue（如 [#1500](https://github.com/netease-youdao/LobsterAI/issues/1500), [#1502](https://github.com/netease-youdao/LobsterAI/issues/1502), [#1516](https://github.com/netease-youdao/LobsterAI/issues/1516)）在今天产生了评论更新，多由于自动机器人标记过期或社区用户的追评，反映出用户对状态不同步、OAuth 认证丢失等核心交互问题的持续关注。

## 5. Bug 与稳定性
今日报告及活跃的 Bug 较多，按严重程度排列如下（目前均无对应 fix PR）：

- **严重（影响核心流程或资损）**：
  - [#2121](https://github.com/netease-youdao/LobsterAI/issues/2121)（新开）：AI 重复输出文字，用户怀疑大量消耗 Token，直接造成资损与体验恶化。
  - [#1516](https://github.com/netease-youdao/LobsterAI/issues/1516)：关闭 Settings 面板时未取消 GitHub Copilot OAuth 轮询，导致认证成功后 Token 静默丢失，严重阻断第三方接入流程。
  - [#1509](https://github.com/netease-youdao/LobsterAI/issues/1509)：Skill 生成长时间阻塞无反馈，用户完全无法进行下一步操作。

- **中等（状态不一致与逻辑漏洞）**：
  - [#1500](https://github.com/netease-youdao/LobsterAI/issues/1500)：禁用技能后未从 `activeSkillIds` 移除，导致提示词污染。
  - [#1502](https://github.com/netease-youdao/LobsterAI/issues/1502)：保存技能列表后当前会话未同步，需切换 Agent 才生效。
  - [#1506](https://github.com/netease-youdao/LobsterAI/issues/1506)：定时任务 IM 通知未选会话即可提交，导致运行时通知静默失败。
  - [#1512](https://github.com/netease-youdao/LobsterAI/issues/1512)：QQ Bot 白名单模式缺少 UI 输入框，功能事实性失效。

- **低（校验与 UI 细节）**：
  - [#1504](https://github.com/netease-youdao/LobsterAI/issues/1504)：POPO 的 AES Key 缺失必填校验。
  - [#1513](https://github.com/netease-youdao/LobsterAI/issues/1513)：声明条款排版与序号不规范。

## 6. 功能请求与路线图信号
用户对“信息与数据管理”提出了强烈的高阶需求，这些信号表明 LobsterAI 的用户正在从“尝鲜”向“重度生产力工具”过渡：
- **分类与检索体系**：[#1541](https://github.com/netease-youdao/LobsterAI/issues/1541) 请求会话标签分类与筛选；[#1525](https://github.com/netease-youdao/LobsterAI/issues/1525) 请求会话颜色标注。
- **内容沉淀与导出**：[#1537](https://github.com/netease-youdao/LobsterAI/issues/1537) 请求对重要 AI 回复增加书签/收藏功能；[#1528](https://github.com/netease-youdao/LobsterAI/issues/1528) 请求批量导出会话记录。
- **数据看板**：[#1532](https://github.com/netease-youdao/LobsterAI/issues/1532) 请求增加本地会话使用统计面板。

**路线图判断**：由于上述功能请求均处于 `[stale]` 状态且无任何关联 PR，短期内被纳入下一版本的概率极低。但这明确指出了产品未来的演进方向——构建更强大的会话生命周期管理能力。

## 7. 用户反馈摘要
- **核心痛点：缺乏过程感知**。用户对 AI 长时间无反馈的“黑盒”状态容忍度极低（#1509），同时对异常输出（如重复字符）导致的 Token 消耗极其敏感（#2121）。
- **核心痛点：前后端状态割裂**。多处反馈指出前端 UI 状态与底层数据（Redux/SQLite）未同步（#1500, #1502, #1516），严重影响操作的确定性和信任感。
- **使用场景**：重度用户已积累大量会话，迫切需要从“线性时间流”管理升级为“多维标签+颜色+收藏”的知识库式管理；同时存在通过 IM 机器人（钉钉、飞书、QQ）进行自动化任务推送的强场景诉求。

## 8. 待处理积压
项目当前存在严重的 Issue 积压，以下关键问题自 2026-04-07 创建以来已长达 2 个月未获解决，且今日仍未被官方回应或关闭，提醒维护者重点关注：
- **状态同步类**：[#1500](https://github.com/netease-youdao/LobsterAI/issues/1500)、[#1502](https://github.com/netease-youdao/LobsterAI/issues/1502)
- **认证阻断类**：[#1516](https://github.com/netease-youdao/LobsterAI/issues/1516)
- **集成失效类**：[#1506](https://github.com/netease-youdao/LobsterAI/issues/1506)、[#1512](https://github.com/netease-youdao/LobsterAI/issues/1512)
- **CI/CD 基建类**：[#1518](https://github.com/netease-youdao/LobsterAI/issues/1518)（Labeler 权限与 Lint 策略问题，直接影响社区贡献体验）

建议维护团队优先处理状态同步与 Token 消耗相关的高优先级 Bug，并清理过期 Issue，以恢复社区信心。

</details>

<details>
<summary><strong>TinyClaw</strong> — <a href="https://github.com/TinyAGI/tinyagi">TinyAGI/tinyagi</a></summary>

过去24小时无活动。

</details>

<details>
<summary><strong>Moltis</strong> — <a href="https://github.com/moltis-org/moltis">moltis-org/moltis</a></summary>

# Moltis 项目动态日报 (2026-06-08)

## 1. 今日速览
过去 24 小时内，Moltis 项目整体呈现“稳步推进、聚焦优化”的态势。项目活跃度适中，共有 3 个 PR 和 1 个 Issue 更新，但尚无代码合并或版本发布。核心开发者 s-salamatov 近期极为活跃，今日更新的 3 个 PR 均由其提交，重点围绕 Telegram 流式输出修复、上下文重载性能优化及日志可见性管控。社区端，移动端 Web UI 的交互体验成为关注焦点。项目当前健康度良好，处于功能细化和稳定性打磨阶段。

## 2. 版本发布
今日无新版本发布。

## 3. 项目进展
今日虽无 PR 被合并或 Issue 被关闭，但 3 个处于 OPEN 状态的 PR 正在积极推进项目的底层稳定性与可控性：
- **[PR #1113](https://github.com/moltis-org/moltis/pull/1113)**: 修复了 Telegram 流式输出在关闭完成通知时的边界 Bug，保障了端侧消息推送的完整性。
- **[PR #1089](https://github.com/moltis-org/moltis/pull/1089)**: 对持久化的工具调用结果进行截断限制，覆盖了常规对话、流式、重试、内存轮次等多个核心场景，这将显著改善长上下文重载时的 Token 消耗与性能表现。
- **[PR #1093](https://github.com/moltis-org/moltis/pull/1093)**: 引入了细粒度的频道活动日志可见性设置（支持 all/errors_only/off），完善了多租户/多频道场景下的权限与隐私控制体系。

## 4. 社区热点
今日最受关注的社区动态为移动端体验的改进诉求：
- **[Issue #1107](https://github.com/moltis-org/moltis/issues/1107)**：请求在移动端 Web UI 中支持多行文本输入。该 Issue 在创建后两日内即获得活跃跟进，表明移动端用户对输入交互体验的敏感度较高。在 AI 助手场景中，长 Prompt 的编写和多轮对话编辑是刚需，单行输入严重制约了移动端的可用性。

## 5. Bug 与稳定性
今日报告/修复了 1 项影响业务流程的 Bug：
- **[中等] Telegram 流式输出最终回复丢失**：当启用 Telegram 流式输出但关闭完成通知时，最终回答未能作为流式最终回复处理，导致用户收不到完整回答。目前已有对应的修复 PR ([PR #1113](https://github.com/moltis-org/moltis/pull/1113))，等待合并。
- **[潜在] 长上下文重载导致 Token 突破限制**：[PR #1089](https://github.com/moltis-org/moltis/pull/1089) 暴露出此前持久化的 `tool` 和 `tool_result` 在重载时未做上限控制，可能在复杂 Agent 任务中引发上下文溢出或成本飙升，目前修复 PR 待合并。

## 6. 功能请求与路线图信号
- **移动端体验完善**：[Issue #1107](https://github.com/moltis-org/moltis/issues/1107) 的多行输入需求，反映出项目在桌面端与移动端体验上存在差距。结合此前已合并的 UI 相关 PR，移动端适配大概率是下一阶段的前端重点。
- **企业级权限与审计控制**：[PR #1093](https://github.com/moltis-org/moltis/pull/1093) 引入的活动日志可见性分级（账户级/频道级/用户级覆盖），释放出项目正在向团队协作和企业级部署场景演进的信号，精细化权限控制有望在近期版本落地。

## 7. 用户反馈摘要
从 [Issue #1107](https://github.com/moltis-org/moltis/issues/1107) 的反馈可以提炼出以下用户痛点：
- **移动端输入受限**：用户在移动端 Web UI 编写复杂 Prompt 或进行多行文本粘贴时体验不佳，单行输入框无法满足深度交互需求，这降低了用户在移动场景下使用 Moltis 的意愿。

## 8. 待处理积压
- **[PR #1089](https://github.com/moltis-org/moltis/pull/1089)** 与 **[PR #1093](https://github.com/moltis-org/moltis/pull/1093)** 均已超过 4 天未被合并，且涉及底层会话重载与权限逻辑，建议维护者尽快安排 Code Review，避免功能分支积压导致后续冲突。
- **[PR #1113](https://github.com/moltis-org/moltis/pull/1113)** 作为 Hotfix，直接影响 Telegram 渠道的正常使用，需优先合并并考虑发布补丁版本。

</details>

<details>
<summary><strong>CoPaw</strong> — <a href="https://github.com/agentscope-ai/CoPaw">agentscope-ai/CoPaw</a></summary>

# CoPaw (QwenPaw) 项目动态日报 - 2026-06-08

## 1. 今日速览
CoPaw (QwenPaw) 项目今日整体活跃度保持在中高水平，社区讨论热度高于代码合并进度。过去 24 小时内新增 5 条活跃 Issue 且 0 条关闭，同时 2 条待处理 PR 均未合并，且无新版本发布。项目当前焦点集中在多模态视觉能力的解耦配置、本地大模型对接的稳定性回归，以及 Agent 记忆系统架构的进化诉求上。最新版本 V1.1.10 在本地私有化部署场景下暴露出一定的兼容性风险，需维护者重点跟进。

## 2. 版本发布
今日无新版本发布。

## 3. 项目进展
今日无 PR 被合并或关闭，项目代码库整体未发生实质性向前推进。目前有 2 条活跃 PR 正在审核与等待合并：
- **[PR #4995](https://github.com/agentscope-ai/QwenPaw/pull/4995)**：由首次贡献者提交，修复了频道渲染器中工具输出丢失的问题，优化了关闭 `show_tool_details` 时的体验，属于重要的体验修复。
- **[PR #4949](https://github.com/agentscope-ai/QwenPaw/pull/4995)**：扩展了 ACP（Agent Client Protocol）服务端能力，为终端 UI 等第三方客户端提供更丰富的元数据支持，该 PR 已处于审核状态，若合并不仅提升协议兼容性，也将丰富 CoPaw 的前端生态。

## 4. 社区热点
今日讨论最活跃的 Issue 均围绕“模型能力扩展”与“本地部署可用性”展开：
- **[Issue #4992](https://github.com/agentscope-ai/QwenPaw/issues/4992) feat: 支持独立视觉模型配置**（👍 0，评论 2）：社区对“视觉中转站”概念反响热烈。用户希望将视觉能力与主模型解耦，在纯文本模型（如 deepseek-v4-flash）下通过挂载独立视觉模型来处理图片。这反映出用户对模块化、异构模型组合调用的强烈诉求。
- **[Issue #4989](https://github.com/agentscope-ai/QwenPaw/issues/4989) [Bug]: 1.1.9 & 1.1.10版本本地部署千问3.6-27B无响应**（👍 0，评论 2）：多位用户确认在新版本中通过 vLLM 本地部署的模型出现连接成功但对话转圈无响应的回归问题，严重打击了本地私有化部署用户的体验。

## 5. Bug 与稳定性
今日报告了 2 个明确的 Bug，按严重程度排列如下：
1. **[严重] 核心对话功能回归**：[Issue #4989](https://github.com/agentscope-ai/QwenPaw/issues/4989) - 升级至 1.1.9/1.1.10 后，vLLM 本地部署模型对话无响应且无后台报错，属于阻断性 Bug，**暂无 Fix PR**。
2. **[轻微] 前端交互异常**：[Issue #4993](https://github.com/agentscope-ai/QwenPaw/issues/4993) - V1.1.10 macOS 客户端图片预览放大拖动时严重抖动，影响多模态交互体验，**暂无 Fix PR**。

## 6. 功能请求与路线图信号
今日的功能请求为项目架构演进提供了明确信号：
- **异构模型调用架构**：[Issue #4992](https://github.com/agentscope-ai/QwenPaw/issues/4992) 提出的 `visual_model` 独立配置，结合正在审核的 [PR #4949](https://github.com/agentscope-ai/QwenPaw/pull/4949)（ACP 协议扩展），预示着 CoPaw 可能正在向“多模型协同/中转调度”的 Agent 架构演进，未来版本极有可能纳入视觉/语音等独立模型路由机制。
- **分层记忆系统**：[Issue #4994](https://github.com/agentscope-ai/QwenPaw/issues/4994) 呼吁引入支持自进化的分层记忆框架。这表明当前的记忆管理已无法满足复杂长程 Agent 任务的需求，可能需要项目组在底层重构记忆存储与检索逻辑。

## 7. 用户反馈摘要
- **本地部署兼容性痛点**：用户倾向使用 vLLM 等工具托管本地模型，但近期版本对标准 OpenAI 协议的兼容性似乎出现倒退，且缺乏有效的错误提示（无后台日志），增加了排查难度。
- **“不求全能，但求好用”**：用户并不强求单一模型具备所有能力，而是希望平台层提供灵活的“插拔式”能力补充（如主模型纯文本 + 视觉模型辅助）。
- **Agent 基础设施期待**：用户对 CoPaw 的定位不仅停留在聊天前端，更期待其具备高级 Agent 特性（如分层记忆、自进化），对系统智能化要求提升。

## 8. 待处理积压
- **[紧急待响应] 本地模型对话回归**：[Issue #4989](https://github.com/agentscope-ai/QwenPaw/issues/4989) 自 06-06 报告至今未被 Close，由于后台无报错，定位困难，严重阻碍了相关用户的版本升级，需维护者尽快介入排查 1.1.9 版本引入的变更。
- **[长期审查] ACP 协议扩展 PR**：[PR #4949](https://github.com/agentscope-ai/QwenPaw/pull/4949) 自 06-03 提交至今已 5 天，涉及 Agent 通信协议的底层扩展，体量较大，目前处于 "Under Review" 状态，需避免因审查周期过长导致合并冲突或社区贡献者流失。

</details>

<details>
<summary><strong>ZeptoClaw</strong> — <a href="https://github.com/qhkm/zeptoclaw">qhkm/zeptoclaw</a></summary>

过去24小时无活动。

</details>

<details>
<summary><strong>ZeroClaw</strong> — <a href="https://github.com/zeroclaw-labs/zeroclaw">zeroclaw-labs/zeroclaw</a></summary>

# ZeroClaw 项目动态日报 (2026-06-08)

## 1. 今日速览
过去 24 小时，ZeroClaw 项目保持高度活跃，共处理 50 条 Issue 更新（17 条关闭）和 50 条 PR 更新（11 条合并/关闭）。项目当前处于 **v0.8.0 版本的发布前夕**，核心维护者 `singlerider` 提交了发布准备 PR (#7364)，并集中合并了多项针对 TUI 客户端 的重大 UX 重构。社区侧，多智能体路由、A2A 协议支持及 Token 消耗优化等高级特性引发热烈讨论，但 Web Dashboard 不可用及 Docker 环境下的严重 Bug 仍需警惕。

## 2. 版本发布
今日无正式新版本发布，但已进入 **v0.8.0 的发布倒计时**：
- **[PR #7364](https://github.com/zeroclaw-labs/zeroclaw/pull/7364) `chore(release): release v0.8.0`**：维护者已创建发布准备分支，主要修复了 `--no-default-features` 编译下的 `unused_imports` 警告。结合近期合并的 Schema v3 架构重构及 UI 大改版，v0.8.0 将是一次大版本更新。

## 3. 项目进展
今日合并了多个高价值 PR，显著推进了 TUI 交互体验与 Provider 兼容性：
- **TUI 交互彻底重构**：
  - [PR #7190](https://github.com/zeroclaw-labs/zeroclaw/pull/7190)：引入带侧边栏的出站消息队列，解决了 Agent 思考期间用户输入被硬阻塞的痛点。
  - [PR #7209](https://github.com/zeroclaw-labs/zeroclaw/pull/7209)：支持会话内使用 `/model` 命令实时切换模型和 Provider，无需退出重配。
  - [PR #7249](https://github.com/zeroclaw-labs/zeroclaw/pull/7249)：主题系统增强，支持 24位色深回退及按 Agent 覆盖调色板，修复了老终端乱码问题。
- **Provider 容错与扩展**：
  - [PR #7178](https://github.com/zeroclaw-labs/zeroclaw/pull/7178)：引入按别名声明的 Model-Provider 级别故障转移链，替代了旧版全局回退机制，极大提升多模型调配的可靠性（呼应 Issue #4647）。
  - [PR #7315](https://github.com/zeroclaw-labs/zeroclaw/pull/7315)：修复 Bedrock 在非 Claude/Nova 模型下因强行插入 Prompt Caching 导致的 400 错误。

## 4. 社区热点
- **[Issue #4866](https://github.com/zeroclaw-labs/zeroclaw/issues/4866) `[Bug]: Web dashboard is still not available`** (👍0, 评论28)
  - **分析**：今日评论数最高的 Issue。用户普遍反馈 Web UI 和 Tauri 桌面端持续提示需手动 build，跨版本未解决。虽然已 Close，但反映出前后端剥离后的构建引导对新手极不友好。
- **[Issue #2767](https://github.com/zeroclaw-labs/zeroclaw/issues/2767) `[Feature]: Multi-Agent Routing`** (👍9, 评论6)
  - **分析**：点赞数最高。社区强烈需要单 Gateway 下多 Agent 隔离工作区+多渠道账号路由的能力，对标 OpenClaw 架构，是企业级应用的核心诉求。
- **[Issue #3566](https://github.com/zeroclaw-labs/zeroclaw/issues/3566) `[Feature]: A2A Protocol Support`** (👍7, 评论6)
  - **分析**：社区对 Linux 基金会主导的 Agent-to-Agent 协议互通呼声很高，希望 ZeroClaw 能与外部 Agent 生态互联互通。
- **[Issue #2503](https://github.com/zeroclaw-labs/zeroclaw/issues/2503) `[Feature]: where is napcat channel`** (👍0, 评论9)
  - **分析**：国内用户对 QQ/OneBot 协议接入需求旺盛，目前缺少 Napcat 渠道选项。

## 5. Bug 与稳定性
按严重程度排列当前关键 Bug：
- **S0 - 数据丢失/安全风险**：
  - [Issue #4627](https://github.com/zeroclaw-labs/zeroclaw/issues/4627)：Docker 环境下 `file_write` 工具返回成功但宿主机文件不可见（沙箱隔离导致隐式写入失败）。⚠️ **暂无 Fix PR**。
- **S1 - 工作流阻塞**：
  - [Issue #4879](https://github.com/zeroclaw-labs/zeroclaw/issues/4879)：Gemini CLI OAuth 认证后调用依然报错 `rate_limited`，该 Provider 基本不可用。⚠️ **暂无 Fix PR**。
  - [Issue #4873](https://github.com/zeroclaw-labs/zeroclaw/issues/4873)：飞书集成后默认仅调用 LLM 而非走 Agent 逻辑，导致技能无法触发。⚠️ **暂无 Fix PR**。
- **S2 - 降级/安全绕过**：
  - [Issue #5122](https://github.com/zeroclaw-labs/zeroclaw/issues/5122) (已关闭)：`web_fetch` 的私有 IP 限制可被域名解析绕过 (SSRF 风险)。
  - [Issue #4832](https://github.com/zeroclaw-labs/zeroclaw/issues/4832)：高熵检测器误杀微信文件名和 MD5，需提供关闭选项。

## 6. 功能请求与路线图信号
- **Token 消耗优化**：[Issue #5146](https://github.com/zeroclaw-labs/zeroclaw/issues/5146) 提出 Skill Compilation。当前每次调用工具都需发送 400+ 行的 SKILL.md，通过编译为紧凑指令可大幅降低 Token 消耗，这可能成为 v0.8.x 后的性能优化重点。
- **沙箱与安全隔离**：[Issue #6293](https://github.com/zeroclaw-labs/zeroclaw/issues/6293) RFC 提出气隙执行模式，通过 Unix Socket 拆分在线/离线进程，结合 MCP 实现企业级安全隔离。
- **架构升级信号**：[PR #7260](https://github.com/zeroclaw-labs/zeroclaw/pull/7260) 正在为 v0.8.0 的 Schema v3 架构批量添加 7 种 OpenAI 兼容 Provider，表明新版本的 Provider 注册架构已完全模板化。

## 7. 用户反馈摘要
- **Docker 部署门槛过高**：用户在 [Issue #3642](https://github.com/zeroclaw-labs/zeroclaw/issues/3642) 抱怨为控制内存关闭了诸多 Feature (如 WhatsApp)，导致非技术用户自建镜像困难，呼吁提供 All-in-One 的 "full" 镜像。
- **渠道模式上下文丢失**：[Issue #4827](https://github.com/zeroclaw-labs/zeroclaw/issues/4827) 和 [Issue #4880](https://github.com/zeroclaw-labs/zeroclaw/issues/4880) 反映，Agent 在 Channel (Telegram/QQ等) 中运行时，中间 Tool 调用结果被丢弃，且压缩机制不触发，导致多轮对话失忆。
- **配置逻辑不符合直觉**：[Issue #5803](https://github.com/zeroclaw-labs/zeroclaw/issues/5803) 指出 Fallback 链竟忽略配置文件仅读环境变量，暴露了重连机制的设计盲区（现已修复）。

## 8. 待处理积压
- **[Issue #4627](https://github.com/zeroclaw-labs/zeroclaw/issues/4627) (S0 Bug)**：Docker 沙箱文件写入静默失败，严重违背用户预期，需维护者尽快明确文件持久化边界或给出配置指引。
- **[Issue #4879](https://github.com/zeroclaw-labs/zeroclaw/issues/4879) (S1 Bug)**：Gemini OAuth 不可用，阻碍了白嫖/轻量用户群体的增长，需 Provider 模块优先排查。
- **[PR #7260](https://github.com/zeroclaw-labs/zeroclaw/pull/7260) & [PR #7262](https://github.com/zeroclaw-labs/zeroclaw/pull/7262)**：7 个新 Provider 及其文档的 Stack PR，等待合并，可能作为 v0.8.0 的发布前置依赖。
- **[Issue #4710](https://github.com/zeroclaw-labs/zeroclaw/issues/4710)**：Logo 重设计，当前状态为 `blocked/needs-author-action`，需原作者确认版权或移交社区推进。

</details>

---
*本日报由 [agents-radar](https://github.com/yanzi6039/agents-radar) 自动生成。*