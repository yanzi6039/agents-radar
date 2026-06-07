# OpenClaw Ecosystem Digest 2026-06-07

> Issues: 311 | PRs: 500 | Projects covered: 13 | Generated: 2026-06-07 14:39 UTC

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

# OpenClaw Project Digest — 2026-06-07

## 1. Today's Overview
OpenClaw is experiencing a very high-velocity development cycle, with 500 pull requests and 311 issues updated in the last 24 hours alone. The maintainers merged 119 PRs and closed 138 issues, indicating aggressive iteration, particularly on the newly released `v2026.6.5-beta.2` branch. The project's current focus is clearly bifurcated between expanding communication channel integrations (with major pushes for Microsoft Teams, Telegram, and Feishu) and hardening the agent runtime's stability—specifically addressing memory management, cron job isolation, and context compaction. However, the high volume of open P1 regressions and "diamond lobster" rated bugs suggests that the rapid feature expansion is outpacing stability efforts, leading to notable friction in production deployments.

## 2. Releases
**v2026.6.5-beta.2** was released today.
*   **Key Changes:** 
    *   QQBot now strips model reasoning/thinking scaffolding (e.g., raw `<thinking>` blocks) before native delivery, preventing internal logic from leaking into channel replies ([#89913](https://github.com/openclaw/openclaw/issues/89913), [#90132](https://github.com/openclaw/openclaw/issues/90132)).
    *   MCP tool results now coerce `resource_link`, `resource`, `audio`, malformed image, and future types, improving tool-output resilience.
*   **Migration/Breaking Notes:** None explicitly listed in the release notes, but the MCP coercion changes imply that tooling relying on strictly raw/invalid MCP outputs may experience behavioral shifts.

## 3. Project Progress
Significant progress was made across channel integrations, memory handling, and CLI security:
*   **Major Feature Advancements:**
    *   **Microsoft Teams Voice/Video:** PR [#91176](https://github.com/openclaw/openclaw/pull/91176) introduces the `msteams` provider to the `voice-call` extension, supporting inbound voice/video and outbound call-backs.
    *   **Subagent Tool Allowlists:** PR [#78441](https://github.com/openclaw/openclaw/pull/78441) adds `toolsAllow` forwarding to `sessions_spawn`, enabling tighter security boundaries for embedded subagents.
    *   **Runtime Self-Context:** PR [#90101](https://github.com/openclaw/openclaw/pull/90101) implements the first config-backed `Runtime Self Context` slice for cost/scale awareness.
*   **Resolved Bugs:** Several painful channel regressions were closed today, including Mattermost slash commands returning 503 ([#68113](https://github.com/openclaw/openclaw/issues/68113)), Feishu dispatch TypeErrors ([#88234](https://github.com/openclaw/openclaw/issues/88234)), and Discord exec approval cards failing to render ([#73802](https://github.com/openclaw/openclaw/issues/73802)).

## 4. Community Hot Topics
*   **Agent Internal Monologue Leaking (27 comments):** Issue [#25592](https://github.com/openclaw/openclaw/issues/25592) remains highly active. When agents produce text between tool calls (e.g., error handling), it routes directly to messaging channels like Slack/iMessage. This highlights a fundamental architectural need for stricter boundaries between "internal processing" and "user-facing output."
*   **Cron State Contamination (12 comments):** Issue [#90991](https://github.com/openclaw/openclaw/issues/90991) reports that scheduled cron triggers contaminate global runtime state, causing system-wide overload failures. Users are demanding better isolation for automated tasks versus interactive sessions.
*   **Codex Turn-Completion Stall (14 comments):** Issue [#88312](https://github.com/openclaw/openclaw/issues/88312) highlights a regression where multi-tool agent turns fail with "Codex stopped before confirming the turn was complete." This underscores ongoing instability in managing complex, multi-step LLM interactions.
*   **agentDir Bootstrap Ignored (14 comments):** Issue [#29387](https://github.com/openclaw/openclaw/issues/29387) reveals that per-agent `SOUL.md` and `TOOLS.md` files are silently ignored if placed in `agentDir`. This is a major pain point for users managing multi-agent configurations.

## 5. Bugs & Stability
Several high-severity bugs and regressions are currently impacting users:
*   **P1 - Cron Global State Contamination:** [#90991](https://github.com/openclaw/openclaw/issues/90991). *Fix PR exists:* [#91187](https://github.com/openclaw/openclaw/pull/91187) isolates auth profile failure policies so cron runs don't pollute shared cooldowns.
*   **P1 - Write Tool Lacks Append Mode:** [#40001](https://github.com/openclaw/openclaw/issues/40001). Isolated cron sessions silently overwrite shared memory files (like `memory/YYYY-MM-DD.md`) because the write tool only truncates. *Linked PR is open.*
*   **P1 - Exec Tool Env Inheritance:** [#31583](https://github.com/openclaw/openclaw/issues/31583). A regression where `skills.entries.<skill>.env` variables are not passed to `exec` subprocesses, breaking secret injection. *Linked PR is open.*
*   **P1 - Compaction Wedge/Cost Explosion:** [#90639](https://github.com/openclaw/openclaw/issues/90639). `safeguard` compaction mode allows sessions to grow to 200K+ tokens before failing, causing massive cost spikes and unrecoverable Slack sessions.
*   **Security - Unbounded CLI Subprocesses:** [#90993](https://github.com/openclaw/openclaw/issues/90993). Leaked subprocesses accumulate. *Fix PR exists:* [#91147](https://github.com/openclaw/openclaw/pull/91147) bounds native hook relay process lifetimes.

## 6. Feature Requests & Roadmap Signals
*   **Topic-Session Families ([#90916](https://github.com/openclaw/openclaw/issues/90916)):** Request for one assistant to have multiple named context lanes with isolated transcripts but shared durable memory. Signals a shift toward persistent, context-aware enterprise assistants.
*   **Bounded Append for Memory Flush ([#90354](https://github.com/openclaw/openclaw/issues/90354)):** Requests hard guardrails for append size and validation during pre-compaction memory flushes. Indicates that the "Dreaming/Memory" subsystem is maturing and needs production-grade safety.
*   **Configurable Session Startup Message ([#45501](https://github.com/openclaw/openclaw/issues/45501)):** Users want to override the hardcoded `/new` reset prompt.
*   **Prediction:** The next stable release will likely focus heavily on cron isolation (driven by recent bug volume) and memory subsystem hardening, as evidenced by the flurry of PRs fixing Dreaming corpus ingestion ([#91182](https://github.com/openclaw/openclaw/pull/91182), [#90433](https://github.com/openclaw/openclaw/pull/90433)) and embedding batches ([#89138](https://github.com/openclaw/openclaw/pull/89138)).

## 7. User Feedback Summary
*   **Pain Points:** Users are frustrated by silent data loss (Write tool overwrites, bootstrap files ignored) and "embarrassing" UX failures where the agent's internal reasoning is exposed in professional Slack/Teams channels. Additionally, compaction logic failing on longer context windows is causing costly API failures with no in-channel recovery.
*   **Use Cases:** Heavy reliance on automated cron agents for workspace maintenance, and sophisticated multi-agent setups requiring isolated sandboxes that still support necessary write operations ([#37634](https://github.com/openclaw/openclaw/issues/37634) - sandbox `workspaceAccess: none` making workspaces read-only).
*   **Satisfaction:** The community is highly engaged in bug triage (providing deep root-cause analyses, e.g., [#88234](https://github.com/openclaw/openclaw/issues/88234)), but frustrated by the backlog of stale, high-impact issues lacking product decisions.

## 8. Backlog Watch
*   **[#25592](https://github.com/openclaw/openclaw/issues/25592) (Text between tool calls leaks):** Open since Feb 2026, 27 comments, rated 🦞 diamond lobster. Still needs a product decision on the boundary between internal thought and user delivery.
*   **[#29387](https://github.com/openclaw/openclaw/issues/29387) (Bootstrap files ignored in agentDir):** Open since Feb 2026, 14 comments. Breaks multi-agent setups, awaiting maintainer review.
*   **[#40001](https://github.com/openclaw/openclaw/issues/40001) (Write tool lacks append mode):** Open since March 2026. Causes silent data loss in cron jobs. A linked PR exists but needs prioritization.
*   **PR [#84009](https://github.com/openclaw/openclaw/pull/84009) (Daily session resets on schedule):** An XL-sized PR waiting on the author since May 19. Addresses session state hygiene but carries high merge risk.

---

## Cross-Ecosystem Comparison

## Cross-Project Ecosystem Report: AI Agents & Personal Assistants (2026-06-07)

### 1. Ecosystem Overview
The open-source AI agent and personal assistant ecosystem is currently characterized by aggressive feature velocity paired with a critical inflection point toward enterprise readiness. Projects are bifurcating between expanding communication channel integrations/multi-agent capabilities and urgently hardening runtime stability, security boundaries, and memory management. As agents transition from experimental tools to production systems, issues like context compaction, multi-tenant isolation, and secure tool execution have emerged as critical battlegrounds. The ecosystem remains highly dynamic, with top-tier projects aggressively closing bugs while struggling to balance rapid feature expansion against P1 regressions and architectural debt.

### 2. Activity Comparison

| Project | Issues (24h) | PRs (24h) | Release Status | Health Score (1-10) |
| :--- | :--- | :--- | :--- | :--- |
| **OpenClaw** | 311 updated | 500 updated | `v2026.6.5-beta.2` | 8 (High velocity, high P1 debt) |
| **ZeroClaw** | 42 updated (18 closed) | 50 updated (19 merged) | Hardening for `v0.8.0` | 8 (Strong S0/S1 resolution) |
| **PicoClaw** | 16 closed | 12 merged | `nightly` | 9 (Robust, defensive iteration) |
| **IronClaw** | 5 updated | 18 updated (6 merged) | Staged `v0.29.1` | 7 (Arch progress, CVE blocked) |
| **NanoBot** | 8 updated | 28 updated | None | 8 (Stable, enterprise focus) |
| **Hermes Agent** | 50 updated (2 closed) | 50 updated (3 merged) | None | 6 (Low closure rate, regressions) |
| **CoPaw** | 6 updated | 1 updated | None | 5 (Post-release regressions) |
| **Moltis** | 2 updated | 3 updated | None | 6 (Steady, review bottleneck) |
| **NanoClaw** | 3 updated | 11 updated | None | 5 (High onboarding friction) |
| **LobsterAI** | 16 updated (0 closed) | 0 | None | 3 (Stalled pipeline, stale backlog) |
| **NullClaw / TinyClaw / ZeptoClaw** | 0 | 0 | N/A | N/A (Dormant) |

### 3. OpenClaw's Position
**Advantages vs. Peers:** OpenClaw operates at a massive scale (500 PRs/311 issues in 24h) unmatched by any other project in the ecosystem. It leads in channel diversity (Teams, Telegram, Feishu, Discord) and advanced multi-agent/memory subsystems (Dreaming, runtime self-context). Its community provides exceptionally deep root-cause analyses.
**Technical Approach Differences:** OpenClaw is heavily "channel-first," focusing on fitting agentic capabilities into existing enterprise communication flows, whereas peers like ZeroClaw (TUI/CLI-first) and Hermes (Desktop-first) prioritize local interface resilience. OpenClaw is also pushing deeper into autonomous memory flushes and complex compaction modes compared to the simpler context-capping approaches seen in Moltis or NanoBot.
**Community Size Comparison:** OpenClaw boasts the largest and most vocal community, generating high engagement on architectural debates (e.g., 27 comments on thought-leakage). However, this community is currently more frustrated by stale product decisions than the communities of PicoClaw or NanoBot, which exhibit higher satisfaction due to responsive maintainership.

### 4. Shared Technical Focus Areas
*   **Multi-Tenant Isolation & Access Control:** Driven by enterprise deployment needs. *OpenClaw* (cron state contamination, subagent allowlists), *NanoBot* (per-user memory isolation, MCP `allowFrom`), *IronClaw* (canonical Reborn identity resolver), and *ZeroClaw* (memory-tool exclusion for ACP sessions) are all actively implementing boundaries between users, sessions, and tools.
*   **Context & Memory Management:** Preventing context overflow and compaction failures is universal. *OpenClaw* faces compaction wedges and append-mode data loss; *NanoBot* is battling cache invalidation from microcompaction and orphaned tool results; *Moltis* is capping persisted tool results; *IronClaw* is designing proactive context compaction.
*   **Tool Security & Execution Scoping:** Moving away from global permissions to granular scoping. *ZeroClaw* (enforcing `allowed_tools`/`denied_tools`), *PicoClaw* (frontmatter tool policy filters), and *IronClaw* (per-channel tool filtering) reflect a shared demand for secure, scoped tool execution.
*   **Provider & Protocol Compatibility:** Handling diverse LLM quirks. *NanoBot* and *LobsterAI* are fixing Copilot/DeepSeek reasoning content parsing; *Hermes* is fixing Bedrock deduplication; *ZeroClaw* is patching Llama 4 `<tool_calls>` parsing; *CoPaw* is fixing local vLLM regressions.

### 5. Differentiation Analysis
*   **OpenClaw & NanoBot:** Targeting enterprise multi-tenant deployments, but OpenClaw focuses on channel breadth and complex memory subsystems, while NanoBot focuses on strict isolation, security (SSRF, symlinks), and provider parity.
*   **PicoClaw & ZeroClaw:** Focused on runtime resilience and security. PicoClaw differentiates via defensive systems programming (Go error handling) and edge-device support (ARM), while ZeroClaw emphasizes TUI resilience, WASM plugin extensibility, and strict S0/S1 bug triage.
*   **Hermes Agent:** Stands out with a Desktop/Gateway architecture, focusing on OS-level integrations (macOS launchd, Windows services, IME support) and rich media branching, though currently hampered by version parity issues.
*   **IronClaw:** Undergoing a deep architectural overhaul ("Reborn"), prioritizing identity resolution and DB-backed configurations over immediate feature drops.
*   **CoPaw & LobsterAI:** More application-layer focused, dealing heavily with UI/UX (session management, model routing) but currently struggling with pipeline throughput and core stability.

### 6. Community Momentum & Maturity
*   **Rapid Iterators (High Momentum):** OpenClaw and ZeroClaw are moving fastest, closing high volumes of issues, but carrying technical debt. PicoClaw is iterating rapidly but with higher discipline (defensive sweeps).
*   **Stabilizing & Maturing:** NanoBot and IronClaw are in an enterprise-maturation phase, prioritizing multi-tenancy, identity, and security hardening over flashy features.
*   **Bottlenecked & Struggling:** Hermes Agent, CoPaw, and NanoClaw show active community demand but are bottlenecked by low merge rates, severe platform regressions, or broken onboarding paths. LobsterAI's pipeline has largely stalled.

### 7. Trend Signals
1.  **The "Thought Leak" Problem:** As agents adopt reasoning models, separating internal monologues from user-facing output is critical. OpenClaw's leaking of `<thinking>` blocks and NanoBot's empty `reasoning_content` drops indicate the industry needs standardized API boundaries for model reasoning states.
2.  **Identity-Based Auth over Static Keys:** ZeroClaw's demand for Subscription-Native OAuth and Azure Entra ID support, alongside IronClaw's "Reborn" identity resolver, signals that enterprise deployments strictly forbid static API keys. Agent developers must prioritize OAuth/Entra flows.
3.  **Multi-Model Visual Relays:** CoPaw's request for a `visual_model` fallback—where text-only LLMs route images to a vision model for pre-processing—highlights a trend toward modular, multi-model routing to optimize cost and capability.
4.  **Sandboxed Extensibility (WASM & MCP):** ZeroClaw's WASM plugin RFCs and PicoClaw/IronClaw's tool policy filters show that agentic extensibility must be sandboxed. Developers cannot rely on global permissions; runtime isolation for MCP servers and custom skills is mandatory.
5.  **Cron & Autonomous State Contamination:** OpenClaw's cron state contamination and Hermes' self-sabotaging Curator reveal that as agents act autonomously, their state mutations must be strictly isolated from interactive sessions to prevent systemic crashes.

---

## Peer Project Reports

<details>
<summary><strong>NanoBot</strong> — <a href="https://github.com/HKUDS/nanobot">HKUDS/nanobot</a></summary>

### 1. Today's Overview
NanoBot is experiencing highly active development, evidenced by 28 updated pull requests and 8 updated issues in the past 24 hours. The project's current focus leans heavily towards stability, security hardening, and fixing session management bugs, with several critical fixes already merged or under active review. Multi-user and enterprise readiness is a clear emerging theme, demonstrated by merged PRs for per-user memory isolation and MCP access control. Community engagement remains strong, particularly around provider compatibility (e.g., GitHub Copilot, DeepSeek) and WebUI enhancements. Overall, the project shows healthy iteration velocity with a strong emphasis on maturing its core infrastructure for production use.

### 2. Releases
No new releases were recorded today.

### 3. Project Progress
Several significant features and bug fixes were merged or closed today, advancing the project's multi-tenant capabilities and channel reliability:
*   **Multi-user Isolation & Security:** Merged PR [#2968](https://github.com/HKUDS/nanobot/pull/2968) introduced per-user memory isolation via `agents.defaults.per_user_memory`, and merged PR [#2533](https://github.com/HKUDS/nanobot/pull/2533) added per-MCP-server `allowFrom` access control—both critical for multi-tenant deployments.
*   **Channel Stability:** Merged PR [#2555](https://github.com/HKUDS/nanobot/pull/2555) fixed WhatsApp bridge duplicate messages on reconnect, and merged PR [#2663](https://github.com/HKUDS/nanobot/pull/2663) fixed WhatsApp LID group mentions.
*   **Reasoning Content Fix:** Closed PR [#4228](https://github.com/HKUDS/nanobot/pull/4228) addressed the empty string `reasoning_content` drop for custom providers, though an alternative open PR [#4227](https://github.com/HKUDS/nanobot/pull/4227) is also seeking to resolve the same issue.
*   **Ongoing Advancements:** Active open PRs include shared voice input support ([#4232](https://github.com/HKUDS/nanobot/pull/4232)), SSRF protection for MCP HTTP URLs ([#4123](https://github.com/HKUDS/nanobot/pull/4123)), and workspace security hardening against symlink escapes ([#4119](https://github.com/HKUDS/nanobot/pull/4119)).

### 4. Community Hot Topics
*   **GitHub Copilot Authentication & Enterprise Support:** Issue [#2573](https://github.com/HKUDS/nanobot/issues/2573) (👍 9) regarding GitHub Copilot login failure was recently closed. The high reaction count indicates this was a widespread pain point for users relying on Copilot as a provider. Additionally, Issue [#4220](https://github.com/HKUDS/nanobot/issues/4220) requests GitHub Copilot for Business/Enterprise support, signaling strong user demand for enterprise-grade provider integration.
*   **WebUI Quality of Life:** Issue [#4233](https://github.com/HKUDS/nanobot/issues/4233) requested displaying the version in the WebUI, which was immediately addressed by PR [#4235](https://github.com/HKUDS/nanobot/pull/4235) on the same day, showing high maintainer responsiveness to user experience feedback.

### 5. Bugs & Stability
*   **High Severity - Session History Wiped:** Issue [#4203](https://github.com/HKUDS/nanobot/issues/4203) reports that `find_legal_message_start` discards all messages when an orphaned tool result follows a user message. Fix PRs are already open: [#4219](https://github.com/HKUDS/nanobot/pull/4219) and [#4229](https://github.com/HKUDS/nanobot/pull/4229).
*   **Medium-High Severity - Cache Invalidation:** Issue [#4222](https://github.com/HKUDS/nanobot/issues/4222) notes that `max_messages` truncation and microcompact continuously invalidate prompt/prefix caching, causing performance and cost regressions. No fix PR is currently linked.
*   **Medium Severity - API Duplicate Turns:** PR [#4234](https://github.com/HKUDS/nanobot/pull/4234) addresses a bug where the OpenAI-compatible API retries empty responses, inadvertently duplicating user turns in the session history.
*   **Low-Medium Severity - Empty Reasoning Content:** Issue [#4105](https://github.com/HKUDS/nanobot/issues/4105) notes custom providers drop empty string `reasoning_content`, breaking providers like DeepSeek. Open PRs [#4227](https://github.com/HKUDS/nanobot/pull/4227) and closed PR [#4228](https://github.com/HKUDS/nanobot/pull/4228) target this.

### 6. Feature Requests & Roadmap Signals
*   **Enterprise/Multi-User Focus:** Requests for GitHub Enterprise support ([#4220](https://github.com/HKUDS/nanobot/issues/4220)) and the merged per-user memory/MCP access controls point to a roadmap heavily targeting enterprise and multi-tenant deployments.
*   **Sub-agent Flexibility:** Issue [#4231](https://github.com/HKUDS/nanobot/issues/4231) requests a `model` parameter for the `spawn` tool to allow subagent model overrides, indicating users are building complex, multi-model agentic workflows.
*   **Voice Input:** PR [#4232](https://github.com/HKUDS/nanobot/pull/4232) adds shared transcription configuration and WebUI/desktop voice input, likely landing in an upcoming release.
*   *Prediction:* The next release will likely focus on hardening multi-user isolation, fixing context window management (caching/orphaned tools), and enhancing WebUI features (version display, voice input).

### 7. User Feedback Summary
Users are actively deploying NanoBot in complex environments, utilizing shared channels (Discord, WhatsApp, Feishu) and advanced LLM providers (DeepSeek, Copilot, custom endpoints). Pain points center around context management—specifically, history trimming breaking caching ([#4222](https://github.com/HKUDS/nanobot/issues/4222)) and wiping context ([#4203](https://github.com/HKUDS/nanobot/issues/4203)). There is clear satisfaction with the project's channel support and MCP capabilities, but friction remains around provider-specific quirks (like reasoning content parsing) and enterprise features. The quick turnaround on the WebUI version request reflects positively on community interaction.

### 8. Backlog Watch
*   Issue [#4222](https://github.com/HKUDS/nanobot/issues/4222) (Cache invalidation via microcompact) has 0 comments and no linked PRs, yet it significantly impacts cost/performance for long conversational sessions.
*   Open security/infrastructure PRs like [#4119](https://github.com/HKUDS/nanobot/pull/4119) (symlink workspace escapes) and [#4123](https://github.com/HKUDS/nanobot/pull/4123) (SSRF guard for MCP) need prompt maintainer review as they represent critical attack vector mitigations.
*   Issue [#2256](https://github.com/HKUDS/nanobot/issues/2256) (Feishu bot reply in topic groups) was created in March and only closed today, suggesting the Feishu channel bridge might have slower iteration cycles compared to other messaging platforms.

</details>

<details>
<summary><strong>Hermes Agent</strong> — <a href="https://github.com/nousresearch/hermes-agent">nousresearch/hermes-agent</a></summary>

# Hermes Agent Project Digest — 2026-06-07

## 1. Today's Overview
Hermes Agent experienced high community engagement today, with 50 issues and 50 pull requests updated, though closure rates remain very low (only 2 issues and 3 PRs closed/merged) and no new releases were published. The project is clearly in an active development and testing phase for its gateway and desktop components, as evidenced by numerous bug reports surrounding v0.16.0 regressions. Significant attention is being directed toward platform-specific stability, particularly on macOS and Windows, as well as provider-level compatibility issues with newer models like GPT-5.x and Bedrock mantle. The high volume of open PRs (47) suggests substantial upcoming changes, but maintainers should ensure bottlenecked reviews don't stall critical stability fixes.

## 2. Releases
No new releases were published today.

## 3. Project Progress
Despite the low merge volume today, several critical and feature-rich pull requests saw activity, advancing the project's gateway robustness and desktop UX:
*   **Desktop & Gateway Media:** PR [#41336](https://github.com/NousResearch/hermes-agent/pull/41336) introduces remote media relay, finally allowing the desktop app to attach images/PDFs and display gateway images over the network.
*   **Desktop Branching:** PR [#40973](https://github.com/NousResearch/hermes-agent/pull/40973) implements Hermes-native session forks for the "branch-from-message" feature, replacing the old pseudo-branch behavior.
*   **Platform Support:** PR [#41054](https://github.com/NousResearch/hermes-agent/pull/41054) adds group-gating controls for WhatsApp, and PR [#41056](https://github.com/NousResearch/hermes-agent/pull/41056) adds C# and F# LSP support via dotnet global tools.
*   **Stability Fixes:** PR [#41324](https://github.com/NousResearch/hermes-agent/pull/41324) fixes a severe Discord event loop blocking issue, PR [#41307](https://github.com/NousResearch/hermes-agent/pull/41307) fixes cron skipping first-run jobs, and PR [#41332](https://github.com/NousResearch/hermes-agent/pull/41332) deduplicates cumulative output from the Bedrock mantle endpoint.

## 4. Community Hot Topics
*   **Japanese Localization & IME Issues:** Issue [#40219](https://github.com/NousResearch/hermes-agent/issues/40219) (5 comments) requested Japanese UI support, highlighting a broader need for i18n. This ties directly into bug [#39025](https://github.com/NousResearch/hermes-agent/issues/39025) (3 comments), where the Windows desktop Chinese IME input fails to submit, showing that non-English input is currently a major pain point.
*   **Context & Memory Management:** Issue [#10299](https://github.com/NousResearch/hermes-agent/issues/10299) (4 comments) discusses cascading context file discovery. Users working in monorepos or nested directories need the agent to respect parent-directory instructions (like `CLAUDE.md` or `.cursorrules`), reflecting a need for more sophisticated, hierarchical context loading.
*   **Autonomous Reliability:** Issue [#29912](https://github.com/NousResearch/hermes-agent/issues/29912) (4 comments) raised alarms about the Curator archiving active skills during consolidation. This reveals anxiety about the agent's autonomous self-modification capabilities causing self-sabotage without proper safeguards.

## 5. Bugs & Stability
*   **P1 / Critical Regressions:**
    *   **macOS 26 Launchd Regression:** Issue [#40831](https://github.com/NousResearch/hermes-agent/issues/40831) reports that PR #40581 hardcoded the `user/<uid>` launchd domain, breaking Aqua sessions. No fix PR is noted yet.
    *   **Silent Message Loss:** Issue [#34089](https://github.com/NousResearch/hermes-agent/issues/34089) notes that conversation compression desynchronizes session IDs. Fix is likely targeted by PR [#31073](https://github.com/NousResearch/hermes-agent/pull/31073).
    *   **Discord Gateway Freeze:** Issue [#41289](https://github.com/NousResearch/hermes-agent/issues/41289) reports the `/model` command blocks the event loop for 120-150s. Fixed by PR [#41324](https://github.com/NousResearch/hermes-agent/pull/41324).
*   **P2 / High Severity:**
    *   **Command Injection:** Issue [#16560](https://github.com/NousResearch/hermes-agent/issues/16560) flags `shell=True` vulnerabilities in `tui_gateway/server.py`.
    *   **Windows Update Data Loss:** Issue [#41255](https://github.com/NousResearch/hermes-agent/issues/41255) reports `hermes update` deletes the `gateway-service/` directory on Windows.
    *   **Background Process Kills:** Issues [#41225](https://github.com/NousResearch/hermes-agent/issues/41225) and [#41235](https://github.com/NousResearch/hermes-agent/issues/41235) report `terminal(background=true)` processes receiving SIGTERM during agent `release()`.
    *   **OAuth Token Conflicts:** Issue [#22903](https://github.com/NousResearch/hermes-agent/issues/22903) reports sibling Codex clients invalidating Hermes OAuth refresh tokens.
*   **Desktop Boot/Build Failures:** Issue [#41329](https://github.com/NousResearch/hermes-agent/issues/41329) describes a boot loop due to Desktop v0.15.1 probing a missing `/api/ws` route in core v0.16.0. Issue [#41327](https://github.com/NousResearch/hermes-agent/issues/41327) reports 404s due to missing `asarUnpack` for `dist/`.

## 6. Feature Requests & Roadmap Signals
*   **Desktop UX Enhancements:** Requests for a "Send Selection to Composer" right-click option ([#41254](https://github.com/NousResearch/hermes-agent/issues/41254)) and a button to merge queued prompts ([#41247](https://github.com/NousResearch/hermes-agent/issues/41247)) indicate power users are running long, complex agent interactions and need better conversation steering tools.
*   **Audit & Observability:** The request for an Agent Audit Log ([#1155](https://github.com/NousResearch/hermes-agent/issues/1155)) persists, vital for enterprise trust and debugging autonomous actions.
*   **Next Version Prediction:** The upcoming version will likely focus heavily on Desktop/Gateway version parity (fixing the boot loops and 404s) and provider streaming fixes (Bedrock/Codex deduplication). Features like remote media relay (#41336) and native session branching (#40973) appear mature enough for the next release cycle.

## 7. User Feedback Summary
Users are heavily utilizing Hermes across diverse platforms (Discord, Telegram, WhatsApp, macOS launchd, Windows services), but platform-specific friction—especially on Windows and macOS—is a major source of dissatisfaction. Windows users in particular feel like second-class citizens due to update-induced data loss (#41255) and IME failures (#39025). The transition to core v0.16.0 has left Desktop users stranded with incompatible builds (#41329). However, the demand for advanced features (CDN for restricted networks [#41291](https://github.com/NousResearch/hermes-agent/issues/41291), custom email domains [#41331](https://github.com/NousResearch/hermes-agent/issues/41331)) shows a dedicated, technically proficient user base pushing the agent into production environments.

## 8. Backlog Watch
*   **Security Vulnerability:** Issue [#16560](https://github.com/NousResearch/hermes-agent/issues/16560) (Command injection via `shell=True`) has been open since April 27. This P2 security issue requires immediate maintainer attention and patching.
*   **Agent Audit Log:** Issue [#1155](https://github.com/NousResearch/hermes-agent/issues/1155) has been open since March 13. Given the increasing autonomy of the agent (as seen in Curator concerns), an audit log is crucial and remains unaddressed.
*   **Cascading Context Discovery:** Issue [#10299](https://github.com/NousResearch/hermes-agent/issues/10299) (open since April 15) represents a significant architectural limitation in how the agent ingests project instructions. Maintainer input on the proposed pluggable context loaders would unblock community contributions.

</details>

<details>
<summary><strong>PicoClaw</strong> — <a href="https://github.com/sipeed/picoclaw">sipeed/picoclaw</a></summary>

# PicoClaw Project Digest (2026-06-07)

## 1. Today's Overview
PicoClaw demonstrates robust and active maintenance, closing 16 issues and merging 12 PRs in the last 24 hours, alongside a new nightly release. The development focus is heavily tilted toward code stability and defensive programming, with a concerted effort to eliminate silently ignored errors and unchecked type assertions across the Go codebase. The project's feature horizon continues to expand, evidenced by the merging of a multi-agent collaboration framework and Google Chat channel support, while the community actively engages on edge-case bugs and deployment flexibility for ARM devices.

## 2. Releases
- **nightly**: `v0.2.9-nightly.20260607.7d2b0c2a`
  - Automated build from the `main` branch.
  - Note: Marked as potentially unstable. Includes all recently merged defensive fixes, provider updates, and multi-agent framework foundations.

## 3. Project Progress
Significant progress was made in system robustness, agent capabilities, and provider/channel expansions:
- **Code Quality & Stability**: Contributor `chengzhichao-xydt` led a massive sweep to handle previously ignored errors in Go. Merged PRs include checking `Close()` errors after file downloads/writes ([PR #3033](https://github.com/sipeed/picoclaw/pull/3033), [PR #3034](https://github.com/sipeed/picoclaw/pull/3034), [PR #3035](https://github.com/sipeed/picoclaw/pull/3035)), checking `os.Getwd()` errors ([PR #3042](https://github.com/sipeed/picoclaw/pull/3042)), and validating `strconv.Atoi`/`json.Unmarshal` outputs ([PR #3043](https://github.com/sipeed/picoclaw/pull/3043)). 
- **Critical Bug Fix**: A goroutine leak in `Manager.Reload()` that caused indefinite background processes was fixed ([PR #3016](https://github.com/sipeed/picoclaw/pull/3016)).
- **Multi-Agent Framework**: The base multi-agent collaboration framework with shared context pool, agent handoff, and discovery tools ([PR #423](https://github.com/sipeed/picoclaw/pull/423)) was merged.
- **Channels & Providers**: Google Chat channel support went live ([PR #830](https://github.com/sipeed/picoclaw/pull/830)). DeepSeek-ai protocol support for ModelScope.cn was added ([PR #1112](https://github.com/sipeed/picoclaw/pull/1112)), and the Anthropic default model ID was corrected to prevent API 404s ([PR #3036](https://github.com/sipeed/picoclaw/pull/3036)).
- **Agent Policies**: Support for frontmatter tool policy filters (allow/deny glob patterns) in AGENT.md was merged ([PR #2838](https://github.com/sipeed/picoclaw/pull/2838)).

## 4. Community Hot Topics
- **ARM Builds & WhatsApp Support**: [Issue #2625](https://github.com/sipeed/picoclaw/issues/2625) (👍 1, 8 comments) highlights a strong user need for pre-compiled ARM64 builds that include WhatsApp support. Users running PicoClaw on Raspberry Pi Zero 2 devices find it difficult to compile from source with the necessary flags.
- **Agent-to-Agent Communication**: [Issue #2929](https://github.com/sipeed/picoclaw/issues/2929) (👍 2, 3 comments) sparked discussion on moving beyond parent-child agent hierarchies (`spawn`/`delegate`) to a peer-to-peer first-class communication layer for cooperative workflows. This aligns directly with the recently merged PR #423.
- **Trading/Exchange Sub-system**: A high volume of issues ([#3024](https://github.com/sipeed/picoclaw/issues/3024) to [#3032](https://github.com/sipeed/picoclaw/issues/3032)) were closed, detailing the implementation of a high-performance exchange connector (Binance REST/WebSocket, lock-free order books, risk manager). This indicates a specialized, actively developed sub-project within PicoClaw.

## 5. Bugs & Stability
- **High Severity - Goroutine Leak**: Channel config reloads leaked goroutines indefinitely. Fixed in [PR #3016](https://github.com/sipeed/picoclaw/pull/3016).
- **High Severity - Anthropic API 404s**: Fresh installs seeded `claude-sonnet-4.6` (dots), which the Anthropic API rejects in favor of `claude-sonnet-4-6` (hyphens). Fixed in [PR #3036](https://github.com/sipeed/picoclaw/pull/3036).
- **Medium Severity - Matrix Identity Parsing**: `allow_from` silently drops messages from Matrix users with standard IDs like `@alice:example.com` because the parser splits on the first colon. Fix proposed in [PR #3045](https://github.com/sipeed/picoclaw/pull/3045), addressing [Issue #3044](https://github.com/sipeed/picoclaw/issues/3044).
- **Medium Severity - MCP CLI Flag Parsing**: [Issue #3041](https://github.com/sipeed/picoclaw/issues/3041) reports that `mcp add` mis-parses global flags, breaking http/sse server additions and mis-naming stdio servers. No fix PR is open yet.
- **Low Severity - Frontend Clipboard API**: Copy buttons failed in HTTP (non-secure) contexts. Fixed via graceful degradation in [PR #2711](https://github.com/sipeed/picoclaw/pull/2711).

## 6. Feature Requests & Roadmap Signals
- **Native Kagi Search**: [PR #3037](https://github.com/sipeed/picoclaw/pull/3037) (Open) introduces Kagi as a native web search provider, signaling an upcoming expansion of search engine options beyond the defaults.
- **Tool Policy Filters**: The merging of [PR #2838](https://github.com/sipeed/picoclaw/pull/2838) sets the stage for highly granular, security-focused agent tool access controls in the next stable release.
- **First-Class Agent P2P Communication**: [Issue #2929](https://github.com/sipeed/picoclaw/issues/2929) indicates that while multi-agent shared context is merged, peer-to-peer agent messaging is the next logical roadmap item requested by the community.

## 7. User Feedback Summary
- **Deployment Friction**: Users deploying on edge devices (like Raspberry Pi) express frustration over the lack of inclusive pre-compiled binaries, forcing them to build from source.
- **Operational Documentation**: Users are asking for clearer guides on upgrading instances from source ([Issue #2834](https://github.com/sipeed/picoclaw/issues/2834)).
- **Silent Failures**: Several user-reported bugs (e.g., Matrix messages silently failing, MCP commands silently misconfiguring) stem from the application swallowing errors rather than providing feedback—a trend actively being rectified by the maintainers today.
- **Workspace Skills Usability**: The built-in skill-creator is currently broken out-of-the-box ([Issue #652](https://github.com/sipeed/picoclaw/issues/652)), impacting users trying to author custom agent behaviors.

## 8. Backlog Watch
- **[PR #2935](https://github.com/sipeed/picoclaw/pull/2935) (Stale)**: Add Traditional Chinese (zh-TW) locale and READMEs. Open since late May with no recent maintainer engagement. Internationalization contributions are stalling.
- **[Issue #3041](https://github.com/sipeed/picoclaw/issues/3041) (Open)**: The `mcp add` flag parsing bug needs triage as it directly breaks the CLI workflow for adding HTTP/SSE MCP servers.
- **[Issue #2625](https://github.com/sipeed/picoclaw/issues/2625) (Stale)**: ARM64 WhatsApp build request remains unanswered. With the growing trend of running local AI on edge devices, resolving this could unlock a significant user base.
- **Open Defensive PRs**: A suite of error-handling PRs by `chengzhichao-xydt` ([PR #3018](https://github.com/sipeed/picoclaw/pull/3018), [PR #3046](https://github.com/sipeed/picoclaw/pull/3046), [PR #3047](https://github.com/sipeed/picoclaw/pull/3047)) are open and awaiting final review/merge to further solidify the codebase.

</details>

<details>
<summary><strong>NanoClaw</strong> — <a href="https://github.com/qwibitai/nanoclaw">qwibitai/nanoclaw</a></summary>

### 1. Today's Overview
NanoClaw shows moderate-to-high development activity as of 2026-06-07, with 11 pull requests updated and 3 new or active issues opened. The project is currently focused on hardening its setup and upgrade paths, as evidenced by multiple PRs addressing orphaned containers, broken CLI commands, and upgrade state management. However, the ratio of open issues to closed issues (3:0) today indicates that bug resolution is currently lagging behind bug discovery. Overall, the project's health appears active but slightly constrained by onboarding friction and configuration management edge cases.

### 2. Releases
*(Omitted — no new releases recorded today.)*

### 3. Project Progress
Three PRs were merged/closed today, advancing system reliability and architecture:
*   **Upgrade Safety:** [PR #2707](https://github.com/qwibitai/nanoclaw/pull/2707) introduced a startup tripwire and upgrade marker. This prevents silent breakages from raw `git pull` updates by forcing migrations through sanctioned paths.
*   **Account Management:** [PR #2706](https://github.com/qwibitai/nanoclaw/pull/2706) fixed account rotation logic by restricting Codex/Gemini modes from entering Anthropic auto-rotation, calibrating DB cursor drift, and improving process termination (SIGTERM -> SIGKILL fallback).
*   **Skill Architecture:** [PR #2698](https://github.com/qwibitai/nanoclaw/pull/2698) merged the skills conformance fleet retrofit, making the skill library upgrade-maintainable by enforcing a standardized model (idempotent deletions, no `VERIFY.md`, functional tests).

### 4. Community Hot Topics
*   **Slack Socket Mode Migration:** Two highly related PRs—[PR #2702](https://github.com/qwibitai/nanoclaw/pull/2702) (adapter fix) and [PR #2700](https://github.com/qwibitai/nanoclaw/pull/2700) (skill setup fix)—aim to switch Slack integration from HTTP webhooks to Socket Mode. This reflects a strong underlying need from the community to run NanoClaw without exposing public endpoints.
*   **Persistent Dirty Working Tree:** [Issue #2312](https://github.com/qwibitai/nanoclaw/issues/2312) remains a sticking point with 2 comments. Users are frustrated that `groups/global/CLAUDE.md` is committed to the repo but deleted on startup, causing permanent Git dirty states. This highlights a clash between default repo configuration and runtime migration scripts.

### 5. Bugs & Stability
*   **High Severity:** [Issue #2703](https://github.com/qwibitai/nanoclaw/issues/2703) — The recommended fresh install path leaves `cli/local` unwired, causing `pnpm run chat hi` to hang for 120s and crash. This is a critical onboarding blocker for new users. *No fix PR is currently attached.*
*   **Medium Severity:** [Issue #2701](https://github.com/qwibitai/nanoclaw/issues/2701) — `ncl groups restart --rebuild` fails when `packages_apt` and `packages_npm` are empty. Rebuild logic needs to skip package installation gracefully. *No fix PR is currently attached.*
*   **Medium Severity:** [PR #2705](https://github.com/qwibitai/nanoclaw/pull/2705) — The `use-native-credential-proxy` silently falls back to the OneCLI gateway on real installs (launchd/systemd) due to environment variable reading failures. A fix PR is currently open.
*   **Low Severity:** [PR #2699](https://github.com/qwibitai/nanoclaw/pull/2699) — `ncl groups create` generates UUIDs that fail OneCLI validation because they don't start with a letter. Fix PR is open.

### 6. Feature Requests & Roadmap Signals
*   **DB-Backed Container Configs:** [PR #2709](https://github.com/qwibitai/nanoclaw/pull/2709) proposes adding DB-backed `env` and `blocked_hosts` JSON columns to `container_configs`. This signals a roadmap shift toward dynamic, database-driven environment configurations rather than static file-based setups.
*   **Telegram Topic Isolation:** [PR #1626](https://github.com/qwibitai/nanoclaw/pull/1626) (open since April) continues to see activity, indicating sustained interest in multi-tenant chat channel isolation. This is a strong candidate for the next integration milestone once current setup/CLI bugs are resolved.

### 7. User Feedback Summary
Users are experiencing significant friction during initial setup and local deployment. The most prominent pain point is that the "recommended" setup path is currently broken out-of-the-box ([Issue #2703](https://github.com/qwibitai/nanoclaw/issues/2703)), eroding first-impression trust. Furthermore, self-hosters running NanoClaw as a system service (systemd/launchd) are finding that features like native credential proxies fail silently ([PR #2705](https://github.com/qwibitai/nanoclaw/pull/2705)). Positively, the merging of the skills conformance PR ([PR #2698](https://github.com/qwibitai/nanoclaw/pull/2698)) and upgrade tripwire ([PR #2707](https://github.com/qwibitai/nanoclaw/pull/2707)) shows that maintainers are actively responding to user pain around update breakages and skill maintenance.

### 8. Backlog Watch
*   [Issue #2312](https://github.com/qwibitai/nanoclaw/issues/2312): Open for a month, this `CLAUDE.md` deletion bug creates constant dirty Git states. It requires maintainer attention to resolve the conflict between the repository's committed files and the `migrateGroupsToClaudeLocal()` function.
*   [PR #1626](https://github.com/qwibitai/nanoclaw/pull/1626): The Telegram topic isolation PR has been open for over two months. It needs a maintainer review to either merge, request changes, or align it with the new skills conformance guidelines introduced in [PR #2698](https://github.com/qwibitai/nanoclaw/pull/2698).

</details>

<details>
<summary><strong>NullClaw</strong> — <a href="https://github.com/nullclaw/nullclaw">nullclaw/nullclaw</a></summary>

No activity in the last 24 hours.

</details>

<details>
<summary><strong>IronClaw</strong> — <a href="https://github.com/nearai/ironclaw">nearai/ironclaw</a></summary>

# IronClaw Project Digest — 2026-06-07

## 1. Today's Overview
IronClaw experienced high development velocity today, with 18 pull requests updated (12 open, 6 merged/closed) and 5 issues updated. The core team is heavily focused on the "Reborn" architectural initiative, merging critical identity resolution, CI optimization, and design documentation. Agent reliability also saw notable improvement with the merging of a premature stop heuristic fix. A pending major release PR (`0.24.0` -> `0.29.1`) indicates the project is on the cusp of a significant version bump, though downstream consumption is currently bottlenecked by unresolved dependency CVEs.

## 2. Releases
No new releases were published today. However, PR [#3708](https://github.com/nearai/ironclaw/pull/3708) is actively staging a major release (`ironclaw` v0.24.0 -> v0.29.1) which includes API-breaking changes in `ironclaw_common` and `ironclaw_skills`. This release is currently held up, as reflected in Issue #3259.

## 3. Project Progress
Significant advancements were merged today, primarily strengthening the Reborn architecture and agent reliability:
*   **OAuth & Identity**: Merged canonical Reborn identity resolver for OAuth and external actors ([PR #4461](https://github.com/nearai/ironclaw/pull/4461), closing [Issue #4381](https://github.com/nearai/ironclaw/issues/4381)), replacing the previous WebUI-shaped user store with a stable `UserId` mapping system.
*   **Agent Loop Fix**: Merged a fix for the premature stop heuristic ([PR #4524](https://github.com/nearai/ironclaw/pull/4524)), removing a coarse condition that treated three identical `LoopFailureKind`s as a lack of progress, which should significantly reduce unwanted agent interruptions.
*   **Channel Routing**: Merged Slack channel route admin wiring ([PR #4510](https://github.com/nearai/ironclaw/pull/4510)), enabling durable Slack channel route stores and WebUI admin management.
*   **Architecture Design**: Merged unified design documentation for background subagents, proactive context compaction, and WebUI run nesting ([PR #4485](https://github.com/nearai/ironclaw/pull/4485), [PR #4486](https://github.com/nearai/ironclaw/pull/4486)).
*   **CI Optimization**: Merged CI scoping to keep Reborn-only PRs out of legacy test partitions ([PR #4520](https://github.com/nearai/ironclaw/pull/4520)).
*   **Feature Parity**: Closed Gateway System feature parity tracking issue ([Issue #30](https://github.com/nearai/ironclaw/issues/30)) and the persistent Feed system feature request ([Issue #70](https://github.com/nearai/ironclaw/issues/70)).

## 4. Community Hot Topics
*   **Crates.io Publishing Blockage ([Issue #3259](https://github.com/nearai/ironclaw/issues/3259))**: With 13 comments, this is the most active discussion today. Downstream users are currently pinned to `v0.24.0` on crates.io due to `wasmtime` 28.x CVEs, despite the GitHub repository being tagged up to `v0.27.0`. The community is actively seeking a resolution to this supply-chain and security bottleneck so they can consume the latest patches safely.
*   **Per-Channel Tool Filtering ([PR #1378](https://github.com/nearai/ironclaw/pull/1378))**: This open XL-sized PR introducing JSON-configurable channel routing for MCP servers and built-in tools continues to see updates. It addresses a strong operational need for multi-channel deployments (e.g., Slack vs. Web) to scope tools appropriately.

## 5. Bugs & Stability
*   🔴 **High: Nightly E2E Failure** ([Issue #4108](https://github.com/nearai/ironclaw/issues/4108)): The scheduled Nightly E2E run failed on commit `e96938e`. No fix PR is referenced yet, posing a risk to the pending v0.29.1 release.
*   🟡 **Medium: Premature Stop Heuristic** ([PR #4524](https://github.com/nearai/ironclaw/pull/4524)): The agent was incorrectly terminating loops upon encountering three identical failure kinds. **Fix merged** today.
*   🟡 **Medium: System Sentinel Deserialization** ([PR #4523](https://github.com/nearai/ironclaw/pull/4523)): `TenantId`/`UserId` deserialization rejected the `\x1fSYSTEM\x1f` sentinel used by `ResourceScope::system()`, causing `service_unavailable` errors on LLM settings endpoints. **Fix PR is open**.

## 6. Feature Requests & Roadmap Signals
Today's open PRs reveal a strong trajectory toward granular user configuration, expanded UI controls, and robust LLM parsing:
*   **User-Scoped Skills UI** ([PR #4527](https://github.com/nearai/ironclaw/pull/4527)): Adding a Settings UI for users to manage their own system/workspace skills.
*   **Reborn Onboarding** ([PR #4525](https://github.com/nearai/ironclaw/pull/4525)): Adding an `ironclaw-reborn onboard` CLI command for first-run initialization without touching v1 state.
*   **Structured Capability Failures** ([PR #4526](https://github.com/nearai/ironclaw/pull/4526)): Introducing Claude-Code-style model-visible provider error summaries.
*   **LLM Parsing Primitives** ([PR #4522](https://github.com/nearai/ironclaw/pull/4522)): Scaffolding shared parsing utilities (Phase A of the RC3/M9 provider parsing framework).
*   *Prediction*: The next release (v0.29.1) will likely ship the Reborn identity resolver, the premature stop fix, and the Reborn onboarding command, pending the resolution of the E2E test failures and the wasmtime CVE bottleneck.

## 7. User Feedback Summary
*   **Pain Point - Dependency Pinning**: Downstream Rust consumers are visibly frustrated by being locked to `v0.24.0` on crates.io due to transitive `wasmtime` vulnerabilities, preventing them from accessing months of updates.
*   **Pain Point - Agent Stubbornness**: Users experienced issues where the agent would prematurely stop tasks under repetitive failure conditions, a friction point resolved by today's merge of [PR #4524](https://github.com/nearai/ironclaw/pull/4524).
*   **Desire - Non-intrusive Updates**: The closure of the Feed feature request ([Issue #70](https://github.com/nearai/ironclaw/issues/70)) highlights user demand for persistent, queryable information streams that don't require interrupting assistant messages.

## 8. Backlog Watch
*   **[Issue #3259](https://github.com/nearai/ironclaw/issues/3259)**: Open since May 5th, this crates.io publishing blockage needs maintainer action to resolve `wasmtime` CVE conflicts. It directly impacts downstream adoption and should be top priority.
*   **[PR #1378](https://github.com/nearai/ironclaw/pull/1378)**: Open since March 18th, this XL PR for per-channel MCP routing touches critical agent, tool, and worker scopes. It requires dedicated reviewer bandwidth to unblock multi-channel deployments.
*   **[PR #3708](https://github.com/nearai/ironclaw/pull/3708)**: The release PR has been open since May 16th. Its prolonged open state suggests it is blocked by the aforementioned E2E failures and dependency CVEs, needing synchronization to push the v0.29.1 release through.

</details>

<details>
<summary><strong>LobsterAI</strong> — <a href="https://github.com/netease-youdao/LobsterAI">netease-youdao/LobsterAI</a></summary>

Here is the structured project digest for LobsterAI based on the provided GitHub data for 2026-06-07:

### 1. Today's Overview
LobsterAI currently exhibits low development activity, with zero pull requests merged, closed, or opened today, and no new releases. However, there is lingering community engagement, evidenced by 16 open issues updated in the last 24 hours, including two newly created issues. The vast majority of the active issues are marked as `[stale]`, indicating a growing backlog of unresolved bugs and feature requests. The project's immediate health is cautious; while users are actively testing and providing detailed feedback, the lack of corresponding development throughput risks alienating the user base if the backlog continues to accumulate.

### 2. Releases
No new releases were recorded today.

### 3. Project Progress
There was no measurable project progress today. No pull requests were opened, merged, or closed, indicating a stalled development pipeline. Consequently, no features advanced, and no bugs were fixed via code commits in the last 24 hours.

### 4. Community Hot Topics
The most recent community discussions stem from two newly opened issues, highlighting active user testing and concern over resource consumption:
*   **[Issue #2120](https://github.com/netease-youdao/LobsterAI/issues/2120)**: Users are requesting task queuing (pre-inputting the next task while one runs, similar to WorkBuddy), extended task timeout limits (due to `terminated` errors during long-running monitoring scripts), and UI adjustments for high-res screens (2560x1600). This indicates power users are pushing the agent's execution boundaries.
*   **[Issue #2121](https://github.com/netease-youdao/LobsterAI/issues/2121)**: A user suspects a bug causing repetitive text output, raising valid concerns about unnecessary token consumption.
*   **[Issue #1509](https://github.com/netease-youdao/LobsterAI/issues/1509)** (2 comments): Users are frustrated by the blocking nature of skill generation and the lack of intermediate processing states (thinking process), comparing LobsterAI's model comprehension unfavorably to OpenClaw.

### 5. Bugs & Stability
Several significant bugs were reported or remain active, primarily focusing on state management, silent failures, and UI validation. No fix PRs exist for these currently.
*   **High Severity - State Management & Prompt Injection**: [Issue #1500](https://github.com/netease-youdao/LobsterAI/issues/1500) (Disabled skills remaining in `activeSkillIds` and injected into prompts) and [Issue #1502](https://github.com/netease-youdao/LobsterAI/issues/1502) (Skills not syncing in the current session without an Agent switch). These directly impact core AI behavior and reliability.
*   **High Severity - Authentication**: [Issue #1516](https://github.com/netease-youdao/LobsterAI/issues/1516) - GitHub Copilot OAuth token is silently lost if the Settings panel is closed during polling, forcing users to re-authenticate.
*   **Medium Severity - Silent Failures**: [Issue #1506](https://github.com/netease-youdao/LobsterAI/issues/1506) - Scheduled tasks with IM notifications silently fail if the target session isn't selected, providing zero feedback to the user.
*   **Low Severity - UI/Validation**: [Issue #1504](https://github.com/netease-youdao/LobsterAI/issues/1504) (Missing required field validation for POPO AES Key), [Issue #1512](https://github.com/netease-youdao/LobsterAI/issues/1512) (Missing input field for QQ Bot allowlist), and [Issue #1513](https://github.com/netease-youdao/LobsterAI/issues/1513) (Inconsistent formatting in Declaration Terms).
*   **Potential Stability/Resource Issue**: [Issue #2121](https://github.com/netease-youdao/LobsterAI/issues/2121) - Repeated output text potentially wasting user tokens.

### 6. Feature Requests & Roadmap Signals
Users are heavily requesting features related to information architecture and task management, signaling the product is being used for complex, long-term workflows:
*   **Session Management & Organization**: A cohesive cluster of requests aims to upgrade the session list from a linear log to a structured database. Key requests include color-coding ([Issue #1525](https://github.com/netease-youdao/LobsterAI/issues/1525)), tagging and filtering ([Issue #1541](https://github.com/netease-youdao/LobsterAI/issues/1541)), batch exporting ([Issue #1528](https://github.com/netease-youdao/LobsterAI/issues/1528)), and bookmarking important AI replies ([Issue #1537](https://github.com/netease-youdao/LobsterAI/issues/1537)).
*   **Task Execution Control**: Requests for task queuing and longer execution timeouts ([Issue #2120](https://github.com/netease-youdao/LobsterAI/issues/2120)) indicate that users want to run automated scripts and monitoring tasks without babysitting the UI.
*   **Local Analytics**: [Issue #1532](https://github.com/netease-youdao/LobsterAI/issues/1532) requests local usage statistics.
*   *Prediction*: The session management features (tags, colors, export) represent the most urgent UX need and are likely candidates for the next version's roadmap if the project aims to retain power users.

### 7. User Feedback Summary
*   **Pain Points**: Users experience poor model comprehension compared to competitors like OpenClaw ([Issue #1509](https://github.com/netease-youdao/LobsterAI/issues/1509)) and are concerned about resource waste (tokens) due to repetitive outputs ([Issue #2121](https://github.com/netease-youdao/LobsterAI/issues/2121)). The lack of intermediate "thinking" states during long operations causes anxiety, as users cannot tell if the agent is stalled or working.
*   **Use Cases**: Users are actively utilizing LobsterAI for long-running data acquisition/monitoring scripts ([Issue #2120](https://github.com/netease-youdao/LobsterAI/issues/2120)) and managing multiple distinct conversation threads (work/personal/experimental), which drives the demand for tagging and color-coding.
*   **Satisfaction**: There is underlying frustration regarding silent failures (IM notifications, OAuth tokens) and state synchronization bugs. The lack of visual feedback and progress indicators diminishes the perceived reliability of the agent.

### 8. Backlog Watch
The project has a significant stale backlog, with 14 issues from April 2026 remaining unresolved and unmaintained for over two months. Critical items requiring immediate maintainer attention include:
*   **Core Skill System Bugs**: [Issue #1509](https://github.com/netease-youdao/LobsterAI/issues/1509) (Skill generation blocking) and [Issue #1500](https://github.com/netease-youdao/LobsterAI/issues/1500) / [Issue #1502](https://github.com/netease-youdao/LobsterAI/issues/1502) (Skill state management).
*   **Authentication Flow**: [Issue #1516](https://github.com/netease-youdao/LobsterAI/issues/1516) (Copilot OAuth token loss).
*   **CI/CD Pipeline**: [Issue #1518](https://github.com/netease-youdao/LobsterAI/issues/1518) (Labeler permissions and lint strategy failures). This is particularly important as broken CI pipelines may be blocking external community contributions and causing the current development stagnation.

</details>

<details>
<summary><strong>TinyClaw</strong> — <a href="https://github.com/TinyAGI/tinyagi">TinyAGI/tinyagi</a></summary>

No activity in the last 24 hours.

</details>

<details>
<summary><strong>Moltis</strong> — <a href="https://github.com/moltis-org/moltis">moltis-org/moltis</a></summary>

# Moltis Project Digest — 2026-06-07

## 1. Today's Overview
Moltis is currently experiencing steady development activity with a focus on integration stability and data management, though no items were merged or closed today. The project saw 2 active issues and 3 open pull requests updated within the last 24 hours, indicating ongoing iterative work rather than feature finalization. Core contributor activity is heavily concentrated on refining channel integrations and context memory handling. No new releases were cut, suggesting the current cycle is still accumulating fixes and features before a stable release. Overall project health appears stable but requires maintainer review to progress the existing queue of open PRs.

## 2. Releases
No new releases were published today.

## 3. Project Progress
No PRs were merged or closed today. However, active development continues on three open fronts:
*   **Telegram Integration:** [PR #1113](https://github.com/moltis-org/moltis/pull/1113) advances a hotfix for Telegram's edit-in-place streaming, ensuring final replies stream correctly even when completion notifications are disabled.
*   **Context/Memory Management:** [PR #1089](https://github.com/moltis-org/moltis/pull/1089) (open since June 1, updated today) introduces a cap on persisted tool results during session rehydration, which will optimize token usage and prevent context overflow across various chat modes.
*   **Privacy & Logging:** [PR #1093](https://github.com/moltis-org/moltis/pull/1093) (open since June 3, updated today) implements granular `activity_log` visibility settings (all/errors_only/off) at the account, channel, and user levels, giving admins and users finer control over AI activity footprints.

## 4. Community Hot Topics
The most active discussions today revolve around deployment friction and mobile usability, each with 1 comment:
*   **[Issue #1107](https://github.com/moltis-org/moltis/issues/1107) (Multiline text input in the mobile web UI):** Users are discussing the constraints of single-line input on mobile devices. The underlying need is for a more accessible, native-like conversational experience on smartphones, which is critical for a personal AI assistant's adoption.
*   **[Issue #1112](https://github.com/moltis-org/moltis/issues/1112) (Disabling auth doesn't seem to disable auth in Docker):** Discussion centers on environment variable handling during container deployment. The underlying need is for reliable, straightforward self-hosting configurations, particularly for users deploying Moltis in secure, internal networks where authentication is unnecessary.

## 5. Bugs & Stability
*   **High Severity:** [Issue #1112](https://github.com/moltis-org/moltis/issues/1112) - Authentication bypass failure in Docker. Setting the project to disable auth does not remove the auth requirement, effectively blocking self-hosted single-user or internal-network deployments. No fix PR is currently attached to this issue.
*   **Medium Severity:** [PR #1113](https://github.com/moltis-org/moltis/pull/1113) - Telegram streaming bug where final replies fail to render properly in edit-in-place mode if completion notifications are turned off. This is a regression/edge case from a previous PR (#1099), and an open fix PR is currently under review.

## 6. Feature Requests & Roadmap Signals
*   **Mobile UX Enhancement:** [Issue #1107](https://github.com/moltis-org/moltis/issues/1107) requests multiline text input for the mobile web UI. This signals a growing use case of Moltis as an on-the-go companion, where mobile web access is preferred over native apps.
*   **Upcoming Features:** Based on open PRs, the next version will likely feature improved token/context efficiency via tool result capping ([PR #1089](https://github.com/moltis-org/moltis/pull/1089)) and sophisticated activity logging controls ([PR #1093](https://github.com/moltis-org/moltis/pull/1093)), pointing to a roadmap focused on enterprise/privacy readiness and LLM cost optimization.

## 7. User Feedback Summary
Real user pain points highlight a friction divide between advanced local deployments and daily UI usage. Self-hosters utilizing Docker are experiencing configuration bottlenecks, specifically with authentication flags not taking effect, leading to deployment dissatisfaction. On the front-end, mobile web users feel constrained by single-line inputs, disrupting the conversational flow expected from a modern AI assistant. Additionally, power users leveraging Telegram integrations are encountering disrupted streaming experiences, indicating that recent streaming updates need further edge-case handling.

## 8. Backlog Watch
*   **[PR #1089](https://github.com/moltis-org/moltis/pull/1089) & [PR #1093](https://github.com/moltis-org/moltis/pull/1093):** Both of these substantial PRs by s-salamatov have been open for 4-6 days without merging or visible recent maintainer review. They introduce important structural changes (context capping and visibility settings) and need priority review to prevent branch drift.
*   **[Issue #1112](https://github.com/moltis-org/moltis/issues/1112):** The Docker auth bug needs immediate triage. Broken deployment configurations can severely impact new user onboarding and self-hosted adoption rates.

</details>

<details>
<summary><strong>CoPaw</strong> — <a href="https://github.com/agentscope-ai/CoPaw">agentscope-ai/CoPaw</a></summary>

# CoPaw Project Digest: 2026-06-07

## 1. Today's Overview
CoPaw is currently experiencing a moderate level of community activity, with 6 open issues and 1 active pull request updated in the last 24 hours, but zero closures or merges. The lack of new releases contrasts with a noticeable uptick in bug reports related to the recent v1.1.10 release, indicating a post-release stabilization phase. Users are actively testing local model integrations and pushing for architectural enhancements, such as vision model fallbacks and broader channel support. Overall, project health remains active, though maintainers need to address emerging regressions in local LLM connectivity.

## 2. Releases
No new releases were recorded today.

## 3. Project Progress
No PRs were merged or closed today. The only active PR, [#4949](https://github.com/agentscope-ai/QwenPaw/pull/4949), is currently under review and represents a significant additive feature: extending the Agent Client Protocol (ACP) server. This PR aims to allow ACP clients (like terminal UIs) to receive essential metadata—such as advertised commands, tool parameters, agent/model metadata, and file links—paving the way for richer third-party client integrations.

## 4. Community Hot Topics
The most actively discussed issues center around model compatibility and platform expansion:
*   **Local Model Regression ([#4989](https://github.com/agentscope-ai/QwenPaw/issues/4989))**: With 2 comments, this issue highlights a critical regression where local vLLM deployments (Qwen 3.6-27B) fail in versions 1.1.9/1.1.10 despite working perfectly in 1.1.5.post2. This suggests a breaking change in how the platform handles standard OpenAI API protocols in recent updates.
*   **Regional Channel Demand ([#4886](https://github.com/agentscope-ai/QwenPaw/issues/4886))**: Also with 2 comments, users are advocating for the addition of MAX Messenger, a highly relevant platform for Russian-speaking users. This aligns with CoPaw’s "Every channel" vision and underscores a localized need for broader messaging platform support.

## 5. Bugs & Stability
Several bugs were reported today, primarily affecting the v1.1.10 release, ranked by severity:
1.  **High Severity - Local LLM Unresponsiveness**: [#4989](https://github.com/agentscope-ai/QwenPaw/issues/4989) - Chat interface enters an infinite loading state ("spinning dots") with no backend logs when using local vLLM models. This is a regression from v1.1.5.post2. No fix PR is currently open.
2.  **Medium Severity - WeCom Tool Call Failure**: [#4990](https://github.com/agentscope-ai/QwenPaw/issues/4990) - Enterprise WeChat (WeCom) channel returns a fallback error message ("Sorry, I cannot answer...") when tool invocation information is closed/hidden. No fix PR is currently open.
3.  **Low Severity - UI Jitter**: [#4993](https://github.com/agentscope-ai/QwenPaw/issues/4993) - Image preview shakes abnormally when zoomed and dragged on macOS. No fix PR is currently open.

## 6. Feature Requests & Roadmap Signals
*   **Visual Model Fallback Architecture**: [#4992](https://github.com/agentscope-ai/QwenPaw/issues/4992) requests a `visual_model` configuration. This would allow text-only main models (e.g., deepseek-v4-flash) to automatically route images to a separate vision model, converting the image to text before passing it back to the main model. This "visual relay station" concept is a strong signal for modular, multi-model routing and is a highly likely candidate for future roadmap inclusion given the rise of specialized text-only models.
*   **MAX Messenger Integration**: [#4886](https://github.com/agentscope-ai/QwenPaw/issues/4886) continues the community's consistent demand for wider messaging channel integrations.

## 7. User Feedback Summary
*   **Pain Points**: Users are frustrated by silent failures, particularly the infinite loading bug with local models ([#4989](https://github.com/agentscope-ai/QwenPaw/issues/4989)) where the absence of backend logs makes debugging difficult. Enterprise channel users are also experiencing clunky error handling when tool calls are restricted ([#4990](https://github.com/agentscope-ai/QwenPaw/issues/4990)).
*   **Use Cases**: The community is actively using CoPaw for air-gapped/local deployments via vLLM, enterprise deployments via WeCom, and seeks to mix-and-match specialized text LLMs with vision models for cost-effective multimodal workflows.

## 8. Backlog Watch
*   **PR [#4949](https://github.com/agentscope-ai/QwenPaw/pull/4949)**: Open since June 3rd, this ACP enhancement PR is awaiting maintainer review. Merging this is crucial for advancing the project's ecosystem and third-party client support.
*   **Issue [#4989](https://github.com/agentscope-ai/QwenPaw/issues/4989)**: As a high-severity regression affecting core chat functionality for local model users, this issue requires urgent maintainer triage to identify what changed between v1.1.5.post2 and v1.1.9 regarding OpenAI API compatibility.

</details>

<details>
<summary><strong>ZeptoClaw</strong> — <a href="https://github.com/qhkm/zeptoclaw">qhkm/zeptoclaw</a></summary>

No activity in the last 24 hours.

</details>

<details>
<summary><strong>ZeroClaw</strong> — <a href="https://github.com/zeroclaw-labs/zeroclaw">zeroclaw-labs/zeroclaw</a></summary>

# ZeroClaw Project Digest — 2026-06-07

## 1. Today's Overview
ZeroClaw exhibited high development activity over the past 24 hours, with 42 issues updated (18 closed) and 50 pull requests updated (19 merged/closed). The project's momentum is heavily concentrated on hardening the agent's security boundaries (tool execution, token rotation, and session management) and significantly improving the TUI (`zerocode`) resilience and UX. While no new version was released today, maintainers are actively clearing P1 and S0/S1 blockers for the upcoming `v0.8.0` milestone, signaling an imminent stabilization phase. 

## 2. Releases
No new releases were published today.

## 3. Project Progress
Substantial progress was made in resolving critical stability and security issues, alongside significant enhancements to the user experience:
*   **TUI Reconnection & Stability:** The critical TUI freezing issue when the daemon disconnects ([#7043](https://github.com/zeroclaw-labs/zeroclaw/issues/7043), [#7125](https://github.com/zeroclaw-labs/zeroclaw/issues/7125)) was resolved via [PR #7158](https://github.com/zeroclaw-labs/zeroclaw/pull/7158), which implements graceful in-loop daemon reconnect and recovery.
*   **Security Fixes:** [PR #6988](https://github.com/zeroclaw-labs/zeroclaw/pull/6988) fixed the S0 token rotation bug ([#6984](https://github.com/zeroclaw-labs/zeroclaw/issues/6984)) by properly invalidating bearer tokens on device rotate/delete. [PR #7177](https://github.com/zeroclaw-labs/zeroclaw/pull/7177) closed a security gap by forcing memory-tool exclusion for ACP sessions server-side.
*   **Provider & Runtime Correctness:** [PR #7180](https://github.com/zeroclaw-labs/zeroclaw/pull/7180) fixed custom/openai-compatible providers failing when `wire_api = "responses"` was set. [PR #7242](https://github.com/zeroclaw-labs/zeroclaw/pull/7242) fixed path-policy false positives for tilde tokens in quoted/heredoc commands ([#7133](https://github.com/zeroclaw-labs/zeroclaw/issues/7133)).
*   **TUI UX Advancements:** Several major feature PRs are in the pipeline for `zerocode`, including an outbound message queue with sidebar ([PR #7190](https://github.com/zeroclaw-labs/zeroclaw/pull/7190)), a live model/provider picker ([PR #7209](https://github.com/zeroclaw-labs/zeroclaw/pull/7209)), and theme enhancements for terminal compatibility ([PR #7249](https://github.com/zeroclaw-labs/zeroclaw/pull/7249)).

## 4. Community Hot Topics
*   **Subscription-Native OAuth ([#5601](https://github.com/zeroclaw-labs/zeroclaw/issues/5601)):** With 7 comments, this is the most discussed issue. Users strongly desire the ability to authenticate with providers (Ollama Cloud, z.ai, Kimi, MiniMax) using subscription logins rather than managing static API keys. The issue remains `blocked`, likely dependent on provider API support or complex architecture changes.
*   **Agent Tool Security Scoping ([#6915](https://github.com/zeroclaw-labs/zeroclaw/issues/6915), [#6914](https://github.com/zeroclaw-labs/zeroclaw/issues/6914)):** A highly debated topic (3 comments each) is how to enforce `allowed_tools`/`denied_tools` and allow temporary elevation for skill execution. The community is actively trying to balance secure defaults with the flexibility needed for Composio/MCP tooling.
*   **WASM Plugin Lifecycle ([#7338](https://github.com/zeroclaw-labs/zeroclaw/issues/7338), [#7339](https://github.com/zeroclaw-labs/zeroclaw/issues/7339)):** Freshly opened today, these RFCs propose bridging ZeroClaw's existing `HookRunner` events to WASM plugins. This indicates strong community interest in a more extensible, sandboxed plugin architecture.

## 5. Bugs & Stability
*   **S0 - Data Loss / Security Risk:**
    *   [Bug #7252](https://github.com/zeroclaw-labs/zeroclaw/issues/7252) [CLOSED]: Killed ACP sessions could be rehydrated from durable history. 
    *   [Bug #6978](https://github.com/zeroclaw-labs/zeroclaw/issues/6978) [CLOSED]: Nested secrets in object-array properties were not redacted. 
*   **S1 - Workflow Blocked:**
    *   [Bug #7043](https://github.com/zeroclaw-labs/zeroclaw/issues/7043) & [Bug #7125](https://github.com/zeroclaw-labs/zeroclaw/issues/7125) [CLOSED]: TUI freezing permanently on daemon disconnect. Fixed by [PR #7158](https://github.com/zeroclaw-labs/zeroclaw/pull/7158).
    *   [Bug #6984](https://github.com/zeroclaw-labs/zeroclaw/issues/6984) [CLOSED]: Token rotation failed to revoke existing bearer tokens. Fixed by [PR #6988](https://github.com/zeroclaw-labs/zeroclaw/pull/6988).
    *   [Bug #6875](https://github.com/zeroclaw-labs/zeroclaw/issues/6875) [CLOSED]: Llama 4 Scout models fail silently because the tool call parser doesn't handle `<tool_calls>` (plural) tags.
*   **S2 - Degraded Behavior:**
    *   [Bug #7133](https://github.com/zeroclaw-labs/zeroclaw/issues/7133) [CLOSED]: Path-policy false-positive on `~` tokens in quoted/heredoc command data. Fixed by [PR #7242](https://github.com/zeroclaw-labs/zeroclaw/pull/7242).
    *   [Bug #7332](https://github.com/zeroclaw-labs/zeroclaw/issues/7332) [CLOSED]: Telegram partial streaming floods edits when `draft_update_interval_ms` is set to 0.
    *   [Bug #7059](https://github.com/zeroclaw-labs/zeroclaw/issues/7059) [CLOSED]: "Default model provider" fallback causing degraded behavior. Addressed via [PR #7178](https://github.com/zeroclaw-labs/zeroclaw/pull/7178).

## 6. Feature Requests & Roadmap Signals
*   **Azure Identity-Based Auth ([#7342](https://github.com/zeroclaw-labs/zeroclaw/issues/7342)):** Requested today, users want Entra ID / managed identity support for Azure OpenAI to comply with enterprise security policies that forbid API keys.
*   **Cron Pre-Hook Skip Gates ([#5607](https://github.com/zeroclaw-labs/zeroclaw/issues/5607)):** Proposed lightweight precondition checks for cron jobs and SOP triggers to skip execution gracefully (exit code 10) if conditions aren't met.
*   **Roadmap Signals:** The active tracker [#7112](https://github.com/zeroclaw-labs/zeroclaw/issues/7112) confirms `v0.8.0` is in a hardening phase, focusing on schema/config breaking changes and tool-call parser stabilization. The introduction of WASM RFCs ([#7338](https://github.com/zeroclaw-labs/zeroclaw/issues/7338)) and per-alias model fallbacks ([PR #7178](https://github.com/zeroclaw-labs/zeroclaw/pull/7178)) signals that `v0.8.x` will likely pivot toward advanced extensibility and enterprise-ready provider resilience.

## 7. User Feedback Summary
*   **Pain Points:** Users are frustrated by global permission scopes (e.g., enabling `python3` for one skill opens it for all), indicating that granular security is a pressing operational need. TUI disconnections causing hard freezes was a major usability blocker that the community is relieved to see patched. Additionally, Llama 4 compatibility issues with tool calls have caused silent failures for users relying on open-source models.
*   **Use Cases:** Operators are deploying ZeroClaw in complex channel setups (WhatsApp, Telegram, Discord) and need per-channel throttling ([#6345](https://github.com/zeroclaw-labs/zeroclaw/issues/6345)) and better streaming controls to prevent API bans or user spam. Enterprise users are constrained by cloud provider security policies requiring identity-based auth over static keys.
*   **Satisfaction:** The project is highly responsive to S0/S1 bugs, with quick turnarounds on critical security and stability fixes. However, some high-value architectural features (like OAuth and skill-scoped permissions) remain `blocked`, causing slight friction for users wanting to productionize multi-tenant or consumer-facing agents.

## 8. Backlog Watch
*   [Issue #5601](https://github.com/zeroclaw-labs/zeroclaw/issues/5601) (OAuth Support): Open since April 2026, this `blocked` P2 issue has high community demand (7 comments, 1 thumbs-up) but seems stuck on architectural decisions or provider limitations.
*   [Issue #6914](https://github.com/zeroclaw-labs/zeroclaw/issues/6914) (Enforce allowed/denied tools): A `blocked` P1 issue that is crucial for agent security. Without enforcement at execution time, the `allowed_tools` config provides a false sense of security.
*   [Issue #5908](https://github.com/zeroclaw-labs/zeroclaw/issues/5908) (CI/CD Debian Container Images): A `blocked` P2 issue open since April. Automated container releases are a standard expectation for deployment, and the lack of CI/CD here hampers adoption for Docker/Nix-based workflows.
*   [Issue #6715](https://github.com/zeroclaw-labs/zeroclaw/issues/6715) (Delete unneeded branches): A low-effort, medium-risk housekeeping issue open since May with 4 comments, awaiting maintainer action to clean up 200+ stale branches.

</details>

---
*This digest is auto-generated by [agents-radar](https://github.com/yanzi6039/agents-radar).*