# OpenClaw 生态日报 2026-06-07

> Issues: 311 | PRs: 500 | 覆盖项目: 13 个 | 生成时间: 2026-06-07 14:39 UTC

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

# OpenClaw 项目动态日报 (2026-06-07)

## 1. 今日速览
OpenClaw 项目今日保持高度活跃，社区互动与代码迭代频繁。过去24小时内共处理 311 条 Issue（新开/活跃 173 条，关闭 138 条，关闭率达 44.3%），PR 更新高达 500 条（合并/关闭 119 条，待合并 381 条），显示项目正处于快速开发与密集修整期。项目今日发布了 `v2026.6.5-beta.2` 版本，重点修复了模型推理内容泄漏至消息渠道的严重 UX 问题。总体而言，项目健康度良好，但多渠道适配、Cron 隔离性及上下文压缩仍是当前的痛点与焦点。

## 2. 版本发布
- **版本号**: [v2026.6.5-beta.2](https://github.com/openclaw/openclaw/releases/tag/v2026.6.5-beta.2)
- **核心更新**:
  - **QQBot 推理剥离**: QQBot 渠道在原生投递前现会剥离模型推理/思考脚手架，防止原始 `<thinking>` 内容泄漏到频道回复中（关联 #89913, #90132，感谢 @openperf）。
  - **MCP 协议增强**: MCP 工具结果现在对 `resource_link`、`resource`、`audio`、格式错误的图像及未来未知类型进行强制转换，提升了工具调用的鲁棒性。
- **迁移注意**: 作为 beta 版本，暂无破坏性变更声明，但由于修改了消息投递的核心过滤逻辑，依赖多渠道输出的生产环境建议观察后再行升级。

## 3. 项目进展
今日项目合并/关闭了 119 个 PR，整体推进显著，主要集中在多渠道稳定性、内存系统优化与安全加固上：
- **多渠道消息与重连修复**：QQBot WebSocket 重连后出站插件解析失败的问题已有修复 PR ([#89038](https://github.com/openclaw/openclaw/pull/89038)) 进入 Maintainer 评审；飞书渠道增加了发送限速 (230020/230006) 的重试机制 ([#89659](https://github.com/openclaw/openclaw/pull/89659))。
- **Cron 与内存系统优化**：修复了 Cron 任务污染全局认证状态的严重 Bug ([#91187](https://github.com/openclaw/openclaw/pull/91187))；Dreaming 语料库现在会排除已归档的会话记录，防止噪声污染分析结果 ([#91182](https://github.com/openclaw/openclaw/pull/91182), [#90433](https://github.com/openclaw/openclaw/pull/90433))；Deep Sleep 阶段现在能正确将摘要写入 `DREAMS.md` ([#91188](https://github.com/openclaw/openclaw/pull/91188))。
- **安全与体验加固**：修复了 CLI 配置网关时 Token 和密码明文显示的漏洞 ([#91135](https://github.com/openclaw/openclaw/pull/91135))；限制了原生 Hook Relay 进程生命周期以防止僵尸进程积累 ([#91147](https://github.com/openclaw/openclaw/pull/91147))。

## 4. 社区热点
今日社区讨论最热烈的问题集中在**智能体内部状态泄漏至消息渠道**以及**Cron 任务引发的全局状态污染**：
- [#25592](https://github.com/openclaw/openclaw/issues/25592) (27评): 工具调用间的文本（如错误处理、内部独白）泄漏到 Slack/iMessage 等消息渠道。此痛点直接推动了 v2026.6.5-beta.2 的发布，但通用通道的修复仍需等待。
- [#90991](https://github.com/openclaw/openclaw/issues/90991) (12评): Cron 计划任务触发后污染全局运行时状态，导致系统级瞬时过载，交互式会话受牵连。维护者 @openperf 已迅速响应并提交修复 PR。
- [#88312](https://github.com/openclaw/openclaw/issues/88312) (14评): Codex app-server 在多工具轮次中出现 turn-completion 停顿的回归 Bug，引发大量企业版用户吐槽。
- [#29387](https://github.com/openclaw/openclaw/issues/29387) (14评): `agentDir` 下的 Bootstrap 文件（SOUL.md 等）被静默忽略，严重影响多智能体自定义人格的配置生效。

## 5. Bug 与稳定性
今日报告的 P1 级 Bug 及回归问题较多，系统稳定性和隔离性面临挑战：
- **P1 严重未修复**：
  - [#90639](https://github.com/openclaw/openclaw/issues/90639): Safeguard compaction 模式失效，会话上下文暴涨至 200K+ tokens 后直接崩溃报错，且在 Slack 渠道无法自动恢复。（暂无 Fix PR）
  - [#29387](https://github.com/openclaw/openclaw/issues/29387): `agentDir` 配置无效，导致系统提示词注入失败。
- **P1 回归问题**：
  - [#88312](https://github.com/openclaw/openclaw/issues/88312): Codex 轮次完成停滞回归。
  - [#31583](https://github.com/openclaw/openclaw/issues/31583): `exec` 工具未继承 `skills.entries.*.env` 环境变量，导致无法向子进程注入密钥（已有 linked PR）。
  - [#90428](https://github.com/openclaw/openclaw/issues/90428): WSL2 环境下 `exec` 工具触发网关 SIGTERM 重启。
- **P1 已确认修复中**：
  - [#90991](https://github.com/openclaw/openclaw/issues/90991): Cron 污染全局状态 (Fix PR: [#91187](https://github.com/openclaw/openclaw/pull/91187))。
  - [#68113](https://github.com/openclaw/openclaw/issues/68113): Mattermost 斜杠命令 503 错误 (已修复并关闭)。

## 6. 功能请求与路线图信号
社区与贡献者正在推动多智能体协同与多模态扩展的边界，以下信号值得重点关注：
- **多模态/新渠道**：PR [#91176](https://github.com/openclaw/openclaw/pull/91176) 添加了 Microsoft Teams 提供商（支持语音、入站视频和回呼），这是项目在 Voice-call 扩展上的重大进展。
- **多智能体上下文隔离**：Issue [#90916](https://github.com/openclaw/openclaw/issues/90916) 提出为单一助手建立“Topic-session families”，允许多个命名上下文通道共享持久记忆但隔离近期对话，此设计如被采纳将大幅提升复杂 Agent 的并发管理能力。
- **子智能体生命周期**：Issue [#22358](https://github.com/openclaw/openclaw/issues/22358) 请求添加 `post_subagent_complete` 钩子，PR [#78441](https://github.com/openclaw/openclaw/pull/78441) 已实现子智能体的 `toolsAllow` 转发，两者结合预示多智能体编排将更细粒度。
- **轻量化推理优化**：Issue [#33962](https://github.com/openclaw/openclaw/issues/33962) 建议使用轻量模型替代主模型生成 slug 文件名，以缓解通道拥堵和成本。

## 7. 用户反馈摘要
从评论和 Issue 描述中提炼出以下真实场景痛点：
- **"内心戏"外露**：用户非常反感智能体的内部推理（如报错处理、中间步骤）直接发送到 Slack/Telegram 等聊天窗口（[#25592](https://github.com/openclaw/openclaw/issues/25592), [#87326](https://github.com/openclaw/openclaw/issues/87326)），v2026.6.5-beta.2 的 QQBot 修复获得了积极反响，但期待全渠道统一解决。
- **Cron 任务的数据破坏力**：隔离的 Cron 会话经常覆盖共享工作区文件（[#40001](https://github.com/openclaw/openclaw/issues/40001)），用户迫切需要 Write 工具的 Append（追加）模式。
- **长对话与上下文崩溃**：Safeguard compaction 触发太晚，导致长对话直接卡死，用户需频繁手动 `/new`（[#90639](https://github.com/openclaw/openclaw/issues/90639)）。
- **多端状态不同步**：在一端（如 Telegram）执行 `/new`，TUI 或 WebChat 端未刷新，仍在旧上下文（[#38966](https://github.com/openclaw/openclaw/issues/38966)）。

## 8. 待处理积压
以下高优先级（P1/P2）Issue 长期缺乏代码级修复进展，存在积压风险，需维护团队重点关注：
- [#25592](https://github.com/openclaw/openclaw/issues/25592)：工具调用间文本泄漏（标记 `clawsweeper:no-new-fix-pr`，Slack/iMessage 等通用通道仍缺乏根本解决方案）。
- [#29387](https://github.com/openclaw/openclaw/issues/29387)：`agentDir` Bootstrap 文件被忽略（标记 `stale`，影响核心配置加载机制）。
- [#40001](https://github.com/openclaw/openclaw/issues/40001)：Write 工具无追加模式导致 Cron 覆盖共享文件（标记 `clawsweeper:no-new-fix-pr`，静默数据丢失风险高）。
- [#37634](https://github.com/openclaw/openclaw/issues/37634)：Sandbox `workspaceAccess: "none"` 导致工作区只读，工具无法写入（标记 `stale`，严重影响沙箱隔离模式的可用性）。
- **PR 积压**：目前有高达 381 个 PR 待合并，其中如 [#84009](https://github.com/openclaw/openclaw/pull/84009)（Gateway 会话定时重置）等大型重构 PR 长期处于 `waiting on author` 状态，可能阻塞后续功能迭代。

---

## 横向生态对比

# 个人 AI 助手与自主智能体开源生态横向对比分析报告 (2026-06-07)

## 1. 生态全景
个人 AI 助手与自主智能体开源生态正经历从“单点功能验证”向“企业级高可用与多智能体协同”的跃升期。多渠道接入（IM、桌面、TUI）与多模型适配已成为标配，但伴随而来的是状态隔离困难、推理内容泄漏及上下文压缩失效等普遍痛点。安全左移趋势显著，权限收敛、凭证脱敏与沙箱防逃逸正取代单纯的功能堆砌，成为各项目的核心分水岭。同时，多模型混合路由（大小模型协同、视觉模型解耦）与插件化架构（WASM、ACP协议）的涌现，标志着生态正在为更复杂的分布式协作做基建准备。

## 2. 各项目活跃度对比

| 项目 | 活跃 Issues | 活跃 PRs | Release 情况 | 健康度评估 |
| :--- | :--- | :--- | :--- | :--- |
| **OpenClaw** | 311 | 500 | v2026.6.5-beta.2 | 🟢 **良好**（高速迭代，但PR积压严重，隔离性痛点突出） |
| **ZeroClaw** | 42 | 50 | 无 (筹备 v0.8.0) | 🟢 **优秀**（安全收敛期，S0/S1缺陷修复迅速） |
| **Hermes Agent** | 48 | 50 | 无 | 🟡 **阵痛期**（新功能合入快，但v0.16.0跨平台回归Bug多，关闭率极低） |
| **PicoClaw** | 18 (清积压) | 12 (已合) | Nightly | 🟢 **优秀**（代码健壮性高，多智能体框架落地） |
| **LobsterAI** | 16 | 0 | 无 | 🔴 **停滞**（核心团队缺位，0 PR流转，严重积压） |
| **IronClaw** | 5 | 18 | 无 (0.29.1待发) | 🟡 **受限**（架构演进中，但发布流水线死锁，安全漏洞受阻） |
| **NanoBot** | 8 | 28 | 无 | 🟢 **良好**（企业级多租户支持稳步落地） |
| **NanoClaw** | 3 | 11 | 无 | 🟡 **基建期**（新手体验差，容器生命周期重构中） |
| **CoPaw** | 6 | 1 | 无 | 🟡 **探索期**（ACP协议扩展，但核心兼容性回归未解） |
| **Moltis** | 2 | 3 | 无 | 🟡 **平稳**（权限与流式输出打磨，Docker安全配置存患） |
| **Null/Tiny/Zepto**| 0 | 0 | 无 | ⚪ **静默** |

## 3. OpenClaw 在生态中的定位
* **生态体量绝对领先**：日活 311 Issue / 500 PR 的数据量远超同类（体量约为第二梯队的 6-10 倍），是当前生态中社区参与度最高、迭代最猛烈的项目。
* **多渠道适配的“急先锋”与“受害者”**：相比 Hermes 偏重桌面端、NanoBot 偏重企业协作，OpenClaw 在 IM 渠道覆盖上最激进（QQBot、飞书、Slack、iMessage）。但这也使其成为“内部状态泄漏至消息渠道”痛点的最大受害者，其 v2026.6.5-beta.2 的发布本质上是渠道适配欠下的技术债反应。
* **内存系统架构差异化**：独有 Dreaming / Deep Sleep 语料库与摘要机制，试图解决长上下文问题，但当前 Safeguard compaction 的崩溃表明其底层内存调度仍不够稳定。
* **技术路线对比**：相比 ZeroClaw 严苛的安全收敛和 PicoClaw 稳健的黑板协作，OpenClaw 当前呈现出“大干快上”的特征，381 个待合并 PR 透露出代码审查面临巨大压力。

## 4. 共同关注的技术方向
* **1. 推理与内部状态泄漏隔离 (OpenClaw, Hermes, NanoBot)**
  随着模型普遍具备深度思考能力，`<thinking>` 标签、工具报错、内部独白等“脚手架”频繁泄漏至 Slack/QQ/Telegram 等终端，严重影响用户体验。各项目亟需在消息投递前增加剥离层。
* **2. Cron 与后台任务的沙箱隔离 (OpenClaw, Hermes, ZeroClaw)**
  定时/后台任务污染全局认证状态、覆盖共享文件、或被生命周期机制误杀（OpenClaw #90991, Hermes #41235）。社区强烈呼吁 Write Append 模式与按频道/任务的执行上下文隔离。
* **3. 上下文压缩与缓存失效 (OpenClaw, Hermes, NanoBot, Moltis)**
  长对话压缩引发的路由失步（Hermes #34089）、上下文暴涨崩溃（OpenClaw #90639）以及截断导致 Prompt Prefix 缓存偏移带来的成本激增（NanoBot #4222），是当前阻碍 Agent 走向长时运行的核心技术瓶颈。
* **4. 细粒度权限与安全防逃逸 (ZeroClaw, NanoBot, PicoClaw)**
  从粗放的 API Key 管控转向 MCP 服务级 `allowFrom`（NanoBot）、Frontmatter 工具 deny/allow（PicoClaw）、凭证防脱敏与 SSRF 拦截（ZeroClaw），运行时安全收敛成刚需。

## 5. 差异化定位分析
* **功能侧重**：
  * **OpenClaw**：极致的多渠道覆盖与 AI 记忆系统。
  * **PicoClaw**：多智能体协同（Blackboard 黑板机制）与边缘设备适配。
  * **ZeroClaw**：极客向/企业级安全，WASM 插件生态与 TUI 体验。
  * **NanoBot**：多租户与企业级合规接入（Copilot for Business 支持）。
  * **Hermes Agent**：桌面端多媒体闭环与跨端 OAuth 集成。
* **目标用户**：
  * **OpenClaw/Hermes**：面向重度 IM 依赖的 C 端/极客用户。
  * **NanoBot/IronClaw**：面向需要私有化部署与数据隔离的 B 端团队。
  * **PicoClaw/CoPaw**：面向开发者与多模型混用的架构探索者。
* **技术架构**：
  * **IronClaw** 坚守 Rust Reborn 架构追求性能底线；**PicoClaw** 采用 Go 构建轻量多 Agent 池；**ZeroClaw** 探索 WASM 沙箱动态扩展；**NanoClaw** 全面拥抱容器化与 DB 驱动配置。

## 6. 社区热度与成熟度
* **狂奔迭代期（OpenClaw, Hermes Agent）**：热度极高，但代码质量与稳定性面临挑战，回归 Bug 频发，PR 积压严重，属于“边飞边换引擎”。
* **质量巩固期（ZeroClaw, NanoBot, PicoClaw）**：活跃度适中，但关闭率与合并质量高，代码提交聚焦于防御性编程、安全补丁与架构重构，项目健康度最佳。
* **架构重塑期（NanoClaw, IronClaw, CoPaw）**：受制于底层重构或发布流程阻塞，表面活跃度不高，但底层正在发生结构性变化（如 DB 持久化、Rust 重构、ACP 协议扩展）。
* **维护停滞期（LobsterAI, NullClaw等）**：核心团队响应断层，Issue 仅靠 Bot 标记 Stale，面临社区信任流失风险。

## 7. 值得关注的趋势信号
1. **从单一巨模型到混合路由编排**：CoPaw 的视觉中转站设想与 NanoBot 的子代理模型覆盖请求，印证了“大模型推理+小模型路由/提取”的降本增效架构正在成为开发者诉求主流。
2. **开源模型协议适配进入深水区**：DeepSeek 空字符串解析、Llama 4 复数 tool_calls 解析失败、千问 vLLM 死锁等 Bug 频出，表明“兼容 OpenAI API”不再是一句口号，而是需要针对各家奇异响应结构做深度兼容的脏活累活。
3. **智能体需要“静默执行”能力**：用户极度反感 Agent 把思考过程与报错暴露在聊天流中，未来的 Agent 架构必将把“内部日志与终端交付内容分离”作为一等公民设计。
4. **C端应用倒逼安全左移**：即便是面向个人的 AI 助手，也因工具调用能力的增强频发路径逃逸、SSRF、命令注入等漏洞。将 Agent 运行在严格白名单与 WASM 沙箱中，将从可选项变为必选项。
5. **发布流水线成为新木桶短板**：IronClaw 因 crates.io 发布滞后导致 CVE 无法修复，OpenClaw 积压 381 个 PR。在开源项目极速膨胀的当下，工程化交付与 CI/CD 的能力正成为制约项目发展的关键瓶颈。

---

## 同赛道项目详细报告

<details>
<summary><strong>NanoBot</strong> — <a href="https://github.com/HKUDS/nanobot">HKUDS/nanobot</a></summary>

# NanoBot 项目动态日报 (2026-06-07)

## 1. 今日速览
NanoBot 今日保持高度活跃，共处理 8 条 Issue（新开/活跃 6 条，关闭 2 条）和 28 条 PR（待合并 20 条，合并/关闭 8 条）。项目当前处于密集的代码审查与功能迭代期，待合并 PR 积压较多（20条），社区贡献积极。今日整体重心聚焦于系统底层稳定性提升（特别是会话历史管理的严重 Bug 修复）与企业级/多用户场景的拓展（如权限控制、企业版 Copilot 支持），项目正朝着更安全、更适合团队部署的方向演进。

## 2. 版本发布
今日无新版本发布。

## 3. 项目进展
今日共有 8 个 PR 被合并或关闭，项目在**多用户隔离**和**渠道通信稳定性**上取得显著进展：
*   **多用户与企业级支持落地**：[PR #2968](https://github.com/HKUDS/nanobot/pull/2968) 合并了 `per_user_memory` 功能，实现了工作区级别的用户记忆隔离；[PR #2533](https://github.com/HKUDS/nanobot/pull/2533) 引入了 MCP 服务器的 `allowFrom` 访问控制，增强了多用户环境下的工具调用权限管理。
*   **渠道通信稳定性提升**：[PR #2555](https://github.com/HKUDS/nanobot/pull/2555) 修复了 WhatsApp 桥接重连时的消息重复处理问题；[PR #2663](https://github.com/HKUDS/nanobot/pull/2663) 修复了 WhatsApp 群组 LID 提及检测失败的问题。
*   **流式响应解析优化**：[PR #4228](https://github.com/HKUDS/nanobot/pull/4228) 修复了自定义 Provider 丢失空字符串 `reasoning_content` 的问题。

## 4. 社区热点
今日最受关注的 Issue 是 [Issue #2573](https://github.com/HKUDS/nanobot/issues/2573)（👍 9），用户报告在 v0.1.4.post6 版本中使用 GitHub Copilot 登录时出现 `Authorization header is badly formatted` 报错，该问题与底层依赖从 litellm 切换为 openai 有关，目前该 Issue 已被关闭，说明核心登录问题已得到修复。此外，[Issue #2256](https://github.com/HKUDS/nanobot/issues/2256) 关于飞书话题群 Bot 回复逻辑的讨论也较活跃（评论 4），社区对多渠道集成体验的精细化关注度较高。

## 5. Bug 与稳定性
今日报告了多个影响核心会话链路的 Bug，部分已有修复 PR：
*   **[高危] 会话历史全量丢失**：[Issue #4203](https://github.com/HKUDS/nanobot/issues/4203) 报告当用户消息后跟孤立的工具结果时，`find_legal_message_start` 会返回列表长度，导致所有上下文消息被丢弃。目前已有两个修复 PR 提交：[PR #4219](https://github.com/HKUDS/nanobot/pull/4219) 和 [PR #4229](https://github.com/HKUDS/nanobot/pull/4229)。
*   **[中危] Prompt 缓存失效导致成本增加**：[Issue #4222](https://github.com/HKUDS/nanobot/issues/4222) 指出 `max_messages` 截断和 `microcompact` 机制导致每次对话的消息前缀发生偏移，破坏了 Prompt/Prefix 缓存，增加 API 开销，**目前尚无修复 PR**。
*   **[中危] API 重试导致用户消息重复**：[PR #4234](https://github.com/HKUDS/nanobot/pull/4234) 暴露了 `/v1/chat/completions` 端点在空响应重试时，会重复持久化用户消息的问题，该 PR 已提供修复逻辑。
*   **[低危] 自定义 Provider 丢弃推理内容**：[Issue #4105](https://github.com/HKUDS/nanobot/issues/4105) 报告空字符串 `reasoning_content` 被强制转为 None，影响 DeepSeek 等模型的思维链解析。修复见 [PR #4227](https://github.com/HKUDS/nanobot/pull/4227)。

## 6. 功能请求与路线图信号
*   **UI 体验优化**：[Issue #4233](https://github.com/HKUDS/nanobot/issues/4233) 请求在 WebUI 中显示当前版本及可用更新。该需求已由 [PR #4235](https://github.com/HKUDS/nanobot/pull/4235) 实现（通过 PyPI 缓存检查新版本），预计很快合入主分支。
*   **子代理模型覆盖**：[Issue #4231](https://github.com/HKUDS/nanobot/issues/4231) 建议为 `spawn` 工具添加 `model` 参数，允许子代理使用与主代理不同的模型（如便宜模型做路由，昂贵模型做推理）。这是一个高价值的架构增强信号，目前尚无对应 PR。
*   **企业级 GitHub Copilot 支持**：[Issue #4220](https://github.com/HKUDS/nanobot/issues/4220) 请求支持 GitHub Copilot for Business / GitHub Enterprise 的 API 端点。结合今日已落地的多用户权限控制 PR，此功能极有可能是下一版本的重点。

## 7. 用户反馈摘要
*   **多租户与权限需求强烈**：用户在多用户部署中遇到记忆混淆和敏感工具滥用问题，直接推动了今日 `per_user_memory` 和 `allowFrom` 的合入，表明团队协作场景是当前核心用户画像。
*   **深度推理模型适配问题**：使用 DeepSeek、Kimi 等国产推理模型时，空 `reasoning_content` 的解析问题成为阻碍，说明社区对非 OpenAI 标准协议的兼容性有较高诉求。
*   **复杂 Agent 编排痛点**：用户希望在多 Agent 协作中灵活调度模型（大模型推理+小模型分流），当前的硬绑定机制限制了这一场景的落地。
*   **对企业级合规接入的渴望**：对 GitHub Enterprise 和 Copilot for Business 的需求，反映出 NanoBot 正在被更大规模的团队评估和采用。

## 8. 待处理积压
当前待合并 PR 数量达到 20 个，其中包含多项核心安全与稳定性修复，建议维护者优先 Review：
*   **[安全] SSRF 防护**：[PR #4123](https://github.com/HKUDS/nanobot/pull/4123) 阻止 MCP 对不安全 HTTP URL 的探测，防止 SSRF 攻击。
*   **[安全] 工作区逃逸拦截**：[PR #4119](https://github.com/HKUDS/nanobot/pull/4119) 阻止通过相对符号链接逃离工作区目录；[PR #4053](https://github.com/HKUDS/nanobot/pull/4053) 防止只读路径被写入。
*   **[待认领] Prompt 缓存失效**：[Issue #4222](https://github.com/HKUDS/nanobot/issues/4222) 尚无 PR 修复，此问题会导致企业级用户的 API 开销显著增加，需尽快排期。
*   **[基础设施] 缺乏超时控制**：[PR #4230](https://github.com/HKUDS/nanobot/pull/4230) 指出 MCP streamableHttp 连接未设置超时，可能导致启动时无限期挂起，需纳入优先合并序列。

</details>

<details>
<summary><strong>Hermes Agent</strong> — <a href="https://github.com/nousresearch/hermes-agent">nousresearch/hermes-agent</a></summary>

# Hermes Agent 项目动态日报 (2026-06-07)

## 1. 今日速览
今日 Hermes Agent 项目保持高度活跃，24 小时内新增/活跃 Issues 达 48 条，PR 更新达 50 条，社区参与度极高。尽管无新版本发布，但大量讨论集中在 v0.16.0 版本引发的回归问题（特别是 macOS 启动域错误、桌面端循环启动及 GPT-5.x 兼容性）。项目整体处于快速迭代与阵痛期并行的状态：新功能（如远程媒体中继、原生会话分叉）正在积极合入，但跨平台稳定性（尤其 Windows 与 macOS 桌面端）和核心上下文压缩机制面临严峻考验。Bug 关闭率偏低（仅 2 条），维护者需警惕积压问题。

## 2. 版本发布
今日无新版本发布。

## 3. 项目进展
今日仅有 3 个 PR 被合并/关闭，但大量核心修复与功能 PR 正在活跃推进，项目在多端协同与模型兼容性上迈出重要步伐：
- **[CLOSED] fix(xai): resolve grok-composer-2.5-fast context for OAuth** ([PR #40046](https://github.com/NousResearch/hermes-agent/pull/40046)): 修复了 xAI grok-composer-2.5-fast 模型上下文长度识别错误的问题，避免了压缩器过早触发。
- **[OPEN] feat(desktop+gateway): remote media relay** ([PR #41336](https://github.com/NousResearch/hermes-agent/pull/41336)): 实现了桌面端连接远程网关时的图片/PDF 附件上传与展示功能，打通了远程部署场景下的多媒体闭环。
- **[OPEN] fix(gateway): wrap blocking /model provider-list calls in asyncio.to_thread** ([PR #41324](https://github.com/NousResearch/hermes-agent/pull/41324)): 修复了 Discord 网关 `/model` 命令因同步 HTTP 调用阻塞事件循环 120s+ 导致超时的问题。
- **[OPEN] fix(agent): deduplicate cumulative message output_items in Codex stream** ([PR #41332](https://github.com/NousResearch/hermes-agent/pull/41332)): 修复了 Bedrock mantle (GPT-5.x) 接口返回重复文本的严重 Bug，提升了主流模型输出的稳定性。
- **[OPEN] fix(cron): don't skip first-run cron jobs past grace window** ([PR #41307](https://github.com/NousResearch/hermes-agent/pull/41307)): 修复了新建 Cron 任务因宽限期逻辑错误被静默跳过的 P1 级问题。

## 4. 社区热点
今日讨论最热烈的问题集中在**本地化支持**、**核心路由机制**与**macOS 严重回归**：
- **日文界面支持请求** ([Issue #40219](https://github.com/NousResearch/hermes-agent/issues/40219), 5 评论): 社区对多语言支持（特别是日文输入法与 UI）呼声很高，目前仅支持英文与简中，阻碍了非英语圈用户的采纳。
- **级联上下文文件发现** ([Issue #10299](https://github.com/NousResearch/hermes-agent/issues/10299), 4 评论): 开发者强烈需要 Agent 能够向上遍历父目录加载 `AGENTS.md` 等上下文，而非仅限 CWD，这是目前多仓库/Monorepo 场景下的核心痛点。
- **macOS 26 launchd 域回归** ([Issue #40831](https://github.com/NousResearch/hermes-agent/issues/40831), 4 评论): v0.16.0 硬编码了 `user/<uid>` 导致 Aqua 会话下网关无法启动，Mac 桌面用户反响强烈。
- **Curator 误归档活跃技能** ([Issue #29912](https://github.com/NousResearch/hermes-agent/issues/29912), 4 评论): 技能整合机制存在 Fail-open 隐患，一次误操作归档了 10 个核心技能，引发对系统鲁棒性的担忧。

## 5. Bug 与稳定性
今日报告了大量 Bug，其中多个 P1 级问题及 v0.16.0 回归问题严重影响用户体验：

**P1 / 严重回归:**
- **macOS 26 launchd 域错误** ([Issue #40831](https://github.com/NousResearch/hermes-agent/issues/40831)): PR #40581 引入回归，破坏 Aqua 会话网关启动。*(暂无 Fix PR)*
- **会话压缩导致网关路由失步** ([Issue #34089](https://github.com/NousResearch/hermes-agent/issues/34089)): 上下文压缩后，Agent 工作记忆与网关 Session ID 脱节，导致消息静默丢失。*(相关修复推进中: [PR #31073](https://github.com/NousResearch/hermes-agent/pull/31073))*
- **Discord /model 命令阻塞事件循环** ([Issue #41289](https://github.com/NousResearch/hermes-agent/issues/41289)): 同步 HTTP 请求卡死异步循环 120s+。*(已有 Fix PR: [PR #41324](https://github.com/NousResearch/hermes-agent/pull/41324))*

**P2 / 核心功能受损:**
- **Bedrock GPT-5.x 输出严重重复** ([Issue #41321](https://github.com/NousResearch/hermes-agent/issues/41321)): mantle 端点累积输出导致文本膨胀。*(已有 Fix PR: [PR #41332](https://github.com/NousResearch/hermes-agent/pull/41332))*
- **Windows 更新删除网关目录** ([Issue #41255](https://github.com/NousResearch/hermes-agent/issues/41255)): 执行 `hermes update` 会误删 `gateway-service/`，导致 Telegram 等网关静默失效。*(部分相关修复: [PR #41264](https://github.com/NousResearch/hermes-agent/pull/41264))*
- **后台进程被生命周期清理误杀** ([Issue #41225](https://github.com/NousResearch/hermes-agent/issues/41225), [Issue #41235](https://github.com/NousResearch/hermes-agent/issues/41235)): `terminal(background=true)` 启动的进程在压缩或退出时被 SIGTERM。*(暂无 Fix PR)*
- **命令注入漏洞** ([Issue #16560](https://github.com/NousResearch/hermes-agent/issues/16560)): `tui_gateway/server.py` 中存在 `shell=True` 导致的命令注入风险。*(暂无 Fix PR)*

**桌面端严重回归:**
- **Desktop 启动循环** ([Issue #41329](https://github.com/NousResearch/hermes-agent/issues/41329)): v0.15.1 桌面端请求已被 v0.16.0 核心移除的 `/api/ws` 路由，导致无法启动。
- **Dashboard 404** ([Issue #41327](https://github.com/NousResearch/hermes-agent/issues/41327)): 静态文件打包进 `app.asar` 但未配置 `asarUnpack`，导致 Web 路由全 404。

## 6. 功能请求与路线图信号
- **桌面端原生会话分支** ([Issue #40950](https://github.com/NousResearch/hermes-agent/issues/40950)): 请求桌面端 "从消息分支" 使用后端真实的 Session Fork 而非伪复制。*(已有实现 PR: [PR #40973](https://github.com/NousResearch/hermes-agent/pull/40973)，极可能近期合入)*
- **自定义 Shell 配置** ([Issue #41249](https://github.com/NousResearch/hermes-agent/issues/41249) 相关): 终端工具硬编码 bash 对 Windows 等平台不友好。*(已有实现 PR: [PR #41326](https://github.com/NousResearch/hermes-agent/pull/41326)，支持 `terminal.shell` 配置)*
- **新用户引导式画像构建** ([PR #41114](https://github.com/NousResearch/hermes-agent/pull/41114)): 首次对话时通过 Opt-in 机制收集用户偏好并写入 Memory，增强 Agent 记忆连续性。
- **CDN 预编译分发** ([Issue #41291](https://github.com/NousResearch/hermes-agent/issues/41291)): 中国大陆等受限网络环境无法访问 GitHub 源码构建，需提供预编译 `.app`。

## 7. 用户反馈摘要
- **跨平台体验割裂严重**：Windows 和 Mac 桌面端在最近更新中受损严重，用户反馈更新后网关丢失、界面 404 或启动循环（#41329, #41255），影响了基本可用性。
- **输入法兼容性差**：CJK（中日）用户输入法受困，日文 UI 缺失（#40219），Windows 下中文 IME 回车无法发送（#39025），`/goal` 命令多行输入被拦截（#41323）。
- **多端 OAuth 冲突**：同一机器上运行 Codex CLI/VS Code 插件会导致 Hermes 的 Refresh Token 被挤下线（#22903），反映出现代多端 AI 工具共存的认证痛点。
- **长上下文与后台任务不可靠**：用户依赖的后台进程常被清理机制误杀（#41235），且上下文压缩后对话容易失忆或断连（#34089），对长时任务极度不友好。

## 8. 待处理积压
以下重要问题长期未获解决或响应，需维护者重点关注：
- **[安全] Command injection via shell=True** ([Issue #16560](https://github.com/NousResearch/hermes-agent/issues/16560)): 创建于 4 月 27 日，存在明确的安全风险，至今未修复。
- **[功能] Agent audit log** ([Issue #1155](https://github.com/NousResearch/hermes-agent/issues/1155)): 创建于 3 月 13 日，社区强烈要求的审计日志功能，是 Agent 企业级应用的基础。
- **[Bug] Codex OAuth refresh token 被兄弟客户端消耗** ([Issue #22903](https://github.com/NousResearch/hermes-agent/issues/22903)): 创建于 5 月 10 日，多端共存场景下频繁导致鉴权失效，影响重度用户。
- **[Bug] Docker s6 重启后网关持久化停止** ([Issue #33597](https://github.com/NousResearch/hermes-agent/issues/33597)): 创建于 5 月 28 日，容器化部署的关键阻碍。

</details>

<details>
<summary><strong>PicoClaw</strong> — <a href="https://github.com/sipeed/picoclaw">sipeed/picoclaw</a></summary>

# PicoClaw 项目动态日报 (2026-06-07)

## 1. 今日速览
PicoClaw 项目今日维持高度活跃状态，Issue 处理效率极高，过去 24 小时内关闭了 16 条 Issue（新开/活跃仅 2 条），显示出维护团队正在积极清理积压。PR 合并表现同样强劲，共 12 条 PR 被合并/关闭，多项重磅功能（如 Google Chat 频道、多智能体协作框架）成功合入主线。代码质量方面，今日出现了密集的防御性编程修复，重点填补了文件 I/O 与类型断言的错误处理盲区。项目整体健康度优秀，正稳步推进 `v0.2.9` 版本的迭代。

## 2. 版本发布
- **nightly: Nightly Build** (`v0.2.9-nightly.20260607.7d2b0c2a`)
  - **性质**：自动化的每日构建版本。
  - **注意事项**：官方提示可能不稳定，建议谨慎用于生产环境。
  - **变更范围**：涵盖自 `v0.2.9` 标签至 `main` 分支的所有最新提交，包含今日合并的多项重要功能与修复。
  - **完整变更日志**：[v0.2.9...main](https://github.com/sipeed/picoclaw/compare/v0.2.9...main)

## 3. 项目进展
今日合并/关闭的 PR 极大地推进了项目在多渠道接入、智能体协作及底层稳定性方面的进展：
- **多渠道支持迈出重要一步**：[#830](https://github.com/sipeed/picoclaw/pull/830) 正式合入 Google Chat 频道支持，扩展了 PicoClaw 的企业级通讯平台覆盖面。
- **多智能体架构落地**：[#423](https://github.com/sipeed/picoclaw/pull/423) 合入了基础多智能体协作框架与共享上下文池，引入了 Blackboard（黑板机制）与 Agent 发现/交接工具，为后续复杂的协同工作流奠定基石。
- **模型供应商生态扩充**：[#1112](https://github.com/sipeed/picoclaw/pull/1112) 合入了 ModelScope 上的 deepseek-ai 协议支持，解决了此前 `deepseek-ai/DeepSeek-V3.2` 等模型报错未知协议的问题。
- **安全与工具管控增强**：[#2838](https://github.com/sipeed/picoclaw/pull/2838) 合入了 Frontmatter 工具策略过滤器，允许在 `AGENT.md` 中使用 `allow/deny` 细粒度管控内置工具与 MCP 服务器。
- **前端体验优化**：[#2711](https://github.com/sipeed/picoclaw/pull/2711) 修复了非安全上下文（HTTP）下剪贴板复制按钮失效的问题，自动降级处理。

## 4. 社区热点
- **WhatsApp 编译支持诉求强烈**：[#2625](https://github.com/sipeed/picoclaw/issues/2625) 获得了较多讨论（8 条评论，👍 1）。用户在树莓派 Zero 2 上使用 PicoClaw，但默认 arm64 构建未包含 WhatsApp 支持，导致更新困难。该 Issue 今日被关闭，可能意味着相关编译选项已调整或已被标记为过期。
- **智能体间通信机制探讨**：[#2929](https://github.com/sipeed/picoclaw/issues/2929) 探讨了点对点的一等公民级智能体通信层（当前仅有 spawn/subagent），该需求获得 2 个赞，反映出社区对多智能体协同工作流的强烈需求。该 Issue 今日被关闭，与上面提到的多智能体框架 PR [#423](https://github.com/sipeed/picoclaw/pull/423) 合并相呼应。
- **批量任务关闭事件**：用户 `jcafeitosa` 今日集中关闭了 9 个 Issues（从 [#3024](https://github.com/sipeed/picoclaw/issues/3024) 到 [#3032](https://github.com/sipeed/picoclaw/issues/3032)），内容涉及 Binance 交易连接器、无锁环形缓冲区等。这看起来是某个外部子项目或个人开发计划的批量清理，拉高了今日的 Issue 关闭率。

## 5. Bug 与稳定性
今日报告了若干关键 Bug，部分已有修复 PR 跟进；同时维护者进行了深度的代码健壮性审查：

**今日新增/活跃 Bug**：
1. 🔴 **Matrix 频道 `allow_from` 规则失效**：[#3044](https://github.com/sipeed/picoclaw/issues/3044) 指出标准 Matrix ID（如 `@alice:example.com`）因包含冒号被错误解析，导致消息被静默拒绝。**已有 Fix PR**：[#3045](https://github.com/sipeed/picoclaw/pull/3045) 修复了 `ParseCanonicalID` 的分割逻辑。
2. 🟠 **`mcp add` 命令参数解析错误**：[#3041](https://github.com/sipeed/picoclaw/issues/3041) 指出全局标志被错误解析为位置参数，导致添加 http/sse 类型服务器失败，且会错误命名 stdio 服务器。**暂无 Fix PR**。

**已修复的稳定性问题**：
- 🟢 **Anthropic 模型默认 ID 错误**：[#2941](https://github.com/sipeed/picoclaw/issues/2941) 反映默认配置使用了带点的 `claude-sonnet-4.6` 导致 API 404。已通过 [#3036](https://github.com/sipeed/picoclaw/pull/3036) 修复为连字符格式 `claude-sonnet-4-6`。
- 🟢 **文件 I/O 与资源泄漏防御性修复**：贡献者 `chengzhichao-xydt` 今日集中提交并合并了多项修护，包括：修复下载文件/飞书资源时 `Close()` 错误被吞没导致文件截断的隐患（[#3033](https://github.com/sipeed/picoclaw/pull/3033), [#3034](https://github.com/sipeed/picoclaw/pull/3034), [#3035](https://github.com/sipeed/picoclaw/pull/3035)）；修复 `Manager.Reload()` 未取消旧 context 导致 Goroutine 泄漏（[#3016](https://github.com/sipeed/picoclaw/pull/3016)）；修复类型断言未检查 `ok` 导致的潜在 Panic（[#3040](https://github.com/sipeed/picoclaw/pull/3040)）。

## 6. 功能请求与路线图信号
- **原生 Kagi 搜索集成**：[#3037](https://github.com/sipeed/picoclaw/pull/3037) 正在尝试将 Kagi Search 作为原生 `web_search` 提供商引入，这表明 PicoClaw 的 Web 检索能力正在走向多样化和专业化。
- **会话历史深度查询**：[#3047](https://github.com/sipeed/picoclaw/pull/3047) 提出恢复 JSONL 会话的完整历史记录读取能力，表明项目在优化轻量级列表分页的同时，也在兼顾详细上下文的持久化需求。
- **国际化（i18n）扩展**：[#2935](https://github.com/sipeed/picoclaw/pull/2935) 提出了繁体中文（zh-TW）的本地化支持，说明项目在非英语社区的关注度正在提升。

## 7. 用户反馈摘要
- **边缘设备体验仍有痛点**：有用户在树莓派 Zero 2 上使用 PicoClaw，发现特定渠道（WhatsApp）的编译支持缺失，说明在跨架构（特别是 arm64 精简版）的交付物上，用户期望有更开箱即用的体验。
- **升级与部署文档不足**：[#2834](https://github.com/sipeed/picoclaw/issues/2834) 反映了用户对于“如何从源码安全升级”缺乏明确指引，这是阻碍新用户留存的一个关键痛点。
- **默认配置开箱即用问题**：Anthropic 模型 ID 的错误（[#2941](https://github.com/sipeed/picoclaw/issues/2941)）表明，用户对首次安装即可正常工作的期望很高，默认配置的细微错误会导致极大的挫败感。

## 8. 待处理积压
- **繁体中文本地化 PR 搁置**：[#2935](https://github.com/sipeed/picoclaw/pull/2935) 已打开近两周，目前处于 Stale 状态，需要维护者进行 Review 并推进合入。
- **防御性修复 PR 积压**：[#3018](https://github.com/sipeed/picoclaw/pull/3018) 及今日新开的 [#3042](https://github.com/sipeed/picoclaw/pull/3042)、[#3043](https://github.com/sipeed/picoclaw/pull/3043)、[#3046](https://github.com/sipeed/picoclaw/pull/3046) 提交了大量关于 `os.Getwd()` 错误处理和类型断言的修复，部分可能存在逻辑重叠（特别是 #3018 与后续单项 PR 之间），建议维护者统一梳理后合并，避免冲突。
- **MCP 命令行解析缺陷**：[#3041](https://github.com/sipeed/picoclaw/issues/3041) 涉及核心 MCP 服务添加流程的阻断性问题，且目前尚无对应 PR 修复，需优先排期。

</details>

<details>
<summary><strong>NanoClaw</strong> — <a href="https://github.com/qwibitai/nanoclaw">qwibitai/nanoclaw</a></summary>

# NanoClaw 项目动态日报 (2026-06-07)

## 1. 今日速览
NanoClaw 项目今日保持较高活跃度，过去 24 小时内新增 3 个 Issue，产生 11 项 PR 更新（8 项待合并，3 项已关闭）。社区当前聚焦于**新手安装体验的健壮性**与**容器生命周期管理的稳定性**，Slack 集成与凭证代理等核心模块迎来了密集修复。尽管有 3 个 PR 被关闭（未合入主分支），但大量聚焦底层重构和 Bug 修复的 PR 正在排队等待合并，反映出项目正处于“稳固基建与优化首体验”的阶段。

## 2. 版本发布
今日无新版本发布。

## 3. 项目进展
今日共有 3 项 PR 被关闭（均未合入主线），另有 8 项待合并 PR 正在积极推进：
*   **[#2707](https://github.com/nanocoai/nanoclaw/pull/2707) [CLOSED] feat(upgrade): startup tripwire + upgrade marker**：试图引入“启动熔断机制”，拒绝未走正规升级路径的实例启动。虽被关闭，但暴露了项目对版本迁移安全性的强烈需求。
*   **[#2706](https://github.com/nanocoai/nanoclaw/pull/2706) [CLOSED] fix(账号轮换): 限制模式并校准切换状态**：修复了多模型账号轮换时的游标漂移和通知错乱问题，该 PR 的提出说明多模型网关调度机制正在经历实战打磨。
*   **[#2698](https://github.com/nanocoai/nanoclaw/pull/2698) [CLOSED] Skills conformance: exemplars + fleet retrofit**：试图对技能库进行标准化改造使其可升级维护，被关闭说明技能架构的规范化方案仍需探讨。
*   **排队中的关键进展**：数据库持久化容器配置 ([#2709](https://github.com/nanocoai/nanoclaw/pull/2709))、Slack 全面转向 Socket Mode ([#2700](https://github.com/nanocoai/nanoclaw/pull/2700), [#2702](https://github.com/nanocoai/nanoclaw/pull/2702))、孤儿容器清理 ([#2708](https://github.com/nanocoai/nanoclaw/pull/2708)) 等重要重构已提交，项目整体架构正向更健壮的云端/守护进程部署模式演进。

## 4. 社区热点
今日最活跃的讨论集中在启动与安装流程的阻碍上：
*   **[#2312](https://github.com/nanocoai/nanoclaw/issues/2312) [OPEN] groups/global/CLAUDE.md 启动时被无条件删除**：该 Issue 自 5 月初创建，今日再次活跃（2 条评论）。用户指出 `migrateGroupsToClaudeLocal()` 逻辑导致拉取仓库重启后，工作区永远处于脏状态。这触及了版本控制与本地运行时状态冲突的核心痛点，社区对迁移脚本的副作用表达了明确不满。
*   **[#2703](https://github.com/nanocoai/nanoclaw/issues/2703) [OPEN] setup 推荐路径导致命令挂起 120s**：今日新开，反映官方推荐的 `pnpm run chat hi` 在 `cli/local` 未连接时直接卡死且无任何错误提示。这暴露出安装脚本的断言检查严重缺失，直接影响新用户的首次体验。

## 5. Bug 与稳定性
今日报告的 Bug 主要涉及安装、运行时配置和容器管理，按严重程度排列如下：

*   **🔴 严重 (P0/P1) - 新手流程阻断**：
    *   **[#2703](https://github.com/nanocoai/nanoclaw/issues/2703)**：推荐安装路径下命令挂起 120s 无响应，无修复 PR。
*   **🟠 中等 (P2) - 运行时状态脏乱**：
    *   **[#2312](https://github.com/nanocoai/nanoclaw/issues/2312)**：启动时无条件删除入库文件导致 Git 脏树，无修复 PR。
*   **🟡 低 (P3) - 边缘场景报错**：
    *   **[#2701](https://github.com/nanocoai/nanoclaw/issues/2701)**：无包配置时 `ncl groups restart --rebuild` 失败，无修复 PR。
*   **🟢 已有修复 PR 的 Bug**：
    *   Slack 适配器 HTTP Webhook 报错：有 [#2702](https://github.com/nanocoai/nanoclaw/pull/2702) 和 [#2700](https://github.com/nanocoai/nanoclaw/pull/2700) 修复。
    *   凭证代理绕过失效：有 [#2705](https://github.com/nanocoai/nanoclaw/pull/2705) 修复。
    *   CLI 创建 ID 导致 OneCLI 解析失败：有 [#2699](https://github.com/nanocoai/nanoclaw/pull/2699) 修复。
    *   服务停止时孤儿容器未回收：有 [#2708](https://github.com/nanocoai/nanoclaw/pull/2708) 修复。

## 6. 功能请求与路线图信号
*   **容器配置持久化演进**：**[#2709](https://github.com/nanocoai/nanoclaw/pull/2709)** 为 `container_configs` 引入了 DB 支持的 `env` 和 `blocked_hosts` 字段，实现了维护者请求的 #1867。这释放出明确信号：NanoClaw 正在摆脱静态文件配置，向动态数据库驱动的容器管理演进，极大增强了多实例部署的灵活性。
*   **Telegram 多租户能力**：**[#1626](https://github.com/nanocoai/nanoclaw/pull/1626)** 提出了基于 Topic 的 Telegram 自动注册与隔离功能。该功能若合入，将使 NanoClaw 在群组场景下具备真正的多会话隔离能力，是个人 AI 助手走向社群服务的关键一步。
*   **升级防护网**：尽管 #2707 被关闭，但“拒绝非法升级（如裸 git pull）”的诉求明确，预计维护者后续会重构并重新提交该特性的 PR。

## 7. 用户反馈摘要
*   **开箱体验存在断点**：用户严格按照官方推荐路径操作，却因依赖未连接导致命令卡死（[#2703](https://github.com/nanocoai/nanoclaw/issues/2703)反馈），且缺乏 Graceful Fallback 或明确报错，引发挫败感。
*   **隐式逻辑破坏用户预期**：`CLAUDE.md` 被无声删除（[#2312](https://github.com/nanocoai/nanoclaw/issues/2312)）和原生代理未生效（[#2705](https://github.com/nanocoai/nanoclaw/pull/2705)背景）都反映出：代码中的“智能默认行为”在失效时没有给用户足够的可见性，导致排查困难。
*   **多模型混用需要隔离**：用户在使用 Codex/Gemini 模式时收到 Claude 的额度通知（[#2706](https://github.com/nanocoai/nanoclaw/pull/2706)背景），说明不同底层模型的账号状态和路由逻辑需要更严格的边界隔离。

## 8. 待处理积压
*   **[#2312](https://github.com/nanocoai/nanoclaw/issues/2312)**：自 5 月 6 日提交的 `CLAUDE.md` 删除问题，今日虽有讨论但仍未指派或提交修复。作为每次启动都会制造 Git Diff 的源头，严重干扰贡献者的版本管理，建议维护者优先排期。
*   **[#1626](https://github.com/nanocoai/nanoclaw/pull/1626)**：Telegram Topic 隔离特性 PR 已打开超 2 个月，今日虽有更新但仍未合并。作为社区期待的高价值 IM 集成功能，建议维护者评估其架构设计并推进合入或给出修改意见。

</details>

<details>
<summary><strong>NullClaw</strong> — <a href="https://github.com/nullclaw/nullclaw">nullclaw/nullclaw</a></summary>

过去24小时无活动。

</details>

<details>
<summary><strong>IronClaw</strong> — <a href="https://github.com/nearai/ironclaw">nearai/ironclaw</a></summary>

# IronClaw 项目动态日报 (2026-06-07)

## 1. 今日速览
IronClaw 项目今日保持高活跃度，共有 18 个 PR 更新与 5 个 Issue 更新。核心开发者（serrrfirat, henrypark133 等）正全力推进 "Reborn" 架构演进及 WebUI/Slack 渠道功能增强，今日成功合并了 6 个 PR 并关闭了 3 个 Issue。目前仍有 12 个 PR 待合并（包含多个 XL 级别大型改动），审查压力较大。值得注意的是，关于 crates.io 发布滞后导致下游依赖受安全漏洞（CVEs）阻断的问题，成为今日社区最核心的讨论焦点。

## 2. 版本发布
今日无新版本发布。
> **⚠️ 迁移预警**：虽然今日无版本发布，但正在待合并的 Release PR [#3708](https://github.com/nearai/ironclaw/pull/3708) 显示，下个版本将包含多个破坏性变更（`ironclaw_common` 0.4.2 -> 0.5.0，`ironclaw_skills` 0.3.0 -> 0.4.0，主 crate `ironclaw` 将跃升至 0.29.1）。下游开发者需密切跟踪该 PR 中的 API 变更细节。

## 3. 项目进展
今日共关闭/合并 6 个 PR，在身份认证、渠道路由、智能体行为优化及架构设计上取得实质性进展：
*   **身份与 OAuth 系统**：合并了 [#4461](https://github.com/nearai/ironclaw/pull/4461)，引入规范的 Reborn 身份解析器，为 OAuth 和外部参与者提供统一的 `UserId` 映射边界，正式关闭了 Issue [#4381](https://github.com/nearai/ironclaw/issues/4381)。
*   **渠道与通信**：合并了 [#4510](https://github.com/nearai/ironclaw/pull/4510)，完成了 Slack 频道路由的管理员接线，提升了多渠道部署的管理体验。
*   **智能体执行稳定性**：合并了 [#4524](https://github.com/nearai/ironclaw/pull/4524)，修复了智能体循环中“过早停止”的粗糙启发式逻辑，保留了更精确的无进展检测器。
*   **架构设计落定**：合并了 [#4485](https://github.com/nearai/ironclaw/pull/4485) 与 [#4486](https://github.com/nearai/ironclaw/pull/4486)，确立了后台子代理与上下文压缩的统一设计文档，引入了 `PostCapabilityStage` 概念。
*   **CI 效率优化**：合并了 [#4520](https://github.com/nearai/ironclaw/pull/4520)，将 Reborn 专属测试从 Legacy 流水线中隔离，提升 CI 执行效率。

## 4. 社区热点
今日讨论最热烈的问题是 Issue [#3259](https://github.com/nearai/ironclaw/issues/3259) (👍 0，13 条评论)。
*   **核心诉求**：由于 `wasmtime` 28.x 存在 CVE 漏洞，下游使用者被迫锁定在 `crates.io` 上最高发布的 `0.24.0` 版本。然而 GitHub Repo 实际已推进至 `0.27.0`。发布链路的严重滞后导致下游无法通过升级来规避安全漏洞，引发了社区对发布流水线自动化的强烈呼吁。

## 5. Bug 与稳定性
*   **🔴 高/中严重度**：Nightly E2E 测试失败 ([#4108](https://github.com/nearai/ironclaw/issues/4108))。由 CI 机器人自动报告，当前无修复 PR，需密切关注是否为主干代码回归。
*   **🟡 中严重度**：系统哨兵值反序列化异常 ([#4523](https://github.com/nearai/ironclaw/pull/4523))。`TenantId`/`UserId` 的序列化与反序列化路径验证不对称，导致 `\x1fSYSTEM\x1f` 哨兵值被拒，进而引发 `/api/webchat/v2/llm/*` 请求返回 `service_unavailable`。目前已有修复 PR 提交待审查。
*   **🟢 低严重度（已修复）**：智能体因重复 `LoopFailureKind` 被过早终止执行的 Bug，已通过 PR [#4524](https://github.com/nearai/ironclaw/pull/4524) 修复合并。

## 6. 功能请求与路线图信号
*   **Feed 系统 (非中断式信息流)**：Issue [#70](https://github.com/nearai/ironclaw/issues/70) 今日关闭，该功能旨在为智能体和工具提供持久化、可查询但非阻塞的 Feed 推送，结合近期 UI 相关 PR，预计该能力将在近期版本中落地。
*   **Gateway 功能对齐**：Issue [#30](https://github.com/nearai/ironclaw/issues/30) 追踪与 OpenClaw 的网关功能对齐，今日关闭，暗示核心功能已初步补齐。
*   **Reborn 架构推进**：PR [#4525](https://github.com/nearai/ironclaw/pull/4525) 增加了 Reborn 的 `onboard` 引导命令，PR [#4527](https://github.com/nearai/ironclaw/pull/4527) 增加了用户级 Skills 设置 UI，表明 "Reborn" 正在从底层重构走向用户体验和初始化闭环建设。
*   **多渠道工具路由**：PR [#1378](https://github.com/nearai/ironclaw/pull/1378) 提出基于 Channel 动态过滤 MCP 和内置工具，直击多端部署的痛点，有望成为下一版本的核心能力。

## 7. 用户反馈摘要
从 Issue [#3259](https://github.com/nearai/ironclaw/issues/3259) 的密集讨论中提炼出真实痛点：**下游生态对 crate 发布周期的稳定性有极高要求**。当底层依赖出现 CVE 时，如果 `crates.io` 发布断层，下游项目将面临“不升级有安全风险，升级又无包可用”的死锁困境。用户强烈建议 IronClaw 团队建立更自动化、高频的发布机制，确保 GitHub Releases 与 crates.io 同步。

## 8. 待处理积压
*   **🚨 Release 严重阻塞**：PR [#3708](https://github.com/nearai/ironclaw/pull/3708)（发布 0.29.1）自 5月16日 创建至今仍处于 Open 状态，这是导致上述 Issue [#3259](https://github.com/nearai/ironclaw/issues/3259) 安全漏洞无法修复的直接原因，**强烈建议维护者优先处理并推进发布**。
*   **大型功能 PR 审查瓶颈**：PR [#1378](https://github.com/nearai/ironclaw/pull/1378)（多渠道 MCP 路由，XL 体积）自 3月18日 开启至今未合并；今日新增的多个 XL 级 PR（[#4527](https://github.com/nearai/ironclaw/pull/4527), [#4526](https://github.com/nearai/ironclaw/pull/4526), [#4511](https://github.com/nearai/ironclaw/pull/4511)）进一步增加了审查队列压力，需合理分配 Review 资源。
*   **依赖更新堆积**：Dependabot 提交的 PR（[#4503](https://github.com/nearai/ironclaw/pull/4503) 涉及 38 个更新，[#4002](https://github.com/nearai/ironclaw/pull/4002) 涉及 16 个 Actions 更新）长期待合并，易引发后续合并冲突，需定期专项清理。

</details>

<details>
<summary><strong>LobsterAI</strong> — <a href="https://github.com/netease-youdao/LobsterAI">netease-youdao/LobsterAI</a></summary>

# LobsterAI 项目动态日报 (2026-06-07)

## 1. 今日速览
LobsterAI 今日社区端呈现较高活跃度，共有 16 条 Issue 更新，但项目整体研发推进处于停滞状态。过去 24 小时内，项目无新版本发布、无 PR 合并与关闭、无任何 Issue 被解决。当前活跃度完全由社区反馈驱动，核心表现为大量历史 Bug 被重新标记为 `[stale]`，以及用户新增的关于 Token 损耗和任务连续性的痛点反馈。维护者对社区反馈的响应存在严重滞后，项目健康度亮起红灯，亟需核心团队介入修复阻塞流程与状态同步类 Bug。

## 2. 版本发布
本日无新版本发布。

## 3. 项目进展
本日无合并或关闭的 PR。项目代码库在过去 24 小时内未发生任何实质性向前推进，所有开放 Issue 均处于悬而未决的状态。

## 4. 社区热点
今日讨论最活跃、用户痛点最集中的议题主要集中在**核心交互阻塞**与**Token 成本损耗**上：
- **[#1509 skills文件生成阻塞无感知，同模型理解能力有偏差](https://github.com/netease-youdao/LobsterAI/issues/1509)**（2条评论）：用户反馈在使用 skill-creator 时长时间阻塞且无中间态展示，且同模型下需求理解能力不及竞品，直接触达 AI 助手核心体验痛点。
- **[#2121 重复输出文字导致Token浪费的疑问](https://github.com/netease-youdao/LobsterAI/issues/2121)**（新开 Issue）：用户怀疑 AI 出现重复输出文字的现象，担忧大量消耗 Token。这反映了 LLM 应用中用户对成本极度敏感。
- **[#2120 关于任务连续性与执行时长的建议](https://github.com/netease-youdao/LobsterAI/issues/2120)**：用户提出预输入任务队列、延长单次任务监控时长等建议，对标竞品 WorkBuddy，反映了重度用户对 Agent 自主执行能力的更高要求。

## 5. Bug 与稳定性
今日暴露的 Bug 多为影响核心流程的状态同步与静默失败问题，目前均无对应 fix PR。按严重程度排列如下：

**严重：**
- **[#1516 GitHub Copilot OAuth认证成功后Token静默丢失](https://github.com/netease-youdao/LobsterAI/issues/1516)**：关闭 Settings 面板后轮询未取消，导致认证成功但 Token 无法写回 UI 并被静默丢弃，属于严重的可用性阻断 Bug。
- **[#1509 skills文件生成阻塞且无中间态展示](https://github.com/netease-youdao/LobsterAI/issues/1509)**：任务执行死锁且无报错提示，用户完全失去控制权。

**较高：**
- **[#1500 禁用技能后仍保留在activeSkillIds中被调用](https://github.com/netease-youdao/LobsterAI/issues/1500)** & **[#1502 保存技能列表后当前会话未同步](https://github.com/netease-youdao/LobsterAI/issues/1502)**：Redux 状态与 UI/后台逻辑不一致，导致技能系统的行为完全不可预测。
- **[#1506 定时任务IM通知静默失败](https://github.com/netease-youdao/LobsterAI/issues/1506)**：会话列表为空时仍可提交，导致运行时通知丢失且无报错，严重影响可靠性。

**中等：**
- **[#1512 QQ Bot群组白名单UI缺少输入框](https://github.com/netease-youdao/LobsterAI/issues/1512)**：白名单功能在 UI 层面形同虚设。
- **[#1504 Popo的AES Key未进行必填校验](https://github.com/netease-youdao/LobsterAI/issues/1504)**：表单校验缺失。

**低/基础设施：**
- **[#1518 CI Labeler权限错误及lint策略说明](https://github.com/netease-youdao/LobsterAI/issues/1518)**：影响 CI 流程，但不影响终端用户。
- **[#1513 声明条款内容排版规范问题](https://github.com/netease-youdao/LobsterAI/issues/1513)**：UI 文本显示瑕疵。

## 6. 功能请求与路线图信号
今日涌现大量关于**信息组织与效率管理**的功能请求，结合当前零 PR 的现状，这些需求短期内难以落地，但指明了产品演进方向：
- **标签与分类系统**：[#1541 会话列表标签分类和筛选](https://github.com/netease-youdao/LobsterAI/issues/1541)、[#1525 会话列表颜色标注](https://github.com/netease-youdao/LobsterAI/issues/1525)。用户面临会话数量爆炸，急需多维度管理工具。
- **信息检索与回溯**：[#1537 长会话消息收藏/书签功能](https://github.com/netease-youdao/LobsterAI/issues/1537)。AI 生成内容的二次利用能力缺失。
- **数据导出与统计**：[#1528 批量导出会话](https://github.com/netease-youdao/LobsterAI/issues/1528)、[#1532 本地使用统计面板](https://github.com/netease-youdao/LobsterAI/issues/1532)。数据资产沉淀需求显现。
- **执行连续性**：[#2120 任务预输入队列](https://github.com/netease-youdao/LobsterAI/issues/2120)。Agent 从单次对话向连续执行工作流的升级信号。

## 7. 用户反馈摘要
从今日 Issue 中可提炼出以下真实用户痛点与场景：
1. **状态不一致引发信任危机**：禁用技能依然生效、保存设置不生效需重启（#1500, #1502），使得用户对系统当前状态产生怀疑，无法确认 Agent 到底用了什么能力。
2. **静默失败极度消耗耐心**：OAuth 丢失（#1516）、IM 通知不发（#1506）均无报错，用户以为操作成功实则失效，体验极差。
3. **对 Token 成本极其敏感**：重复输出（#2121）引发用户对 Token 浪费的担忧，在 AI 应用中，成本透明度和控制是核心诉求。
4. **重度用户需要工作流支撑**：用户已不满足于单轮问答，需要任务排队（#2120）、长时间脚本监控、批量导出（#1528）等生产力工具特性，将 LobsterAI 视为工作流节点而非简单聊天框。

## 8. 待处理积压
项目存在严重的 Issue 积压与维护缺位问题。今日活跃的 16 条 Issue 中，有 14 条创建于 2026-04-07，距今已满两个月，均被标记为 `[stale]` 且无官方回应，其中包含多个严重阻断流程的 Bug。以下重要积压需维护者立即关注：
- **认证与状态类**：[#1516 OAuth Token丢失](https://github.com/netease-youdao/LobsterAI/issues/1516)、[#1500 技能状态不同步](https://github.com/netease-youdao/LobsterAI/issues/1500)、[#1502 会话未同步](https://github.com/netease-youdao/LobsterAI/issues/1502)
- **核心流程阻塞**：[#1509 Skills生成阻塞](https://github.com/netease-youdao/LobsterAI/issues/1509)
- **基础设施**：[#1518 CI权限报错](https://github.com/netease-youdao/LobsterAI/issues/1518)

⚠️ **项目健康度预警**：0 PR、0 Close 的数据表明项目可能处于休眠或核心团队资源严重不足的状态，建议开源社区及网易有道相关团队尽快确认项目维护计划，避免社区贡献者流失。

</details>

<details>
<summary><strong>TinyClaw</strong> — <a href="https://github.com/TinyAGI/tinyagi">TinyAGI/tinyagi</a></summary>

过去24小时无活动。

</details>

<details>
<summary><strong>Moltis</strong> — <a href="https://github.com/moltis-org/moltis">moltis-org/moltis</a></summary>

# Moltis 项目动态日报 (2026-06-07)

## 1. 今日速览
2026-06-07，Moltis 项目保持中度活跃，核心开发者与社区持续推动功能迭代与问题修复。今日无新版本发布，亦无 PR 或 Issue 被合并或关闭，当前有 3 个活跃 PR 等待审核，2 个新开/活跃 Issue 引发讨论。核心开发者 s-salamatov 集中优化了 Telegram 流式输出修复、上下文重载容量限制及频道日志可见性控制，项目在多渠道接入体验和细粒度权限配置上稳步推进。整体来看，项目处于功能打磨与企业级管控增强的并行阶段，但需关注社区安全相关 Bug 的响应速度。

## 2. 版本发布
今日无新版本发布。

## 3. 项目进展
今日虽无合并或关闭的 PR，但从活跃的待合并 PR 可以看出项目正在以下几个重要方向取得实质性进展：
*   **Telegram 集成热修复**：[PR #1113](https://github.com/moltis-org/moltis/pull/1113) 修复了在禁用完成通知时，Telegram 流式输出最后一轮回复缺失的问题，提升了即时通讯渠道的对话完整性与用户体验。
*   **上下文与性能优化**：[PR #1089](https://github.com/moltis-org/moltis/pull/1089) 对持久化的工具调用结果在重载前增加了容量限制，并全面应用于常规聊天、流式输出、重试及压缩等场景，有助于防止历史记录过长导致的内存溢出或性能衰退。
*   **权限与可见性精细化**：[PR #1093](https://github.com/moltis-org/moltis/pull/1093) 引入了账号、频道、用户三级联动的活动日志可见性设置，支持 `all/errors_only/off` 模式，显著增强了多租户或多用户场景下的隐私管控能力。

## 4. 社区热点
今日社区反馈与讨论主要集中在移动端体验与私有化部署配置两个方面：
*   [Issue #1107](https://github.com/moltis-org/moltis/issues/1107)：用户 IlyaBizyaev 请求在移动端 Web UI 增加多行文本输入支持。该 Issue 反映出移动端用户在输入长提示词时的交互痛点，目前已有 1 条评论探讨实现方案。
*   [Issue #1112](https://github.com/moltis-org/moltis/issues/1112)：用户 methompson 报告 Docker 环境下禁用鉴权未生效。此问题触及私有化部署的核心安全配置，引发 1 条评论，说明部分用户在内网开箱即用场景下遇到了阻碍。

## 5. Bug 与稳定性
今日报告的 Bug 按严重程度排列如下：
*   **[高] Docker 环境下禁用 Auth 失效**：[Issue #1112](https://github.com/moltis-org/moltis/issues/1112)。在 Docker 部署中关闭鉴权配置未能生效，可能导致内网暴露或未授权访问，属于安全与基础功能的双重缺陷。**目前暂无对应的 fix PR**，需维护者重点关注。
*   **[中] Telegram 流式输出最终回复丢失**：当 Telegram 流式输出开启但完成通知关闭时，最终回答未按预期作为流式最终回复处理，导致消息显示不完整。**已有 fix PR**：[PR #1113](https://github.com/moltis-org/moltis/pull/1113)，正在等待合并。

## 6. 功能请求与路线图信号
*   **移动端交互优化**：[Issue #1107](https://github.com/moltis-org/moltis/issues/1107) 提出的多行文本输入是移动端 Web UI 提升生产力的关键特性。随着 AI 助手在移动设备上的使用率提升，优化移动端输入体验是必然趋势，有望排入近期的路线图。
*   **日志与管控粒度增强**：[PR #1093](https://github.com/moltis-org/moltis/pull/1093) 引入的活动日志可见性设置，释放出 Moltis 正在向企业级/团队级应用迈进的信号。细粒度的日志权限控制意味着项目正在考虑多角色协同场景下的信息隔离需求，该 PR 合并后将构成下一版本的重要卖点。

## 7. 用户反馈摘要
*   **私有化部署的便利性期望**：从 [Issue #1112](https://github.com/moltis-org/moltis/issues/1112) 看出，部分用户希望在受信任的内网环境中能“零配置”免密使用 Moltis。当前 Docker 镜像在环境变量解析或配置覆盖上可能存在逻辑漏洞，导致用户预期与实际行为不符。
*   **移动端生产力受限**：[Issue #1107](https://github.com/moltis-org/moltis/issues/1107) 折射出用户在移动端进行复杂 Prompt 编写的需求，单行输入框严重制约了移动端作为生产力工具的潜力。
*   **第三方平台集成体验敏感**：[PR #1113](https://github.com/moltis-org/moltis/pull/1113) 暴露出在 Telegram 等第三方平台集成时，用户对 AI 回复的连贯性和完整性极其敏感，任何流式输出状态的异常都会严重影响对话体验。

## 8. 待处理积压
*   [PR #1089](https://github.com/moltis-org/moltis/pull/1089) 与 [PR #1093](https://github.com/moltis-org/moltis/pull/1093) 分别创建于 6月1日与 6月3日，至今日仅有作者自行更新，尚无其他维护者参与 Review。这两个 PR 涉及底层上下文架构优化和权限模型重构，属于核心逻辑变更，建议维护团队尽快介入评估，避免长期积压导致后续合并冲突。
*   [Issue #1112](https://github.com/moltis-org/moltis/issues/1112) 涉及 Docker 认证失效的安全隐患，建议维护者在 24 小时内确认并复现，防止带病发布。

</details>

<details>
<summary><strong>CoPaw</strong> — <a href="https://github.com/agentscope-ai/CoPaw">agentscope-ai/CoPaw</a></summary>

# CoPaw 项目动态日报 (2026-06-07)

## 1. 今日速览
CoPaw 项目今日整体活跃度中等偏上，主要集中在问题反馈与架构讨论。过去24小时内无新版本发布，也无 PR 合并记录，但新增了 6 条活跃 Issue 和 1 条处于审查状态的核心 PR 更新。社区反馈焦点集中在最新版 v1.1.10 的兼容性回归与前端交互细节上，尤其是本地 vLLM 部署模型无响应的问题。整体来看，项目正处于 ACP 协议扩展期与多模型编排架构的探索阶段，稳定性维护需提上日程。

## 2. 版本发布
今日无新版本发布。

## 3. 项目进展
今日无已合并或关闭的 PR。当前有 1 条重要 PR 正在审查中：
*   [PR #4949](https://github.com/agentscope-ai/QwenPaw/pull/4949) **feat(acp): advertise commands, surface errors, tool params, agent/model meta, file links**：该 PR 扩展了 QwenPaw 的 ACP (Agent Client Protocol) 服务器，为终端 UI（TUI）等客户端提供命令广播、错误展示、工具参数、Agent/Model 元数据及文件链接等元数据支持。此变更属于增量更新，标志着 CoPaw 在多端协议标准化和开发者生态体验上迈出了实质性步伐。

## 4. 社区热点
今日讨论最活跃的 Issues 围绕本地模型兼容性与多模态架构展开：
*   [Issue #4989](https://github.com/agentscope-ai/QwenPaw/issues/4989) **本地千问3.6-27B对话无响应**：评论数 2 条。该问题为严重的功能回归，影响了从 v1.1.5.post2 升级到 v1.1.9/1.1.10 的本地 vLLM 部署用户，反映出社区对 OpenAI 标准协议兼容性及版本升级平滑度的高度敏感。
*   [Issue #4992](https://github.com/agentscope-ai/QwenPaw/issues/4992) **支持独立视觉模型配置**：评论数 1 条。用户提出了“视觉中转站”的架构设想，击中了多模型协作编排的痛点，引发了关于如何解耦主模型逻辑能力与视觉能力的深层讨论。

## 5. Bug 与稳定性
今日报告的 Bug 按严重程度排列如下，目前均无对应 fix PR：
*   🔴 **高严重度** - [Issue #4989](https://github.com/agentscope-ai/QwenPaw/issues/4989)：v1.1.9 & 1.1.10 版本本地部署千问3.6-27B对话无响应（死循环加载），且后台无报错日志，严重影响本地化部署用户的核心对话流程，属于典型的回归缺陷。
*   🟡 **中严重度** - [Issue #4990](https://github.com/agentscope-ai/QwenPaw/issues/4990)：企业微信渠道在关闭工具调用信息时，返回兜底报错语（“抱歉，我无法回答你的问题”），影响渠道侧的交互体验与信息完整性。
*   🟢 **低严重度** - [Issue #4993](https://github.com/agentscope-ai/QwenPaw/issues/4993)：V1.1.10 前端页面图片预览放大后拖动出现严重抖动，属于前端 UI 交互细节缺陷，不影响核心业务逻辑。

## 6. 功能请求与路线图信号
*   **视觉模型降级/Fallback机制** ([Issue #4992](https://github.com/agentscope-ai/QwenPaw/issues/4992))：用户请求增加 `visual_model` 独立配置，当主模型（如 deepseek-v4-flash、LongCat-2.0）不支持多模态时，自动路由至视觉模型提取文本。这与当前 AI Agent 走向多模型混合编排的行业趋势高度契合，且属于配置层面的扩展，有较大概率被纳入下一版本的 Roadmap。
*   **扩展消息渠道** ([Issue #4886](https://github.com/agentscope-ai/QwenPaw/issues/4886))：请求接入 MAX Messenger（俄罗斯主流通讯平台）。符合项目 "Every channel" 的产品定位，此类渠道级 Feature 通常对社区贡献者开放，较易通过 PR 形式落地。

## 7. 用户反馈摘要
*   **痛点**：版本升级带来的兼容性断裂是当前最大痛点，尤其是 Docker 容器部署场景下，升级后核心功能失效且缺乏有效报错日志，排查困难；此外，企业微信等 IM 端的工具调用展示逻辑不够完善。
*   **使用场景**：大量用户采用 Docker + vLLM 本地化部署大模型（千问3.6-27B），并严格遵循 OpenAI API 协议对接；在多模态处理中，存在“主模型跑文本 + 便宜模型跑视觉”的成本优化组合需求。
*   **反馈**：用户对前端体验的要求日益提高（如图片拖拽抖动问题被迅速提出）；社区对“多模型路由协作”的呼声强烈，不再满足于单一的端到端大模型依赖。

## 8. 待处理积压
目前虽无长期未响应的陈年 Issue，但需警惕以下问题转化为积压：
*   **[Issue #4989](https://github.com/agentscope-ai/QwenPaw/issues/4989)（本地模型连接回归）**：由于涉及无日志报错的死锁/挂起，且影响面覆盖所有 v1.1.9 以上版本的本地 vLLM 用户，若官方不迅速介入排查，极易演变为阻碍版本迭代的严重积压问题，建议维护者最高优先级跟进。
*   **[PR #4949](https://github.com/agentscope-ai/QwenPaw/pull/4949)（ACP协议增强）**：该 PR 自 6月3日创建至今处于 Under Review 状态，涉及底层协议元数据的扩展，是 TUI 等第三方客户端的基石，建议推动尽快合入，避免分支长期偏离主干。

</details>

<details>
<summary><strong>ZeptoClaw</strong> — <a href="https://github.com/qhkm/zeptoclaw">qhkm/zeptoclaw</a></summary>

过去24小时无活动。

</details>

<details>
<summary><strong>ZeroClaw</strong> — <a href="https://github.com/zeroclaw-labs/zeroclaw">zeroclaw-labs/zeroclaw</a></summary>

# ZeroClaw 项目动态日报 (2026-06-07)

## 1. 今日速览
ZeroClaw 今日保持高活跃度，共处理 42 条 Issue 更新（24 新开/活跃，18 关闭）与 50 条 PR 更新（31 待合并，19 已合并/关闭），开源社区贡献与核心团队推进均呈健康态势。项目当前重心显著向**运行时安全与权限收敛**（如工具执行白名单、凭证脱敏）以及 **TUI (zerocode) 稳定性重建**倾斜。此外，WASM 插件架构的 RFC 引入及多家国内大模型厂商的原生 OAuth 接入需求，显示出项目向更开放架构与更广生态集成的明确信号。项目目前处于 v0.8.0 发布前的关键收敛期。

## 2. 版本发布
今日无新版本发布。根据 Issue [#7112](https://github.com/zeroclaw-labs/zeroclaw/issues/7112) (v0.8.0 release queue) 的活跃状态，项目正集中清理 Stable-tier 阻塞项，预计近期将发布正式版。

## 3. 项目进展
今日共有 19 个 PR 被合并或关闭，重点推进了安全修复与 TUI 体验重构：
- **安全漏洞修补**：PR [#6988](https://github.com/zeroclaw-labs/zeroclaw/pull/6988) 修复了高危 Bug，确保设备 Token 轮换/删除时旧 Bearer Token 立即失效；PR [#7177](https://github.com/zeroclaw-labs/zeroclaw/pull/7177) 在服务端强制排除了 ACP 会话的长期内存工具，防止越权。
- **TUI (zerocode) 稳定性重建**：PR [#7158](https://github.com/zeroclaw-labs/zeroclaw/pull/7158) 彻底重构了 TUI 断线重连逻辑，解决了守护进程断开导致 UI 冻结的 S1 级缺陷；PR [#7118](https://github.com/zeroclaw-labs/zeroclaw/pull/7118) 统一并修复了 TUI 的导航快捷键。
- **Provider 兼容性**：PR [#7180](https://github.com/zeroclaw-labs/zeroclaw/pull/7180) 修复了自定义与 OpenAI 兼容模型 `wire_api = "responses"` 不生效的问题，增强了自建模型端点的兼容性。
- **安全策略精准化**：PR [#7242](https://github.com/zeroclaw-labs/zeroclaw/pull/7242) 修复了路径策略对引号和 Heredoc 中 `~` 符号的误报。

## 4. 社区热点
- **多提供商原生 OAuth 接入诉求**：Issue [#5601](https://github.com/zeroclaw-labs/zeroclaw/issues/5601)（7 评论）讨论热烈，用户希望为零一万物、Kimi、MiniMax 等提供订阅制的 Provider 增加原生 OAuth 登录，免除静态 API Key 管理。该需求已被接受但目前处于 Blocked 状态。
- **运行时工具权限收敛**：Issue [#6914](https://github.com/zeroclaw-labs/zeroclaw/issues/6914) 与 [#6915](https://github.com/zeroclaw-labs/zeroclaw/issues/6915)（各 3 评论）引发对智能体执行工具时权限提升的讨论。社区关注如何在 Skill 执行期间临时激活受限工具，同时严格限制核心 Agent 的 `allowed_tools`，这是 ZeroClaw 走向企业级安全的必经之路。
- **WASM 插件架构 RFC**：Issue [#7338](https://github.com/zeroclaw-labs/zeroclaw/issues/7338) 与追踪 Issue [#7339](https://github.com/zeroclaw-labs/zeroclaw/issues/7339) 提出了 WASM Plugin 生命周期钩子的 RFC，旨在将目前未挂载的 15 个 Hook 暴露给 WASM 插件，这可能彻底改变 ZeroClaw 的扩展生态。

## 5. Bug 与稳定性
按严重程度排列今日重点 Bug：
- **S0 - 数据丢失/安全风险**：
  - [#7252](https://github.com/zeroclaw-labs/zeroclaw/issues/7252) [已关闭]：`session/kill` 无法彻底杀死 ACP 会话，会从持久化历史中重新唤醒。已通过内部提交修复。
  - [#6978](https://github.com/zeroclaw-labs/zeroclaw/issues/6978) [已关闭]：嵌套在对象数组中的 `#[secret]` 字段在配置显示时未脱敏。已修复。
- **S1 - 工作流阻塞**：
  - [#7043](https://github.com/zeroclaw-labs/zeroclaw/issues/7043) & [#7125](https://github.com/zeroclaw-labs/zeroclaw/issues/7125) [已关闭]：TUI 在 Daemon 断开后永久冻结。**已有修复 PR** [#7158](https://github.com/zeroclaw-labs/zeroclaw/pull/7158)。
  - [#6984](https://github.com/zeroclaw-labs/zeroclaw/issues/6984) [已关闭]：Token 轮换不撤销旧 Token。**已有修复 PR** [#6988](https://github.com/zeroclaw-labs/zeroclaw/pull/6988)。
- **S2 - 行为降级**：
  - [#7059](https://github.com/zeroclaw-labs/zeroclaw/issues/7059) [已关闭]：频道编排器中残留的“默认模型 Provider”回退逻辑违反 V3 Schema 规范，正在剔除。
  - [#6875](https://github.com/zeroclaw-labs/zeroclaw/issues/6875) [已关闭]：Llama 4 Scout 输出复数 `<tool_calls>` 标签导致解析失败。已修复。
  - [#7133](https://github.com/zeroclaw-labs/zeroclaw/issues/7133) [已关闭]：路径安全策略对命令行中引号内的 `~` 产生误报。**已有修复 PR** [#7242](https://github.com/zeroclaw-labs/zeroclaw/pull/7242)。

## 6. 功能请求与路线图信号
- **身份认证云原生化**：Issue [#7342](https://github.com/zeroclaw-labs/zeroclaw/issues/7342) 请求 Azure OpenAI 支持基于身份的认证，结合 #5601，可以看出社区强烈希望 ZeroClaw 摆脱硬编码 API Key，向云原生密钥管理靠拢。
- **TUI 实时控制增强**：PR [#7209](https://github.com/zeroclaw-labs/zeroclaw/pull/7209) 和 [#7190](https://github.com/zeroclaw-labs/zeroclaw/pull/7190) 正在为 TUI 引入会话内实时切换模型和出站消息队列功能，极大增强了交互式使用的灵活性，预计将很快合入主分支。
- **Cron 调度精细化**：PR [#7348](https://github.com/zeroclaw-labs/zeroclaw/pull/7348) 修复了关闭 `catch_up_on_startup` 时仍会执行逾期任务的问题，反映出 ZeroClaw 在自动化调度场景下的对时序控制的精细度要求在提高。

## 7. 用户反馈摘要
- **痛点**：静态 API Key 管理繁琐，特别是在使用具备订阅制的模型服务时，用户更期望无缝的 OAuth 体验；TUI 与 Daemon 的连接极其脆弱，一旦断线容易导致工作流中断；工具权限全局配置过于粗糙，容易导致权限放大的安全焦虑。
- **场景**：有用户在使用 WhatsApp/Telegram 等双工频道部署时，急需按频道限流（[#6345](https://github.com/zeroclaw-labs/zeroclaw/issues/6345)）以防止 Agent 刷屏；使用 Llama 4 等开源模型自建推理服务时，遇到 Tool Call 解析静默失败的开发阻塞。
- **满意度**：对核心团队响应 S0/S1 级安全漏洞（Token 不失效、密钥脱敏）的修复速度表示认可；对 TUI (`zerocode`) 近期密集的 UX 改进（重连、主题、模型切换）持积极期待态度。

## 8. 待处理积压
以下高优先级 Issue 处于 `blocked` 状态较久，需维护者重点关注以解除阻塞：
- [#5601](https://github.com/zeroclaw-labs/zeroclaw/issues/5601)：多家主流 Provider 原生 OAuth 支持（P2，Blocked，悬置近 2 个月）。
- [#5775](https://github.com/zeroclaw-labs/zeroclaw/issues/5775)：按 Skill 粒度控制脚本/命令执行权限（P2，Blocked，核心安全特性）。
- [#5607](https://github.com/zeroclaw-labs/zeroclaw/issues/5607)：Cron 任务的 Pre-hook 前置门控机制（P2，Blocked）。
- [#6917](https://github.com/zeroclaw-labs/zeroclaw/issues/6917)：Composio 工具调度的 Action 级过滤（P2，Blocked）。
- [#5908](https://github.com/zeroclaw-labs/zeroclaw/issues/5908)：Debian 容器镜像的 CI/CD 构建与发布（P2，Blocked，影响部署规范化）。

</details>

---
*本日报由 [agents-radar](https://github.com/yanzi6039/agents-radar) 自动生成。*