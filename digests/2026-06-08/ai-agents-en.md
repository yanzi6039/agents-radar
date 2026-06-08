# OpenClaw Ecosystem Digest 2026-06-08

> Issues: 299 | PRs: 500 | Projects covered: 13 | Generated: 2026-06-08 01:03 UTC

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

## OpenClaw Deep Dive

# OpenClaw Project Digest — 2026-06-08

## 1. Today's Overview
OpenClaw experienced high activity over the past 24 hours, with 500 pull requests updated (126 merged/closed) and 299 issues updated (119 closed). The robust closure rate indicates active maintainer engagement, though the sheer volume of open PRs (374) suggests a potential review bottleneck. No new releases were cut today. The community remains heavily focused on session state reliability, messaging channel fidelity, and cron execution isolation, with several critical P1 regressions requiring immediate attention.

## 2. Releases
No new releases were published today.

## 3. Project Progress
Significant progress was made across channel integrations, agent runtime execution, and memory management, with 126 PRs merged or closed:
*   **Agent & Subagent Execution:** PR [#78441](https://github.com/openclaw/openclaw/pull/78441) advances subagent tooling by forwarding `toolsAllow` from `sessions_spawn`. PR [#91206](https://github.com/openclaw/openclaw/pull/91206) fixes a missing model parameter in `spawnSubagentDirect`, and PR [#91076](https://github.com/openclaw/openclaw/pull/91076) addresses a Codex regression where orphan tool calls suppressed assistant replies.
*   **Security & Config:** PR [#91286](https://github.com/openclaw/openclaw/pull/91286) fixes an inverted `minSecurity` order that mistakenly treated `"full"` as the most restrictive setting. PR [#91268](https://github.com/openclaw/openclaw/pull/91268) ensures `trusted-proxy` setups count as authentication proof.
*   **Messaging & Channels:** PR [#90858](https://github.com/openclaw/openclaw/pull/90858) overhauls iMessage split-send coalescing using structural metadata. PR [#89659](https://github.com/openclaw/openclaw/pull/89659) adds crucial retry logic for Feishu send rate-limits. PR [#89045](https://github.com/openclaw/openclaw/pull/89045) recovers terminal session statuses on visible inbound turns to prevent silent message drops.
*   **Memory & State:** PR [#90101](https://github.com/openclaw/openclaw/pull/90101) introduces runtime self-context config and tooling, advancing the cost/scale-awareness effort. PR [#91275](https://github.com/openclaw/openclaw/pull/91275) fixes QMD memory rebind detection for changed roots.

## 4. Community Hot Topics
*   **Text Leaking to Channels (27 comments):** Issue [#25592](https://github.com/openclaw/openclaw/issues/25592) remains a major pain point. Internal processing text between tool calls is being routed as visible messages to users on Slack/iMessage, representing a significant UX and security failure.
*   **SQLite Migration Strategy (18 comments):** Issue [#88838](https://github.com/openclaw/openclaw/issues/88838) sparked deep architectural discussion on tracking the session/transcript migration to SQLite via a branch-by-abstraction seam to avoid high-risk rewrites.
*   **Codex Turn-Completion Stall (14 comments):** Issue [#88312](https://github.com/openclaw/openclaw/issues/88312) reports a regression where multi-tool agent turns fail with "Codex stopped before confirming the turn was complete" on the 2026.5.27 release.
*   **agentDir Bootstrap Ignored (14 comments):** Issue [#29387](https://github.com/openclaw/openclaw/issues/29387) highlights frustration that per-agent `agentDir` configurations are silently bypassed, breaking agent portability.
*   **Cron Contamination (13 comments):** Issue [#90991](https://github.com/openclaw/openclaw/issues/90991) details how cron scheduled triggers contaminate global runtime state, causing system-wide transient overloads.

## 5. Bugs & Stability
*   **P1 - Security/Message Loss:** Text between tool calls leaks to messaging channels ([#25592](https://github.com/openclaw/openclaw/issues/25592)). No fix PR is linked yet.
*   **P1 - Regression (Session State):** Codex app-server turn-completion stall returns errors ([#88312](https://github.com/openclaw/openclaw/issues/88312)). Fix is likely addressed by PR [#91076](https://github.com/openclaw/openclaw/pull/91076).
*   **P1 - Regression (Security):** `exec` tool does not inherit `skills.entries.*.env` environment variables, breaking secret injection ([#31583](https://github.com/openclaw/openclaw/issues/31583)).
*   **P1 - Data Loss:** The `write` tool lacks append mode, causing isolated cron sessions to destroy shared workspace files ([#40001](https://github.com/openclaw/openclaw/issues/40001)).
*   **P1 - Overload:** Cron triggers contaminate global runtime state, causing system-wide failures ([#90991](https://github.com/openclaw/openclaw/issues/90991)). Fix potentially related to PR [#91287](https://github.com/openclaw/openclaw/pull/91287) which de-duplicates cron system events.
*   **P1 - Message Loss:** Delivery-recovery starts before channel transports are ready after a gateway restart, silently dropping messages ([#91212](https://github.com/openclaw/openclaw/issues/91212)).
*   **P1 - Cost/Wedge:** Compaction safeguard mode allows sessions to grow to the context ceiling, causing costly wedges ([#90639](https://github.com/openclaw/openclaw/issues/90639)).

## 6. Feature Requests & Roadmap Signals
*   **Post-Subagent Hooks:** Request for an extension hook (`post_subagent_complete`) to auto-generate trajectory files after subagent finishes ([#22358](https://github.com/openclaw/openclaw/issues/22358)). Aligns with recent active PRs around subagent tool forwarding.
*   **Topic-Session Families:** Request for one assistant to support multiple named context lanes with isolated transcripts but shared durable memory ([#90916](https://github.com/openclaw/openclaw/issues/90916)). 
*   **Gateway-Lite Mode:** Proposal for a deployment mode without an AI harness for deterministic plugins and webhooks ([#86881](https://github.com/openclaw/openclaw/issues/86881)).
*   **Append Semantics for Memory:** Request for validated append guardrails for pre-compaction memory flushes ([#90354](https://github.com/openclaw/openclaw/issues/90354)).
*   **Roadmap Prediction:** The active development on memory/state (PR [#91274](https://github.com/openclaw/openclaw/pull/91274), PR [#91275](https://github.com/openclaw/openclaw/pull/91275)) and runtime context (PR [#90101](https://github.com/openclaw/openclaw/pull/90101)) suggests the next release will heavily feature SQLite state migration completion and context-window cost controls.

## 7. User Feedback Summary
Users are expressing significant frustration with **silent data and message loss**, particularly regarding the lack of an append mode for the write tool ([#40001](https://github.com/openclaw/openclaw/issues/40001)) and delivery recovery failures on restart ([#91212](https://github.com/openclaw/openclaw/issues/91212)). **Session state fragility** is another major pain point, with users reporting that WebSocket reconnects terminate sessions ([#38091](https://github.com/openclaw/openclaw/issues/38091)) and compaction wedges cause unrecoverable "Something went wrong" errors ([#90639](https://github.com/openclaw/openclaw/issues/90639)). Multi-model users are also struggling with configurations that use absolute token thresholds, which break when switching between models with different context windows ([#87136](https://github.com/openclaw/openclaw/issues/87136)). On the positive side, the community is highly engaged in proposing architectural improvements (like SQLite migration seams and topic-session families) rather than just reporting bugs.

## 8. Backlog Watch
*   **[#25592](https://github.com/openclaw/openclaw/issues/25592) - Text leaking to messaging channels:** A P1 security/message-loss issue open since Feb 2026. Tagged `clawsweeper:needs-security-review` and `clawsweeper:needs-product-decision`, but still lacking a fix PR.
*   **[#29387](https://github.com/openclaw/openclaw/issues/29387) - Bootstrap files in agentDir silently ignored:** A P1 regression open since Feb 2026 that breaks per-agent configuration portability.
*   **[#31583](https://github.com/openclaw/openclaw/issues/31583) - `exec` tool doesn't inherit skill environment variables:** A P1 regression blocking secret injection, open since March 2026.
*   **[#40001](https://github.com/openclaw/openclaw/issues/40001) - Write tool lacks append mode:** A P1 data-loss issue open since March 2026.
*   **PR [#78441](https://github.com/openclaw/openclaw/pull/78441) - feat(subagents): forward toolsAllow:** An M-sized feature PR ready for maintainer review since May 2026, potentially blocked by compatibility and security boundary checks.

---

## Cross-Ecosystem Comparison

## 1. Ecosystem Overview
The open-source personal AI assistant and agent ecosystem is currently characterized by high-velocity iteration split between foundational architectural overhauls and intense reliability hardening. Projects are transitioning from single-model chatbots to complex, multi-agent orchestrators, driving a massive demand for robust context management, provider routing flexibility, and secure sandboxing. While core frameworks like OpenClaw and IronClaw push the boundaries of multi-tenant and enterprise-grade infrastructure, lighter-weight projects are rapidly iterating on local deployment ergonomics and UI/TUI experiences. Across the board, "silent failures"—where systems degrade or drop data without erroring—are eroding user trust, making observability and fail-closed mechanics the primary focus of current development efforts.

## 2. Activity Comparison

| Project | Issues Updated | PRs Updated | Merged/Closed PRs | Release Status | Health Score |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **OpenClaw** | 299 | 500 | 126 | No release | **High** (Review bottleneck risk) |
| **IronClaw** | 50 | 38 | 16 | Pending (0.29.1) | **High** (Active overhaul) |
| **ZeroClaw** | 50 | 50 | 11 | Imminent (v0.8.0) | **High** (Pre-release stabilization) |
| **Hermes Agent**| 50 | 50 | 6 | No release | **Medium** (High P1 regression count) |
| **PicoClaw** | 17 closed | 12 merged | 12 | Nightly cut | **High** (Stabilizing rapidly) |
| **NanoBot** | 7 | 18 | 4 | No release | **Good** (Healthy contributor cycle) |
| **NanoClaw** | 3 | 9 | 3 | No release | **Medium** (Security/onboarding gaps) |
| **CoPaw** | 5 | 2 | 0 | No release | **Medium** (Regressions blocking users) |
| **Moltis** | 1 | 3 | 0 | No release | **Stable** (Slow accumulation phase) |
| **LobsterAI** | 15 (stale) | 0 | 0 | No release | **Low** (Stagnant) |
| **NullClaw / TinyClaw / ZeptoClaw** | 0 | 0 | 0 | No release | **Inactive** |

## 3. OpenClaw's Position
*   **Advantages vs. Peers:** OpenClaw operates at a scale of community engagement and raw PR throughput (500 PRs updated) that dwarfs most of the ecosystem. Its dominance in enterprise messaging channel fidelity (Slack, iMessage, Feishu) and advanced agentic execution (subagent tooling, cron isolation) keeps it as the core reference implementation.
*   **Technical Approach Differences:** While IronClaw is chasing a ground-up "Reborn" microservices/WASM architecture and ZeroClaw is optimizing for local TUI/Desktop provider-routing, OpenClaw is evolving its monolith via "branch-by-abstraction" (e.g., the SQLite session migration). This pragmatic approach maintains continuity but introduces severe merge/review bottlenecks (374 open PRs).
*   **Community Size Comparison:** OpenClaw’s community is an order of magnitude larger in raw activity than ZeroClaw or Hermes. However, its issue-to-maintainer ratio is strained; P1 security flaws like text leaking to channels have languished since February, whereas smaller projects like PicoClaw and NanoBot resolve critical sandboxing bugs within days.

## 4. Shared Technical Focus Areas
*   **Context & Memory Management:** Token waste and context fragility are universal pains. OpenClaw is battling context-ceiling wedges and migrating to SQLite; Moltis is capping persisted tool results before rehydration; CoPaw is requesting self-evolving hierarchical memory; and LobsterAI users are reporting costly generation loops.
*   **Multi-Agent & Interoperability:** Standardized inter-agent communication is a rising requirement. Hermes Agent and ZeroClaw both show high community demand for Google's A2A protocol, while OpenClaw is actively merging subagent tooling forwarding (`toolsAllow`).
*   **Provider Flexibility & Fallbacks:** Users refuse to be locked into single providers. ZeroClaw merged per-alias model fallbacks; CoPaw users want decoupled "Visual Model Fallbacks" (pairing cheap text LLMs with separate vision models); and PicoClaw is integrating Omniroute.
*   **Security & Sandboxing:** Container and execution isolation is actively breaking on modern OSs. NanoBot is struggling with `bwrap` on Ubuntu 24.04; NanoClaw has an ungated `create_agent` MCP tool allowing container escapes; and IronClaw is implementing WASM and `openat2` hardening to prevent tenant sandbox escapes.
*   **Deployment Friction:** Onboarding is failing across the board. ZeroClaw users struggle with Docker `file_write` silent failures; NanoClaw's recommended CLI path hangs; and Hermes users want a "Thin Client" Desktop mode to bypass local runtime setup.

## 5. Differentiation Analysis
*   **Feature Focus:** OpenClaw and Moltis differentiate on enterprise channel integrations and compliance. IronClaw is uniquely focused on multi-tenant architecture and Configuration-as-Code. CoPaw is leaning into modular multimodal routing (Qwen/Visual relay). ZeroClaw is prioritizing local power-user experience (TUI, provider-fallback routing).
*   **Target Users:** IronClaw and NanoClaw explicitly target multi-tenant and containerized enterprise deployments. PicoClaw targets edge/ARM users (Android/Termux, Raspberry Pi). Hermes Agent targets Desktop power-users needing gateway connections. LobsterAI targets casual/enterprise IM users but is currently failing to retain them.
*   **Technical Architecture:** IronClaw (Rust/WASM microservices), OpenClaw (Node/TypeScript monolith transitioning via seams), PicoClaw (Go-based edge-focused), NanoBot (Python-focused with deep MCP integration).

## 6. Community Momentum & Maturity
*   **Tier 1 - Rapid Iteration:** OpenClaw, IronClaw, and ZeroClaw are pushing massive PR/Issue volumes (50-500 updates/day). They are driving the ecosystem's frontier but face significant technical debt and review bottlenecks.
*   **Tier 2 - Rapid Stabilization:** PicoClaw, NanoBot, and Hermes Agent. These projects are merging <20 PRs/day, heavily focused on defensive coding, fixing sandboxing, and resolving P1 regressions before shipping new features. 
*   **Tier 3 - Niche/Stalled:** NanoClaw, CoPaw, and Moltis show moderate to low activity, often blocked by specific architectural bugs or slow maintainer review cycles. LobsterAI is functionally stagnant, with a backlog of untriaged stale issues.

## 7. Trend Signals
1.  **The "Silent Failure" Intolerance:** Users are explicitly rejecting systems that swallow errors. From OpenClaw dropping messages on gateway restarts to NanoClaw's credential proxy silently falling back to defaults and CoPaw's vLLM hanging without logs, the industry demand is shifting to fail-closed architectures with loud, observable errors.
2.  **Cost-Aware Agentic Routing:** Token waste is a top-tier concern. ZeroClaw users want skill compilation instead of raw `SKILL.md` injection; CoPaw wants visual relays to avoid running expensive multimodal models on text tasks; OpenClaw is building runtime self-context config. Developers must build systems that are context-window aware and dynamically route to cheap models where possible.
3.  **A2A and Multi-Agent Standardization:** The high engagement on A2A protocol requests (Hermes, ZeroClaw) and subagent orchestration (OpenClaw) indicates the market is moving past single-agent loops. Developers should anticipate a near-future standard for agent-to-agent delegation and build tooling that supports external agent meshing, not just internal sub-agent calls.

---

## Peer Project Reports

<details>
<summary><strong>NanoBot</strong> — <a href="https://github.com/HKUDS/nanobot">HKUDS/nanobot</a></summary>

### 1. Today's Overview
NanoBot exhibited robust development activity over the past 24 hours, with 18 pull requests and 7 issues updated. The maintainers and community are heavily focused on hardening the agent's infrastructure, as seen by the swift identification and patching of session history and sandbox isolation bugs. Four PRs were successfully merged/closed, advancing WebUI functionality and provider compatibility. Without a new release cut today, the current momentum suggests the project is accumulating stability patches and feature enhancements for a forthcoming minor or patch version.

### 2. Releases
No new releases were published today.

### 3. Project Progress
Four PRs were closed/merged today, marking tangible progress in UI rendering, provider support, and messaging channels:
*   **WebUI Enhancement:** PR [#4240](https://github.com/HKUDS/nanobot/pull/4240) introduced ANSI output rendering inside WebUI code blocks, greatly improving the readability of terminal tool outputs.
*   **Provider Compatibility:** PR [#4227](https://github.com/HKUDS/nanobot/pull/4227) fixed a bug where empty-string `reasoning_content` was incorrectly coerced to `None`, ensuring proper functionality with custom providers like DeepSeek and Kimi (resolves Issue [#4105](https://github.com/HKUDS/nanobot/issues/4105)).
*   **Channel Fixes:** PR [#2885](https://github.com/HKUDS/nanobot/pull/2885) resolved Feishu mention resolution and access token initialization (related to Issue [#2256](https://github.com/HKUDS/nanobot/issues/2256)). PR [#2663](https://github.com/HKUDS/nanobot/pull/2663) fixed WhatsApp group mention detection for LID and device-suffixed bot JIDs.

### 4. Community Hot Topics
The most actively discussed items revolve around context management and sandbox execution, reflecting core pain points for AI agent reliability:
*   **Session History Truncation (Issue [#4203](https://github.com/HKUDS/nanobot/issues/4203)):** Reported with 2 comments, this bug causes the entire message history to be dropped when orphaned tool results follow a user message. The underlying need is for robust and predictable context window management, which is critical for long-running agent sessions.
*   **Sandbox Isolation on Modern Linux (Issues [#4236](https://github.com/HKUDS/nanobot/issues/4236) & [#4237](https://github.com/HKUDS/nanobot/issues/4237)):** Users are running into `bwrap` sandbox failures on Ubuntu 24.04 and issues with the `$HOME` environment variable. This highlights a strong community demand for secure, out-of-the-box sandboxing that doesn't break host OS compatibility or file-writing capabilities.

### 5. Bugs & Stability
Several bugs were reported today, ranked by severity, with maintainers and community members quick to propose fixes:
1.  **High - Session History Wipe:** Issue [#4203](https://github.com/HKUDS/nanobot/issues/4203) causes all messages to be dropped due to orphaned tool results. **Fix exists:** PR [#4219](https://github.com/HKUDS/nanobot/pull/4219).
2.  **High - bwrap Fails on Ubuntu 24.04:** Issue [#4236](https://github.com/HKUDS/nanobot/issues/4236) causes silent aborts due to restricted unprivileged user namespaces. (No fix PR yet).
3.  **Medium - bwrap HOME Env Breaks Tool Writes:** Issue [#4237](https://github.com/HKUDS/nanobot/issues/4237) restricts write access inside the sandbox. **Fix exists:** PR [#4239](https://github.com/HKUDS/nanobot/pull/4239).
4.  **Medium - API Duplicates User Turns:** Issue highlighted in PR [#4234](https://github.com/HKUDS/nanobot/pull/4234) where empty-response retries in the OpenAI-compatible API duplicate user turns in session history. **Fix exists:** PR [#4234](https://github.com/HKUDS/nanobot/pull/4234).
5.  **Low - MCP Timeout Hangs:** PR [#4230](https://github.com/HKUDS/nanobot/pull/4230) addresses MCP startup waiting indefinitely due to `timeout=None` in `streamableHttp`. **Fix exists:** PR [#4230](https://github.com/HKUDS/nanobot/pull/4230).

### 6. Feature Requests & Roadmap Signals
*   **Subagent Model Override (Issue [#4231](https://github.com/HKUDS/nanobot/issues/4231)):** A request to add a `model` parameter to the `spawn` tool. This signals that users are building complex, cost-optimized multi-agent workflows where cheap/fast models handle sub-tasks while powerful models orchestrate. This is a strong candidate for the next feature release.
*   **WebUI Version Display (Issue [#4233](https://github.com/HKUDS/nanobot/issues/4233)):** Users want to see the current version and available updates in the UI. **Already implemented:** PR [#4235](https://github.com/HKUDS/nanobot/pull/4235) adds this to the Settings Overview, along with a PyPy update checker.
*   **Shared Voice Input (PR [#4232](https://github.com/HKUDS/nanobot/pull/4232)):** An open PR elevating transcription from a channel-specific setting to a shared capability, signaling a roadmap move toward unified multimodal input across all interfaces.

### 7. User Feedback Summary
*   **Pain Points:** Users are experiencing friction with the `bwrap` sandbox on modern Linux distributions, leading to silent execution failures. Additionally, the context governance logic (microcompaction and legal message boundaries) has proven brittle when handling edge cases like orphaned tool messages, causing frustration through lost conversational context.
*   **Use Cases:** The bug reports reveal that users are actively using NanoBot with custom LLM providers (DeepSeek, Kimi) and leveraging it in team messaging environments (Feishu, WhatsApp). The subagent model request shows maturing usage patterns involving hierarchical agent delegation.
*   **Satisfaction:** The community is highly engaged and proactive; several issue authors (e.g., `primit1v0`, `michaelxer`) immediately opened corresponding fix PRs, indicating a healthy, contributor-friendly project ecosystem.

### 8. Backlog Watch
Several critical security and stability PRs have been open for over a week and require maintainer attention to merge:
*   **Security PRs:** PR [#4123](https://github.com/HKUDS/nanobot/pull/4123) (SSRF guard for MCP HTTP URLs, open since May 31) and PR [#4119](https://github.com/HKUDS/nanobot/pull/4119) (Blocking relative symlink workspace escapes, open since May 31) are crucial for preventing severe vulnerability exploits.
*   **Filesystem Safety:** PR [#4053](https://github.com/HKUDS/nanobot/pull/4053) (Keeping read-only roots out of write paths, open since May 29) needs review to prevent accidental workspace corruption.
*   **Long-running Channel PR:** PR [#2885](https://github.com/HKUDS/nanobot/pull/2885) (Feishu fix) was finally closed today after being open since April 7th; maintainers should ensure the review process for channel-specific fixes is streamlined to avoid similar bottlenecks.

</details>

<details>
<summary><strong>Hermes Agent</strong> — <a href="https://github.com/nousresearch/hermes-agent">nousresearch/hermes-agent</a></summary>

# Hermes Agent Project Digest (2026-06-08)

## 1. Today's Overview
Hermes Agent experienced high community engagement today, with 50 issues and 50 pull requests updated in the last 24 hours. The project is currently in an active bug-fixing and hardening phase, as evidenced by 44 open PRs (mostly fixes) and 47 open issues, with only 3 issues and 6 PRs closing. There were no new releases today. The high volume of open bugs—particularly P1 and P2 regressions affecting config migration, gateway stability, and Desktop UI—suggests the project is navigating growing pains typical of rapid feature expansion, especially around its Desktop client and multi-platform gateway adapters.

## 2. Releases
No new releases were published today.

## 3. Project Progress
Six PRs were merged/closed today, advancing stability and documentation:
*   **Vision Tool Fixed:** [PR #9077](https://github.com/NousResearch/hermes-agent/issues/9077) (Issue) was closed, resolving the critical `vision_analyze` bug where local and URL images returned "no image".
*   **Gateway Shutdown Hardened:** [PR #40607](https://github.com/NousResearch/hermes-agent/pull/40607) was closed, bounding `ws.close()` timeout so idle servers can't stall shutdowns.
*   **Test Reliability:** [PR #41334](https://github.com/NousResearch/hermes-agent/pull/41334) repaired stale Discord component-view test fixtures for fail-closed auth.
*   **Localization:** [PR #40578](https://github.com/NousResearch/hermes-agent/pull/40578) added an Urdu (ur-PK) README translation.

Active progress is heavily focused on Windows/Gateway reliability and CLI crashes, with maintainers actively pushing fixes for session restore crashes ([PR #41652](https://github.com/NousResearch/hermes-agent/pull/41652)), systemd exit codes ([PR #41642](https://github.com/NousResearch/hermes-agent/pull/41642)), and Windows socket exhaustion ([PR #41638](https://github.com/NousResearch/hermes-agent/pull/41638)). 

## 4. Community Hot Topics
*   **A2A Protocol Demand:** The most active issue is [Issue #514 (A2A Protocol Support)](https://github.com/NousResearch/hermes-agent/issues/514) with 19 comments and 18 👍. The community is heavily pushing for Google's Agent-to-Agent protocol to enable inter-agent communication, signaling a strong user need for multi-agent orchestration rather than isolated assistants.
*   **Desktop as a Thin Client:** [Issue #38602 (Desktop Client-Only Installation)](https://github.com/NousResearch/hermes-agent/issues/38602) garnered 8 👍. Users want the Desktop app decoupled from the local runtime to act as a pure client connecting to remote Hermes instances, reflecting a mature deployment use-case.
*   **WeChat Enterprise Limitations:** [Issue #29144 (Weixin multi-account)](https://github.com/NousResearch/hermes-agent/issues/29144) highlights a hard blocker for enterprise teams in China, where the gateway currently overwrites single WeChat bindings, unlike Telegram/Discord. 

## 5. Bugs & Stability
Several high-severity bugs were reported today, alongside multiple P2 gateway and tool bugs:
*   **P1 - Config Migration Silently Kills Tools:** [Issue #38798](https://github.com/NousResearch/hermes-agent/issues/38798) reports that migrating from config v25 to v26 rewrites `hermes-cli` to `hermes`, silently breaking all tools. *No fix PR yet.*
*   **P1 - State.db Unavailability:** [Issue #41386](https://github.com/NousResearch/hermes-agent/issues/41386) notes that if SQLite is unavailable, the CLI runs live-only, silently failing to save transcripts. *No fix PR yet.*
*   **P1 - Scanner Crash Hangs Agent:** [Issue #41400](https://github.com/NousResearch/hermes-agent/issues/41400) reports a Tirith security scanner SIGSEGV causing a 20-minute agent hang due to retry loops. *No fix PR yet.*
*   **P2 - Systemctl Exit Code:** Gateway exits with code 1 on `systemctl stop` ([Issue #41631](https://github.com/NousResearch/hermes-agent/issues/41631)). **Fix PR:** [PR #41642](https://github.com/NousResearch/hermes-agent/pull/41642).
*   **P2 - Session Restore Crash:** Unexpected terminal closes cause `MarkupError` crashes on resume ([Issue #41645](https://github.com/NousResearch/hermes-agent/issues/41645)). **Fix PR:** [PR #41652](https://github.com/NousResearch/hermes-agent/pull/41652).
*   **P2 - Windows Socket Exhaustion:** Tool bridge creates excessive `TIME_WAIT` connections ([Issue #41612](https://github.com/NousResearch/hermes-agent/issues/41612)). **Fix PR:** [PR #41638](https://github.com/NousResearch/hermes-agent/pull/41638).
*   **P2 - Vision/Image Handling:** Two distinct bugs reported: Desktop image paste sends file path instead of data ([Issue #41556](https://github.com/NousResearch/hermes-agent/issues/41556)), and MCP tools are discovered but not exposed in TUI ([Issue #41625](https://github.com/NousResearch/hermes-agent/issues/41625)).

## 6. Feature Requests & Roadmap Signals
*   **Multi-Agent & Fleet Management:** Alongside the highly-upvoted A2A protocol request, [PR #29460 (hermes mesh — fleet provisioner CLI v0.1)](https://github.com/NousResearch/hermes-agent/pull/29460) is actively being developed. This strongly signals that multi-agent orchestration and provisioning is the next major architectural frontier for Hermes.
*   **Unified LLM Routing:** [Issue #41190](https://github.com/NousResearch/hermes-agent/issues/41190) requests a unified plugin route selector for provider/model overrides, pointing to current fragmentation in how LLM calls are routed across main, delegate, and auxiliary tasks.
*   **Gateway Configurability:** [Issue #41519](https://github.com/NousResearch/hermes-agent/issues/41519) asks for configurable accepted filetypes in the gateway, moving away from hardcoded limits.
*   **Prediction:** The next version will likely focus heavily on Windows/Gateway reliability fixes and config migration safety, given the current P1 bugs. The `hermes mesh` CLI might also land experimentally.

## 7. User Feedback Summary
**Pain Points:** Windows users are experiencing significant friction, including DNS resolution failures for Weixin ([Issue #41597](https://github.com/NousResearch/hermes-agent/issues/41597)), git-bash path normalization issues ([PR #41643](https://github.com/NousResearch/hermes-agent/pull/41643)), and port exhaustion. Desktop users are frustrated by UI flickering on Wayland ([Issue #38015](https://github.com/NousResearch/hermes-agent/issues/38015)) and profile-selection failures ([Issue #41517](https://github.com/NousResearch/hermes-agent/issues/41517)). Additionally, silent failures (like config migration breaking tools and state.db drops) are eroding trust, as users believe the system is working when it isn't.
**Use Cases:** Users are actively trying to deploy Hermes in enterprise team settings (WeChat multi-account), connect Desktop clients to remote cloud backends, and integrate tightly via MCP/A2A protocols for agentic workflows.

## 8. Backlog Watch
*   [Issue #514 (A2A Protocol)](https://github.com/NousResearch/hermes-agent/issues/514): Open since March, this is the highest-engagement feature request. It needs a definitive maintainer decision or roadmap commitment.
*   [Issue #29144 (Weixin Multi-Account)](https://github.com/NousResearch/hermes-agent/issues/29144): A hard blocker for enterprise adoption in a major market, requiring architectural changes to the gateway adapter.
*   [Issue #38602 (Desktop Thin-Client)](https://github.com/NousResearch/hermes-agent/issues/38602): Highly requested (8 👍), requiring a refactor of the Electron bootstrapping logic.
*   [Issue #38798 (Config Migration v25->v26)](https://github.com/NousResearch/hermes-agent/issues/38798): A P1 data-loss/silent failure bug that has been open for 4 days without a linked fix PR; requires immediate attention.

</details>

<details>
<summary><strong>PicoClaw</strong> — <a href="https://github.com/sipeed/picoclaw">sipeed/picoclaw</a></summary>

# PicoClaw Project Digest — 2026-06-08

## 1. Today's Overview
PicoClaw is experiencing a high-velocity stabilization phase, evidenced by 17 closed issues and 12 merged pull requests in the last 24 hours. The maintainer team is heavily focused on defensive programming and hardening the codebase, specifically targeting unchecked type assertions, ignored I/O errors, and edge cases in identity parsing. A new nightly build (v0.2.9-nightly) was cut, incorporating these reliability fixes. While core agent loop and bus stability improvements are progressing, the community remains vocal about channel-specific bugs (Telegram, Matrix) and the need for more flexible provider configurations.

## 2. Releases
- **nightly: v0.2.9-nightly.20260607.7d2b0c2a**
  - **Type:** Automated Nightly Build (Use with caution)
  - **Changes:** Includes recent merges up to the `main` branch. Key additions in this nightly cycle encompass Anthropic default model ID fixes, native Kagi search integration, and extensive error-handling hardening for file I/O and type assertions.
  - **Full Changelog**: https://github.com/sipeed/picoclaw/compare/v0.2.9...main

## 3. Project Progress
Significant progress was made in code quality and integration stability:
- **Defensive Coding & Hardening:** A series of PRs by `chengzhichao-xydt` were merged to eliminate silent failures across the codebase. This includes checking `ok` for type assertions in agent startup ([PR #3046](https://github.com/sipeed/picoclaw/pull/3046)) and model probes ([PR #3040](https://github.com/sipeed/picoclaw/pull/3040)), and explicitly checking `Close()` errors after file writes to prevent silent data truncation in media downloads ([PR #3033](https://github.com/sipeed/picoclaw/pull/3033), [PR #3035](https://github.com/sipeed/picoclaw/pull/3035)) and Feishu resources ([PR #3034](https://github.com/sipeed/picoclaw/pull/3034)).
- **Provider Fixes:** The default Anthropic model ID was corrected from `claude-sonnet-4.6` to the API-compliant `claude-sonnet-4-6` ([PR #3036](https://github.com/sipeed/picoclaw/pull/3036), closes [Issue #2941](https://github.com/sipeed/picoclaw/issues/2941)).
- **New Integrations:** Native Kagi web search provider was added ([PR #3037](https://github.com/sipeed/picoclaw/pull/3037)).
- **Infrastructure:** A cluster of issues related to Binance exchange connectors, risk management, and ClawHub CLI (EX-001 to EXM-003, RG-001) were closed out, indicating a completed internal milestone for trading/data-exchange features.

## 4. Community Hot Topics
- **Codex OAuth Empty Responses** ([Issue #2674](https://github.com/sipeed/picoclaw/issues/2674)): 8 comments, 4 thumbs up. Users are trying to connect PicoClaw to the ChatGPT backend via Codex OAuth but hitting empty streaming responses. This highlights a strong user desire to use first-party ChatGPT endpoints seamlessly.
- **Android/Termux Support** ([Issue #286](https://github.com/sipeed/picoclaw/issues/286), [PR #2902](https://github.com/sipeed/picoclaw/pull/2902)): 8 comments, 2 thumbs up. The community shows significant interest in running PicoClaw on ARM64 Android devices, leading to the merging of a dedicated Termux guide.
- **Release Cadence & Stability Frustrations** ([Issue #2952](https://github.com/sipeed/picoclaw/issues/2952)): Users are expressing frustration over the gap between stable releases, coupled with bugs in `actions:run` defaults and QQ channel restart loops. This points to a need for more frequent stable patches or better default configurations for out-of-the-box models.

## 5. Bugs & Stability
Ranked by severity:
1. **Matrix Access Control Failure** ([Issue #3044](https://github.com/sipeed/picoclaw/issues/3044)): `allow_from` silently rejects standard Matrix IDs containing colons (`@localpart:domain`). *Fix exists: [PR #3045](https://github.com/sipeed/picoclaw/pull/3045).*
2. **MCP CLI Flag Parsing Breakage** ([Issue #3041](https://github.com/sipeed/picoclaw/issues/3041)): `mcp add` mis-parses global flags (like `--no-color`) into positional arguments, breaking HTTP/SSE server additions. *Fix exists: [PR #3048](https://github.com/sipeed/picoclaw/pull/3048).*
3. **Agent Loop Reload Goroutine Leaks** ([PR #2904](https://github.com/sipeed/picoclaw/pull/2904)): Reload paths create detached goroutines and panic risks. *Fix exists and open for review.*
4. **Telegram Location Ignored** ([Issue #3049](https://github.com/sipeed/picoclaw/issues/3049)): Bot completely ignores location pins, only processing `message.text`. *No fix PR yet.*
5. **Anthropic API 404s on Default Config** ([Issue #2941](https://github.com/sipeed/picoclaw/issues/2941)): Fresh installs defaulting to an invalid model ID. *Fixed in [PR #3036](https://github.com/sipeed/picoclaw/pull/3036).*

## 6. Feature Requests & Roadmap Signals
- **Omniroute Provider Support** ([Issue #2978](https://github.com/sipeed/picoclaw/issues/2978)): Users want to integrate combo routers like Omniroute, signaling a need for easier multi-model routing configurations.
- **Telegram Reply-as-Mention** ([PR #2975](https://github.com/sipeed/picoclaw/pull/2975)): Allowing replies to bot messages to act as mentions in group chats, aligning PicoClaw's behavior with standard Telegram bot UX.
- **Skill Dependency Management** ([PR #2936](https://github.com/sipeed/picoclaw/pull/2936)): Skipping skills whose required binaries are missing from the PATH, preventing LLM hallucinations about unavailable tools.
- **Prediction for v0.2.9 Stable:** The stable release will likely focus heavily on reliability, featuring the Matrix/MCP CLI fixes, the Anthropic model ID correction, and the broader type-safety/I/O error handling patches.

## 7. User Feedback Summary
- **Pain Points:** Users struggle with upgrading between versions ([Issue #2834](https://github.com/sipeed/picoclaw/issues/2834)) and experience channel-specific instability, particularly QQ channel context loops ([Issue #2952](https://github.com/sipeed/picoclaw/issues/2952)). The default model configurations also cause friction when they don't align perfectly with provider APIs.
- **Use Cases:** Edge deployment is highly popular, with users running PicoClaw on Raspberry Pi and Android/Termux environments. There is also active usage of diverse channel integrations (Matrix, Telegram, QQ, Feishu).
- **Satisfaction:** Generally positive regarding the breadth of integrations, but tempered by frustration with silent failures and streaming parsing edge cases in providers.

## 8. Backlog Watch
- **[PR #2904](https://github.com/sipeed/picoclaw/pull/2904)**: Open since May 20, addresses critical agent loop reload and panic cleanup. Needs maintainer review to prevent runtime instability in production.
- **[PR #2975](https://github.com/sipeed/picoclaw/pull/2975)**: Telegram reply-as-mention feature open since May 30. A highly requested UX improvement that is currently stalled.
- **[Issue #2978](https://github.com/sipeed/picoclaw/issues/2978)**: Omniroute provider request, stale since May 31. Maintainers should provide guidance on custom provider configurations or confirm roadmap inclusion.

</details>

<details>
<summary><strong>NanoClaw</strong> — <a href="https://github.com/qwibitai/nanoclaw">qwibitai/nanoclaw</a></summary>

### NanoClaw Project Digest — 2026-06-08

**1. Today's Overview**
NanoClaw experienced moderate activity over the past 24 hours, with 9 pull requests updated and 3 active issues, but no new releases published. The development focus is currently split between tightening container security/configurations and fixing onboarding friction. Three PRs were merged/closed, indicating steady progress on account rotation, upgrade safety, and documentation. However, the lack of closed issues alongside newly opened security and setup bugs suggests the project is in an iterative debugging phase rather than a stable consolidation phase.

**2. Releases**
No new releases were published today.

**3. Project Progress**
Three pull requests were closed/merged today, advancing stability and operational safety:
*   **[PR #2707](https://github.com/nanocoai/nanoclaw/pull/2707) (Closed):** Introduced a startup tripwire and upgrade marker. This prevents silent breakages when users perform a raw `git pull` instead of using sanctioned migration paths, failing loudly with a self-healing message.
*   **[PR #2706](https://github.com/nanocoai/nanoclaw/pull/2706) (Closed):** Fixed account rotation logic (limiting Codex/Gemini modes from entering Anthropic auto-rotation), calibrated DB cursor drift before switching, and improved `killGroup` process termination with a SIGTERM to SIGKILL fallback.
*   **[PR #2710](https://github.com/nanocoai/nanoclaw/pull/2710) (Closed):** Added documentation for Ollama prompt caching, explaining how to filter the cache-busting hash to improve performance.

**4. Community Hot Topics**
The most engaged issue today highlights persistent configuration management problems:
*   **[Issue #2312](https://github.com/nanocoai/nanoclaw/issues/2312) (2 comments):** The `groups/global/CLAUDE.md` file is unconditionally deleted on startup, causing a permanent dirty working tree. This has been an open annoyance since May, and the ongoing discussion reflects a need for the core team to address how global configuration files are managed versus locally migrated ones.
*   **Older PRs seeing renewed activity:** [PR #2531](https://github.com/nanocoai/nanoclaw/pull/2531) (fixing duplicate text when `send_message` fires mid-turn) and [PR #1626](https://github.com/nanocoai/nanoclaw/pull/1626) (Telegram topic isolation) were both updated today, signaling that the community remains highly invested in messaging stability and multi-platform integrations.

**5. Bugs & Stability**
Several significant bugs were reported today, focusing on security gaps and broken onboarding:
1.  **High Severity - Security:** **[Issue #2711](https://github.com/nanocoai/nanoclaw/issues/2711)** - The `create_agent` MCP tool is ungated. Despite being documented as "admin-only," any container can create agent groups, posing a serious container escape/privilege escalation risk. No fix PR is currently attached.
2.  **Medium Severity - Onboarding:** **[Issue #2703](https://github.com/nanocoai/nanoclaw/issues/2703)** - Following the recommended setup path leaves `cli/local` unwired, causing `pnpm run chat hi` to hang for 120 seconds and timeout. This severely impacts first-time user experience. No fix PR is currently attached.
3.  **Medium Severity - Credential Proxy:** **[PR #2705](https://github.com/nanocoai/nanoclaw/pull/2705)** - The `use-native-credential-proxy` silently falls back to the OneCLI gateway on real installs because it only reads `process.env`. A fix PR is currently open.
4.  **Low Severity - Orphaned Processes:** **[PR #2708](https://github.com/nanocoai/nanoclaw/pull/2708)** - Agent containers are not reaped on service stop, leading to orphaned processes. A fix PR is open.

**6. Feature Requests & Roadmap Signals**
*   **Container Configuration Enhancements:** **[PR #2709](https://github.com/nanocoai/nanoclaw/pull/2709)** implements a maintainer-requested feature (#1867) to add DB-backed `env` and `blocked_hosts` JSON columns to `container_configs`. This signals an upcoming shift toward more robust, centralized container management and network security policies.
*   **CLI Testing:** **[PR #2704](https://github.com/nanocoai/nanoclaw/pull/2704)** introduces unit tests for `cli-agent parseArgs`, reflecting a community push for better test coverage in the CLI setup flows, likely prompted by the onboarding bugs seen today.

**7. User Feedback Summary**
Users are experiencing friction during initial setup and container management. The broken "recommended path" for CLI chat ([Issue #2703](https://github.com/nanocoai/nanoclaw/issues/2703)) is a major source of dissatisfaction for new users, while the ungated agent creation ([Issue #2711](https://github.com/nanocoai/nanoclaw/issues/2711)) erodes trust for production deployments. Conversely, users running multi-model setups (Anthropic, Codex, Gemini) are highly engaged, as seen in the account rotation fixes, indicating strong enterprise/power-user adoption. The silent fallback behavior in credential proxying ([PR #2705](https://github.com/nanocoai/nanoclaw/pull/2705)) also shows that users expect transparent error handling over silent degradation.

**8. Backlog Watch**
*   **[Issue #2312](https://github.com/nanocoai/nanoclaw/issues/2312):** Open since May 6th, this dirty working tree bug causes continuous annoyance for developers but lacks a targeted fix PR. It needs maintainer triage.
*   **[PR #1626](https://github.com/nanocoai/nanoclaw/pull/1626):** Open since April 4th, the Telegram topic isolation feature PR is still awaiting review/merge. The prolonged open state suggests a bottleneck in reviewing integration features.
*   **[PR #2531](https://github.com/nanocoai/nanoclaw/pull/2531):** Open since May 18th, this poll-loop duplicate text fix is crucial for chat stability but remains unmerged, requiring maintainer attention to verify and merge.

</details>

<details>
<summary><strong>NullClaw</strong> — <a href="https://github.com/nullclaw/nullclaw">nullclaw/nullclaw</a></summary>

No activity in the last 24 hours.

</details>

<details>
<summary><strong>IronClaw</strong> — <a href="https://github.com/nearai/ironclaw">nearai/ironclaw</a></summary>

# IronClaw Project Digest — 2026-06-08

## 1. Today's Overview
IronClaw is currently experiencing high development velocity, almost entirely concentrated on the "Reborn" architectural overhaul and the WebUI v2 beta. In the last 24 hours, 50 issues and 38 pull requests saw updates, with a solid 16 PRs merged/closed, indicating strong execution alongside active planning (42 open issues). The project is deeply entrenched in wiring up the Reborn host kernel, enforcing strict security safeguards for multi-tenant production, and expanding channel integrations (Slack, WebChat). No new stable release was cut today, though a major version bump remains pending.

## 2. Releases
No new releases were published in the last 24 hours. However, release PR [#3708](https://github.com/nearai/ironclaw/pull/3708) (open since mid-May) remains active, tracking a significant version bump (`ironclaw` 0.24.0 -> 0.29.1) that includes API-breaking changes in `ironclaw_common` and `ironclaw_skills`.

## 3. Project Progress
Significant forward momentum occurred on the WebChat v2 surface and Reborn infrastructure facades:
*   **WebChat v2 & Channel Integrations:** Thread deletion was added ([#4516](https://github.com/nearai/ironclaw/pull/4516)), Slack host-beta durable stores were wired up ([#4463](https://github.com/nearai/ironclaw/pull/4463)), and a Slack allowed-channel picker was implemented ([#4532](https://github.com/nearai/ironclaw/pull/4532)).
*   **Reborn Loop & Tooling:** Structured model-visible tool observations were merged ([#4530](https://github.com/nearai/ironclaw/pull/4530)), and outbound preference facade contracts were added ([#4511](https://github.com/nearai/ironclaw/pull/4511)).
*   **Core Infrastructure:** Active PRs pushed forward user-scoped skills settings UI ([#4527](https://github.com/nearai/ironclaw/pull/4527)), preservation of active tasks during compaction ([#4534](https://github.com/nearai/ironclaw/pull/4534)), and a critical fix for configured extension credential staging ([#4492](https://github.com/nearai/ironclaw/pull/4492)).

## 4. Community Hot Topics
*   **Reborn Product Workflow Facade:** The most active issue is [#3280](https://github.com/nearai/ironclaw/issues/3280) (7 comments), discussing the addition of `ProductWorkflow` and `InboundTurnService`. This highlights the community's focus on cleanly separating product adapters from host-layer services.
*   **Configuration Complexity:** EPIC [#3036](https://github.com/nearai/ironclaw/issues/3036) (5 comments, 1 👍) is generating significant interest regarding "Configuration-as-Code." Users and operators are clearly struggling with the current mix of `.env`, JSON, and manual runtime flags, expressing a strong need for declarative, schema-validated tenant blueprints.
*   **Security & Hook Hardening:** A cluster of issues by contributor @zmanian around security boundaries (e.g., `SecurityAuditSink` adoption [#3959](https://github.com/nearai/ironclaw/issues/3959), third-party activation hardening [#3957](https://github.com/nearai/ironclaw/issues/3957)) shows an active, ongoing discourse on how to safely isolate third-party extensions in a multi-tenant environment.

## 5. Bugs & Stability
*   **Production Readiness Seams:** Issue [#3333](https://github.com/nearai/ironclaw/issues/3333) (P0) reveals that the `reborn-integration` branch currently contains several fake/in-memory/no-op seams that need production implementations before cutover.
*   **Credential Staging Bug:** PR [#4492](https://github.com/nearai/ironclaw/pull/4492) addresses a bug where configured extension credentials weren't properly staged for first capability calls, requiring local-dev defaults to fall back to the `SecretStore`.
*   **Filesystem Isolation Risk:** Issue [#3956](https://github.com/nearai/ironclaw/issues/3956) notes a stability/security concern where current `openat2` TOCTOU hardening fails to block mount-point traversal across device boundaries, posing a potential sandbox escape risk for tenant environments.

## 6. Feature Requests & Roadmap Signals
*   **WebUI Beta Tracker:** Issue [#3607](https://github.com/nearai/ironclaw/issues/3607) acts as the owner-module tracker for the Reborn WebUI Beta, confirming the immediate roadmap is heavily UI-centric.
*   **SSO Parity:** Carrying v1 Google/GitHub/NEAR SSO into WebChat v2 ([#4116](https://github.com/nearai/ironclaw/issues/4116)) is highly requested, signaling that identity federation is a prerequisite for the v2 beta rollout.
*   **WASM Product Adapters:** Structuring ProductAdapters as WASM components ([#3572](https://github.com/nearai/ironclaw/issues/3572)) points toward a future roadmap of deeply sandboxed, isolated channel integrations.
*   *Prediction:* The upcoming release will likely finalize the Reborn config.toml seeding ([#4517](https://github.com/nearai/ironclaw/pull/4517)) and the user-scoped skills UI ([#4527](https://github.com/nearai/ironclaw/pull/4527)), making local developer setup significantly easier.

## 7. User Feedback Summary
*   **Pain Point - Local Setup Friction:** Users and developers find running IronClaw locally as a coding agent overly complex. Issue [#3044](https://github.com/nearai/ironclaw/issues/3044) and PR [#4492](https://github.com/nearai/ironclaw/pull/4492) highlight the pain of manually wiring grants, mounts, and auth policies just to get a local shell running.
*   **Pain Point - Config Drift:** The manual, fragmented configuration system is causing operational headaches; users want audit trails and diffs, as expressed in [#3036](https://github.com/nearai/ironclaw/issues/3036).
*   **Use Case - Channel Control:** The addition of the Slack allowed-channel picker ([#4532](https://github.com/nearai/ironclaw/pull/4532)) and outbound preferences ([#4511](https://github.com/nearai/ironclaw/pull/4511)) aligns with operator needs for granular, admin-managed routing of agent communications across disparate workspaces.

## 8. Backlog Watch
*   **Stale Release PR:** PR [#3708](https://github.com/nearai/ironclaw/pull/3708) has been open since May 16th and includes breaking API changes. It needs maintainer attention to finalize the `0.29.1` release.
*   **P0 Cutover Blockers:** Issues [#3032](https://github.com/nearai/ironclaw/issues/3032) (no-exposure safeguards) and [#3026](https://github.com/nearai/ironclaw/issues/3026) (config-driven composition root) are critical Reborn production blockers that have seen low recent engagement relative to their P0 status.
*   **Dependency Bottleneck:** Dependabot PRs for major ecosystem bumps ([#4503](https://github.com/nearai/ironclaw/pull/4503), [#4002](https://github.com/nearai/ironclaw/pull/4002)) are piling up and require review to prevent downstream integration conflicts.

</details>

<details>
<summary><strong>LobsterAI</strong> — <a href="https://github.com/netease-youdao/LobsterAI">netease-youdao/LobsterAI</a></summary>

## LobsterAI Project Digest (2026-06-08)

### 1. Today's Overview
LobsterAI is currently experiencing a period of low development activity, with zero pull requests merged, closed, or opened in the last 24 hours, and no new releases. The issue tracker saw 15 updates, but all remain open, with 14 of them tagged as `[stale]` from early April, indicating a significant backlog. A newly created issue today regarding token waste highlights ongoing user concerns about core agent execution behavior. Overall, project momentum appears stagnant, with community-reported bugs and feature requests accumulating without recent maintainer engagement.

### 2. Releases
No new releases were recorded today. 

### 3. Project Progress
There is no project progress to report for today. Zero PRs were merged or closed, and zero issues were resolved. The codebase remains unchanged in the last 24 hours.

### 4. Community Hot Topics
The most active recent discussion is a new issue reported today regarding runtime efficiency, while older stale issues continue to reflect underlying needs for better state management and UI feedback.
*   **[#2121 Repeated output consuming tokens](https://github.com/netease-youdao/LobsterAI/issues/2121)**: A user reported that the agent outputs repetitive text, raising concerns about unnecessary token consumption and cost. This highlights a critical need for better generation halting mechanisms or loop detection.
*   **[#1509 Skills generation blocking and lack of intermediate state](https://github.com/netease-youdao/LobsterAI/issues/1509)**: With 2 comments, this older issue emphasizes a strong user need for transparency during agent execution. Users are frustrated when the AI blocks during file generation without showing its thinking process, leaving them unable to take further action.

### 5. Bugs & Stability
Several bugs are present in the tracker, primarily related to state synchronization and silent failures. No fix PRs are currently available. Ranked by severity:
*   **High Severity**:
    *   [#2121 Repeated output consuming tokens](https://github.com/netease-youdao/LobsterAI/issues/2121): Directly impacts user costs and resource efficiency.
    *   [#1509 Skills generation blocking](https://github.com/netease-youdao/LobsterAI/issues/1509): Renders the skill creation process unresponsive without error feedback.
    *   [#1516 GitHub Copilot OAuth Token silently lost](https://github.com/netease-youdao/LobsterAI/issues/1516): Closing the Settings panel during polling causes authentication success to be lost, breaking the Copilot integration.
*   **Medium Severity**:
    *   [#1500 Disabled skills still active in `activeSkillIds`](https://github.com/netease-youdao/LobsterAI/issues/1500): Redux state fails to sync, causing disabled skills to persist in prompts.
    *   [#1502 Agent settings skill list fails to sync to current session](https://github.com/netease-youdao/LobsterAI/issues/1502): Requires an Agent switch to force a UI refresh.
    *   [#1506 Scheduled task IM notifications fail silently](https://github.com/netease-youdao/LobsterAI/issues/1506): Empty `delivery.to` allows form submission but results in no notification delivery.
*   **Low Severity**:
    *   [#1504 Missing required validation for Popo AES Key](https://github.com/netease-youdao/LobsterAI/issues/1504): Form validation oversight.
    *   [#1512 QQ Bot missing UI input for group whitelist](https://github.com/netease-youdao/LobsterAI/issues/1512): Makes whitelist feature unusable via UI.
    *   [#1513 Inconsistent Terms of Service formatting](https://github.com/netease-youdao/LobsterAI/issues/1513): UI text display issue.
    *   [#1518 CI Labeler permission error](https://github.com/netease-youdao/LobsterAI/issues/1518): DevOps/Infrastructure issue, not affecting end-user stability.

### 6. Feature Requests & Roadmap Signals
Users are heavily requesting features related to session organization and data management, signaling that LobsterAI's user base is maturing and dealing with information overload:
*   **Session Organization**: [#1541 Tags and filtering for sessions](https://github.com/netease-youdao/LobsterAI/issues/1541) and [#1525 Color coding for sessions](https://github.com/netease-youdao/LobsterAI/issues/1525) indicate users need multi-dimensional ways to organize growing conversation histories.
*   **Data Portability & Management**: [#1528 Batch export of sessions](https://github.com/netease-youdao/LobsterAI/issues/1528) and [#1532 Local usage statistics dashboard](https://github.com/netease-youdao/LobsterAI/issues/1532) suggest users want better local backup, migration, and analytics capabilities.
*   **In-Conversation Productivity**: [#1537 Message bookmarking/pinning](https://github.com/netease-youdao/LobsterAI/issues/1537) reflects the need to quickly retrieve critical AI answers in long-context chats.
*   **Prediction**: If the project resumes active development, the next version will likely target session management (tags/color/export) to significantly improve the power-user experience.

### 7. User Feedback Summary
*   **Pain Points**: Users are frustrated by "silent failures" (IM notifications, Copilot auth, skill generation) where the UI provides no error or loading states. State synchronization between UI components (e.g., Redux store vs. current session) is another major friction point. Finally, token waste due to generation loops is a critical cost concern.
*   **Use Cases**: Users are integrating LobsterAI with enterprise IM platforms (DingTalk, Feishu, QQ, Popo) for automated notifications. They are also using it for long, complex tasks (like skill creation) where intermediate thought processes are crucial for trust and debuggability.
*   **Satisfaction**: User sentiment appears low due to the accumulation of stale issues and lack of recent maintainer response or fixes.

### 8. Backlog Watch
The backlog is critical, with 14 issues marked `[stale]` since 2026-04-07—over two months ago. The following functional bugs require immediate maintainer attention to restore basic reliability:
*   [#1500 Disabled skills still injected into prompts](https://github.com/netease-youdao/LobsterAI/issues/1500)
*   [#1502 Agent skill list not syncing to active session](https://github.com/netease-youdao/LobsterAI/issues/1502)
*   [#1509 Skill generation blocking without UI feedback](https://github.com/netease-youdao/LobsterAI/issues/1509)
*   [#1516 Copilot OAuth token lost on settings close](https://github.com/netease-youdao/LobsterAI/issues/1516)

</details>

<details>
<summary><strong>TinyClaw</strong> — <a href="https://github.com/TinyAGI/tinyagi">TinyAGI/tinyagi</a></summary>

No activity in the last 24 hours.

</details>

<details>
<summary><strong>Moltis</strong> — <a href="https://github.com/moltis-org/moltis">moltis-org/moltis</a></summary>

# Moltis Project Digest — 2026-06-08

## 1. Today's Overview
Moltis exhibits moderate ongoing development activity focused on platform integrations, context management, and administrative controls, though no items were merged or closed today. Three open pull requests indicate active work by contributors to refine Telegram streaming behavior, optimize LLM context rehydration, and enhance channel logging privacy. No new releases were cut, suggesting the project is currently in an accumulation and stabilization phase prior to a future release. Overall project health appears steady, driven by consistent internal improvements rather than external feature launches.

## 2. Releases
No new releases were recorded today.

## 3. Project Progress
No pull requests were merged or closed in the last 24 hours. However, active development continues across three significant open PRs:
*   [PR #1113](https://github.com/moltis-org/moltis/pull/1113): Refines the Telegram integration by ensuring final replies stream correctly when completion notifications are disabled.
*   [PR #1089](https://github.com/moltis-org/moltis/pull/1089): Advances context management by capping persisted tool results before session rehydration, applying comprehensively across chat, streaming, retries, and compaction.
*   [PR #1093](https://github.com/moltis-org/moltis/pull/1093): Introduces granular visibility settings (`all`, `errors_only`, `off`) for channel activity logs, establishing a hierarchy of user, channel, and account-level overrides.

## 4. Community Hot Topics
The most active community item is [Issue #1107](https://github.com/moltis-org/moltis/issues/1107) (1 comment), which requests multiline text input for the mobile web UI. The underlying need highlights a significant friction point for power users: interacting with AI agents often requires structuring complex, multi-step prompts, which is cumbersome when restricted to a single-line input on mobile devices. While the open PRs have no recent comments, their scope (especially the context capping in [PR #1089](https://github.com/moltis-org/moltis/pull/1089)) touches on the critical AI-assistant challenge of managing token limits, which is a perennial topic for advanced users.

## 5. Bugs & Stability
*   **Medium Severity - Telegram Streaming Regression:** [PR #1113](https://github.com/moltis-org/moltis/pull/1113) identifies a bug introduced after PR #1099 where Telegram edit-in-place streaming fails to output the final answer if completion notifications are disabled. A hotfix PR is currently open and awaiting merge.

No crashes, data loss, or other high-severity regressions were reported today.

## 6. Feature Requests & Roadmap Signals
*   **Mobile UX Enhancement:** [Issue #1107](https://github.com/moltis-org/moltis/issues/1107) requests multiline input for the mobile web UI, signaling that mobile accessibility and prompt-structuring ergonomics are desired by the user base.
*   **Administrative Control & Privacy:** [PR #1093](https://github.com/moltis-org/moltis/pull/1093) points toward a roadmap signal for better multi-user and enterprise-grade controls, allowing admins to quiet channel noise by toggling activity log visibility.
*   *Prediction:* The next version will likely focus on backend stability and administrative controls. The multiline mobile input feature is a strong candidate for inclusion given its direct impact on core usability.

## 7. User Feedback Summary
Real user pain points center around mobile usability. The lack of a native multiline input on the mobile web UI forces users into awkward workarounds to format prompts correctly, detracting from the conversational fluidity expected of a modern personal AI assistant. Satisfaction appears high regarding the project's multi-platform presence (e.g., Telegram integration), but users expect platform-specific UI norms to be supported. 

## 8. Backlog Watch
Two substantial PRs have been open for several days without reviewer engagement:
*   [PR #1089](https://github.com/moltis-org/moltis/pull/1089) (Open since June 1) addresses core context handling and token efficiency by capping persisted tool results.
*   [PR #1093](https://github.com/moltis-org/moltis/pull/1093) (Open since June 3) introduces extensive visibility settings with complex override hierarchies.

Both involve deep architectural or logic changes and require maintainer attention to prevent stalling. Additionally, [Issue #1107](https://github.com/moltis-org/moltis/issues/1107) remains open and awaiting triage since June 5.

</details>

<details>
<summary><strong>CoPaw</strong> — <a href="https://github.com/agentscope-ai/CoPaw">agentscope-ai/CoPaw</a></summary>

### 1. Today's Overview
The CoPaw (QwenPaw) project experienced moderate community activity on 2026-06-08, with 5 new or updated issues and 2 active pull requests, but no merged code or closed issues. The lack of closures suggests maintainers may be backlog grooming or focusing on ongoing development branches. Current community focus is heavily skewed toward multimodal flexibility, local model compatibility, and memory system architecture. Overall project health remains active, though the absence of fixes for recently reported regressions warrants attention.

### 3. Project Progress
No PRs were merged and no issues were closed today. However, active development is evident:
*   **PR #4995** (Open): A first-time contributor submitted a fix to preserve tool output attachments and text in the channel renderer when `show_tool_details` is disabled, addressing metadata loss.
*   **PR #4949** (Under Review): A significant feature PR extending the QwenPaw ACP (Agent Client Protocol) server is currently under review. It aims to support command advertising, error surfacing, tool parameters, and agent/model metadata, indicating a push toward richer third-party client integrations.

### 4. Community Hot Topics
The most active discussions center on model flexibility and local deployment reliability:
*   **[Issue #4992](https://github.com/agentscope-ai/QwenPaw/issues/4992)** (2 comments): Request for a decoupled "Visual Model Fallback." Users want to pair pure-text LLMs (like deepseek-v4-flash) with a separate vision model for image processing, rather than being forced to use a natively multimodal main model. This highlights a strong user need for modular, cost-effective multimodal architectures.
*   **[Issue #4989](https://github.com/agentscope-ai/QwenPaw/issues/4992)** (2 comments): A regression bug affecting local vLLM deployments. Users are actively discussing workflow disruptions caused by the recent 1.1.9/1.1.10 updates breaking previously functional local model integrations.

### 5. Bugs & Stability
Two distinct bugs were reported, with one being a critical regression:
1.  **High/Critical - Local Model Regression**: **[Issue #4989](https://github.com/agentscope-ai/QwenPaw/issues/4989)** - In versions 1.1.9 and 1.1.10, using a locally deployed Qwen3.6-27B via vLLM causes the chat UI to hang indefinitely ("spinning dots") after submitting a prompt. Connection tests pass, but inference fails silently with no backend error logs. This is a regression from v1.1.5.post2. *No fix PR is currently listed.*
2.  **Low/Cosmetic - UI Jitter**: **[Issue #4993](https://github.com/agentscope-ai/QwenPaw/issues/4993)** - In the V1.1.10 macOS client, zoomed-in image previews experience severe shaking/dragging glitches. *No fix PR is currently listed.*

### 6. Feature Requests & Roadmap Signals
*   **Modular Vision Architecture**: **[Issue #4992](https://github.com/agentscope-ai/QwenPaw/issues/4992)** proposes a `visual_model` config field to act as a "visual relay station." This aligns with industry trends toward agentic routing and specialized model pipelines.
*   **Advanced Memory Systems**: **[Issue #4994](https://github.com/agentscope-ai/QwenPaw/issues/4994)** requests a hierarchical, self-evolving memory framework, noting the current system is too basic for advanced agent use cases.
*   **Prediction**: The visual fallback feature is highly specific and solves a clear architectural limitation; it may be prioritized for an upcoming minor release. The ACP enhancements in **[PR #4949](https://github.com/agentscope-ai/QwenPaw/pull/4949)** also signal that the next version will likely focus heavily on ecosystem extensibility and external client support.

### 7. User Feedback Summary
*   **Pain Points**: Users are frustrated by silent failures when connecting local models (vLLM), as the UI provides false positives (successful connection tests) but no actual inference or error logging. The inability to mix-and-match text and vision models without upgrading the main LLM is also a major friction point.
*   **Use Cases**: Running local open-weight models (Qwen3.6-27B) via standard OpenAI-compatible protocols (vLLM) and building advanced AI agents requiring sophisticated, layered memory retention.
*   **Satisfaction**: Presently mixed to negative due to the v1.1.9/1.1.10 regression that has forced users to downgrade to v1.1.5.post2 to maintain basic functionality.

### 8. Backlog Watch
*   **[Issue #4989](https://github.com/agentscope-ai/QwenPaw/issues/4989)**: The vLLM local deployment regression needs immediate maintainer triage, as it breaks core chat functionality for a segment of the user base and currently has no linked fix PR.
*   **[PR #4949](https://github.com/agentscope-ai/QwenPaw/pull/4949)**: Open since 2026-06-03 and now "Under Review," this large ACP feature PR requires a final maintainer push to merge, as it blocks downstream projects (like the QwenPaw TUI).

</details>

<details>
<summary><strong>ZeptoClaw</strong> — <a href="https://github.com/qhkm/zeptoclaw">qhkm/zeptoclaw</a></summary>

No activity in the last 24 hours.

</details>

<details>
<summary><strong>ZeroClaw</strong> — <a href="https://github.com/zeroclaw-labs/zeroclaw">zeroclaw-labs/zeroclaw</a></summary>

# ZeroClaw Project Digest — 2026-06-08

## 1. Today's Overview
ZeroClaw exhibited high development velocity today, with 50 issues and 50 pull requests updated within the last 24 hours. The project closed 17 issues and merged 11 PRs, reflecting active maintenance and feature maturation. Activity is heavily concentrated on hardening the provider layer, enhancing the "zerocode" TUI/desktop experience, and preparing for the upcoming v0.8.0 milestone. The community remains highly engaged, particularly around multi-agent architectures, token optimization, and deployment ergonomics. Overall, the project is in a highly active, pre-release stabilization phase.

## 2. Releases
No new official releases were published today. However, PR [#7364](https://github.com/zeroclaw-labs/zeroclaw/pull/7364) (`chore(release): release v0.8.0`) is currently open, indicating that the v0.8.0 release is actively being prepared and is imminent.

## 3. Project Progress
Significant architectural and UX advancements were merged today, setting the stage for v0.8.0:
*   **TUI/Zerocode Enhancements**: Merged an outbound message queue that prevents input blocking during agent turns ([#7190](https://github.com/zeroclaw-labs/zeroclaw/pull/7190)), live in-session model/provider switching ([#7209](https://github.com/zeroclaw-labs/zeroclaw/pull/7209)), and theme enhancements with color-depth fallbacks ([#7249](https://github.com/zeroclaw-labs/zeroclaw/pull/7249)).
*   **Provider & Fallback Resilience**: Merged per-alias model-provider fallback on failure ([#7178](https://github.com/zeroclaw-labs/zeroclaw/pull/7178)) and added 7 new OpenAI-compatible providers under the new schema-v3 architecture ([#7260](https://github.com/zeroclaw-labs/zeroclaw/pull/7260)).
*   **Cloud Provider Fixes**: Fixed Bedrock prompt caching for unsupported models ([#7315](https://github.com/zeroclaw-labs/zeroclaw/pull/7315)) and reset Bedrock Qwen `conversation_id` before each prompt ([#7343](https://github.com/zeroclaw-labs/zeroclaw/pull/7343)).
*   **Azure OpenAI**: Wired `reasoning_effort` into the dedicated Azure provider for o-series/GPT-5.x models ([#7350](https://github.com/zeroclaw-labs/zeroclaw/pull/7350)).
*   **Channel Routing**: Opened PR [#7361](https://github.com/zeroclaw-labs/zeroclaw/pull/7361) implementing per-turn output routing and voice delivery fixes.

## 4. Community Hot Topics
*   **Web Dashboard Deployment Issues**: Issue [#4866](https://github.com/zeroclaw-labs/zeroclaw/issues/4866) (28 comments) highlights persistent user friction with the Web UI and Tauri desktop app failing to locate the built web dashboard. Although closed, it reveals a significant onboarding pain point.
*   **Token Consumption & Skill Compilation**: Issue [#5146](https://github.com/zeroclaw-labs/zeroclaw/issues/5146) (9 comments) discusses minimizing token usage by compiling skills instead of sending full `SKILL.md` files to cloud LLMs. This resonates deeply with users facing cost and latency issues.
*   **Multi-Agent & Interoperability**: High engagement on A2A Protocol Support ([#3566](https://github.com/zeroclaw-labs/zeroclaw/issues/3566), 7 👍 / 6 comments) and Multi-Agent Routing ([#2767](https://github.com/zeroclaw-labs/zeroclaw/issues/2767), 9 👍 / 6 comments). The community strongly desires multi-agent orchestration and standard protocol compliance.
*   **Docker Ergonomics**: Request for a "full" Docker image ([#3642](https://github.com/zeroclaw-labs/zeroclaw/issues/3642), 9 comments, 3 👍) underscores the need for lower barriers to entry for non-technical users who currently struggle with feature flags.

## 5. Bugs & Stability
*   **S0 - Data Loss / Security Risk**: Issue [#4627](https://github.com/zeroclaw-labs/zeroclaw/issues/4627) reports that `file_write` silently fails in Docker, with files invisible on the host filesystem. No fix PR is currently visible in the activity log; requires urgent attention.
*   **S1 - Workflow Blocked**: 
    *   Gemini CLI OAuth failures ([#4879](https://github.com/zeroclaw-labs/zeroclaw/issues/4879)) remain open and unresolved.
    *   Channel mode `auto_compact_history` discarding tool-call context ([#4827](https://github.com/zeroclaw-labs/zeroclaw/issues/4827) - Closed).
    *   Fallback provider chain ignoring config files ([#5803](https://github.com/zeroclaw-labs/zeroclaw/issues/5803) - Closed, likely resolved by PR #7178).
*   **S2 - Degraded Behavior**:
    *   `web_fetch` blocking allowed private hosts resolving to private IPs ([#5122](https://github.com/zeroclaw-labs/zeroclaw/issues/5122) - Closed).
    *   `context_compression` not triggering in daemon mode ([#4880](https://github.com/zeroclaw-labs/zeroclaw/issues/4880) - Closed).

## 6. Feature Requests & Roadmap Signals
*   **Provider & Model Flexibility**: Per-alias model fallback ([#4647](https://github.com/zeroclaw-labs/zeroclaw/issues/4647)) and provider-scoped fallback chains are highly requested. The merged PRs #7178 and #7260 show the team is actively delivering on this for v0.8.0.
*   **Sandboxing & Security**: Configurable writable paths and network access for the bubblewrap sandbox ([#5127](https://github.com/zeroclaw-labs/zeroclaw/issues/5127)) and air-gapped execution via Unix socket ([#6293](https://github.com/zeroclaw-labs/zeroclaw/issues/6293)) signal a strong roadmap push towards secure, isolated enterprise execution.
*   **Channel Integrations**: Demand for better channel support continues, with requests for Napcat/Onebot ([#2503](https://github.com/zeroclaw-labs/zeroclaw/issues/2503)) and a dedicated `send_channel_message` tool ([#5145](https://github.com/zeroclaw-labs/zeroclaw/issues/5145)).
*   **Memory & Context**: Using tool-calling for memory consolidation structured output ([#4760](https://github.com/zeroclaw-labs/zeroclaw/issues/4760)) indicates a future shift away from prompt-constrained JSON parsing for memory operations.

## 7. User Feedback Summary
Users are expressing frustration with deployment friction, specifically around Docker volumes (the silent `file_write` failure) and web UI builds. Non-technical users find the default Docker image too restrictive and desire out-of-the-box feature parity. Conversely, advanced users are praising the direction of provider fallbacks and TUI responsiveness. Channel integration users (Feishu, QQ/Napcat) feel like second-class citizens due to missing features or default misconfigurations (e.g., Feishu defaulting to raw LLM instead of the Agent). Cost-conscious users are actively asking for architecture changes (skill compilation) to reduce token consumption.

## 8. Backlog Watch
*   **[S0] Docker `file_write` silent failure ([#4627](https://github.com/zeroclaw-labs/zeroclaw/issues/4627))**: Open since late March, rated as S0 (data loss/security risk), yet lacks a linked fix PR. Requires immediate maintainer investigation.
*   **[S1] Gemini CLI OAuth broken ([#4879](https://github.com/zeroclaw-labs/zeroclaw/issues/4879))**: Open since March, blocks Gemini users entirely, no PR visible.
*   **Full Docker Image ([#3642](https://github.com/zeroclaw-labs/zeroclaw/issues/3642))**: Marked as `status:blocked`. High community demand, needs an owner to unblock progress.
*   **LeakDetector false positives ([#4832](https://github.com/zeroclaw-labs/zeroclaw/issues/4832))**: High-entropy token redaction breaks legitimate workflows (e.g., WeChat media files), accepted but waiting for implementation.
*   **Logo redesign ([#4710](https://github.com/zeroclaw-labs/zeroclaw/issues/4710))**: Marked `needs-author-action`, stuck in limbo despite community interest.

</details>

---
*This digest is auto-generated by [agents-radar](https://github.com/yanzi6039/agents-radar).*