# AI CLI Tools Community Digest 2026-06-07

> Generated: 2026-06-07 14:39 UTC | Tools covered: 9

- [Claude Code](https://github.com/anthropics/claude-code)
- [OpenAI Codex](https://github.com/openai/codex)
- [Gemini CLI](https://github.com/google-gemini/gemini-cli)
- [GitHub Copilot CLI](https://github.com/github/copilot-cli)
- [Kimi Code CLI](https://github.com/MoonshotAI/kimi-cli)
- [OpenCode](https://github.com/anomalyco/opencode)
- [Pi](https://github.com/badlogic/pi-mono)
- [Qwen Code](https://github.com/QwenLM/qwen-code)
- [DeepSeek TUI](https://github.com/Hmbown/DeepSeek-TUI)
- [Claude Code Skills](https://github.com/anthropics/skills)

---

## Cross-Tool Comparison

## 1. Ecosystem Overview
The AI CLI tools ecosystem is in a phase of rapid, turbulent scaling, driven by the integration of advanced frontier models (e.g., GPT-5.5, Claude Opus 4.8) and agentic architectures. As tools shift from simple assistants to autonomous agents, context management—specifically compaction and long-session memory—has emerged as the primary architectural bottleneck across the board. Platform stability, particularly on Windows/WSL, remains a significant friction point, while security concerns are escalating from basic sandboxing to preventing credential leaks and runaway token spend in sub-agent workflows.

## 2. Activity Comparison

| Tool | Hot Issues Tracked | Key PRs Updated | Release Status (Jun 7) |
| :--- | :--- | :--- | :--- |
| **Claude Code** | 10 | 5 | **v2.1.168** released |
| **OpenAI Codex** | 10 | 10 | No release |
| **Gemini CLI** | 10 | 10 | No release |
| **GitHub Copilot CLI** | 10 | 1 | No release |
| **OpenCode** | 10 | 10 | No release |
| **Pi** | 10 | 3 | No release |
| **Qwen Code** | 10 | 10 | **v0.17.1-nightly** released |
| **DeepSeek TUI** | 6 | 10 | No release |
| **Kimi Code CLI** | 3 | 2 | No release |

## 3. Shared Feature Directions

*   **Context Compaction & Preservation:** The most universal pain point. Long sessions degrade as compaction fails, loops infinitely, or silently discards critical context. *Tools: OpenCode (compaction loops, Anthropic API boundary violations), Claude Code (auto-compact regressions), Pi (auto-compact crashes), Qwen Code (context amnesia on interrupt), OpenAI Codex (requests for context pins).*
*   **Cost & Resource Guardrails for Agents:** Unfettered sub-agent spawning is causing bill shocks and resource exhaustion. Users need spend caps and resource gates. *Tools: Claude Code (3M token sub-agent spawn, token-burning retries), OpenAI Codex (137GB disk writes), OpenCode (multi-account OAuth for rate limits).*
*   **Windows/WSL Platform Parity:** Windows environments are experiencing severe regressions compared to Unix/macOS. *Tools: GitHub Copilot CLI (215% CPU idle spin, 80s startup delay), OpenAI Codex (UAC crashes, hardcoded PowerShell), Claude Code (tool-call parse failures), Qwen Code (SMB path errors), OpenCode (LF line-ending bugs).*
*   **Security & Sandboxing:** Demand for default-deny boundaries to prevent credential leaks, malicious command injection, and unsafe file writes. *Tools: Claude Code (credential leak prevention), OpenCode (sandboxing requests), Gemini CLI (command injection fixes, memory redaction), DeepSeek TUI (execution policy bypasses), Pi (workspace approval for extensions).*
*   **Multi-Model/Provider Agility:** Rigid single-model sessions are frustrating users who want local/cloud fallbacks and BYOK. *Tools: GitHub Copilot CLI (BYOK mid-session switching), Qwen Code (fallback routing, shared baseUrls), DeepSeek TUI (HuggingFace route polish).*

## 4. Differentiation Analysis

*   **Claude Code** is uniquely pressured by the push for cross-tool standardization (the massive `AGENTS.md` feature request), while its technical differentiators are currently liabilities—macOS kernel memory leaks and unchecked Opus sub-agent costs.
*   **OpenAI Codex** is focused on deep architectural refactoring (global instructions, typed cross-platform path URIs) to stabilize a fragile event loop, but is struggling to tame GPT-5.5 integration regressions (misreported context windows, Bedrock disconnects).
*   **Gemini CLI** is prioritizing code-intelligence depth, pushing for AST-aware file reads and robust component-level behavioral evals, alongside crucial security patches for command injection and regex stack overflows.
*   **GitHub Copilot CLI** differentiates via lifecycle customization (hooks like `awaitingUserInput`), but currently suffers from critical platform isolation (WSL2) and MCP OAuth fan-out issues, with near-zero recent meaningful PR activity.
*   **OpenCode** is battling provider-interop fragility (specifically Anthropic extended thinking API edge cases) and a trust crisis around its ZEN payment routing, though its contributor community is highly active in targeted bug fixing.
*   **Pi** is heavily extension-driven, with developers demanding public API surface area (RPC types, session hooks) to overcome internal encapsulation, while implementing strict workspace approval systems for loading `.pi` extensions.
*   **Qwen Code** is aggressively expanding its daemon/ACP (Agent Client Protocol) architecture, merging massive feature batches (+115k LOC) to enable remote programmatic control, though it struggles with Ink-based TUI rendering flickers.
*   **DeepSeek TUI** is undergoing a foundational architectural shift from a monolithic match pattern to a modular strategy pattern, prioritizing technical debt reduction and Gherkin E2E test coverage over flashy feature releases.

## 5. Community Momentum & Maturity

*   **Rapid Iterators (High Momentum):** Qwen Code, DeepSeek TUI, Gemini CLI, and OpenCode show the highest velocity in PR merges and architectural evolution. Qwen Code's massive daemon integration and DeepSeek TUI's systematic 30+ bug sweep indicate healthy, aggressive development cycles.
*   **Mature but Stagnant/Friction-Heavy:** Claude Code and GitHub Copilot CLI show signs of scaling bottlenecks. Claude Code has the highest upvoted issue (4,074 👍 for AGENTS.md) with zero official response, and Copilot CLI is suffering from severe WSL2 regressions with only spam PR activity visible.
*   **Ecosystem Turmoil:** Kimi Code CLI is experiencing a community trust deficit due to a hard pivot from `kimi-cli` to `kimi-code`, resulting in fragmented migration paths and low issue/PR volume.

## 6. Trend Signals

1.  **AGENTS.md as the New `.env`:** The overwhelming demand for `AGENTS.md` in Claude Code signals that the industry rejects vendor-locked configuration (like `CLAUDE.md`). Standardized, cross-agent instruction files will become the baseline expectation for any enterprise-ready AI tool.
2.  **Compaction is the New Context Window:** As models scale to 1M+ tokens, the challenge is no longer just fitting context, but safely compressing it. API contract violations during compaction (e.g., missing user boundaries with Anthropic, or false success statuses in Gemini) are the new frontier for reliability engineering.
3.  **The Daemonization of CLIs:** The shift from interactive TUIs to daemonized, ACP/REST-capable servers (led by Qwen Code and OpenAI Codex) indicates that AI CLIs are becoming orchestration layers for remote and programmatic agent fleets, not just developer chat interfaces.
4.  **Cost Gates over Capability:** The era of "run until completion" is ending. Community feedback demands hard defaults for cost confirmation (Claude Code's sub-agents) and resource caps (Codex's 137GB disk leak). Future tools must treat token-spend and system-resource limits as first-class security boundaries.
5.  **AST > Grep for Context Retrieval:** Gemini CLI's exploration of AST-aware tools suggests the next leap in token efficiency. Raw string search and file reads generate massive token noise; syntax-aware retrieval will become essential for keeping agentic context windows lean and accurate.

---

## Per-Tool Reports

<details>
<summary><strong>Claude Code</strong> — <a href="https://github.com/anthropics/claude-code">anthropics/claude-code</a></summary>

## Claude Code Skills Highlights

> Source: [anthropics/skills](https://github.com/anthropics/skills)

# Claude Code Skills Community Highlights Report (As of 2026-06-07)

## 1. Top Skills Ranking
Based on community attention and discussion volume, here are the most-watched Skills PRs:

*   **[Add document-typography skill](https://github.com/anthropics/skills/pull/514)** (#514, OPEN): Proposes typographic quality control for AI-generated documents (preventing orphans, widows, and numbering misalignment). Addresses a universal pain point in LLM-generated outputs.
*   **[Add ODT skill](https://github.com/anthropics/skills/pull/486)** (#486, OPEN): Introduces comprehensive OpenDocument Format (.odt, .ods) creation, template filling, and ODT-to-HTML conversion, fulfilling a major gap in open-source document standards.
*   **[Improve frontend-design skill](https://github.com/anthropics/skills/pull/210)** (#210, OPEN): A significant revision of the existing frontend-design skill to improve clarity and actionability, ensuring instructions are immediately executable by Claude within a single context window.
*   **[Add skill-quality-analyzer and skill-security-analyzer](https://github.com/anthropics/skills/pull/83)** (#83, OPEN): Introduces two vital "meta-skills" for the ecosystem—a quality analyzer (evaluating structure, documentation) and a security analyzer—to help developers harden their own skills.
*   **[Implement agent-creator skill](https://github.com/anthropics/skills/pull/1140)** (#1140, OPEN): Adds a meta-skill for generating task-specific agent sets, while also shipping critical fixes for multi-tool evaluation and Windows compatibility in `evaluation.py`.
*   **[Add AURELION skill suite](https://github.com/anthropics/skills/pull/444)** (#444, OPEN): Proposes a structured cognitive and memory framework (kernel, advisor, agent, memory) for professional knowledge management and persistent context across conversations.
*   **[Add 2 community skills: n8n-builder, n8n-debugger](https://github.com/anthropics/skills/pull/190)** (#190, OPEN): Delivers production-tested skills for building and debugging n8n automation workflows, reflecting strong demand for AI-assisted automation.
*   **[Fix feature-dev workflow phases](https://github.com/anthropics/skills/pull/363)** (#363, OPEN): Fixes a critical `TodoWrite` overwrite bug in the `feature-dev` skill that caused quality review and summary phases to be skipped.

## 2. Community Demand Trends
Analysis of top Issues reveals clear demand vectors for the Skills ecosystem:

*   **Enterprise & Team Collaboration:** The highest-voted issue ([#228](https://github.com/anthropics/skills/issues/228), 👍7) demands org-wide skill sharing. Users want shared libraries or direct links instead of manually passing `.skill` files via Slack/Teams.
*   **Reliable Skill Invocation & Evaluation:** A critical bug causing a 0% trigger rate in `run_eval.py` ([#556](https://github.com/anthropics/skills/issues/556)) highlights the community's need for a robust evaluation framework and reliable skill-triggering mechanics.
*   **Context Window Optimization:** Multiple issues point to context bloat as a major bottleneck. Duplicate skills loading identical content ([#189](https://github.com/anthropics/skills/issues/189), 👍8), MCP returning uncompressed/excess data ([#1102](https://github.com/anthropics/skills/issues/1102)), and the inability to selectively preload multi-file references ([#1220](https://github.com/anthropics/skills/issues/1220)) all demand smarter context management.
*   **Security & Governance:** Namespace trust boundary abuse ([#492](https://github.com/anthropics/skills/issues/492)) and proposals for agent governance patterns ([#412](https://github.com/anthropics/skills/issues/412)) indicate rising concern over distinguishing official vs. community skills and enforcing safety boundaries.
*   **MCP Interoperability:** Exposing Skills as MCPs ([#16](https://github.com/anthropics/skills/issues/16)) remains an ongoing conversation, with the community pushing for standardized APIs to bridge skills and external tools.

## 3. High-Potential Pending Skills
These active PRs address immediate technical gaps and are strong candidates for merging in the near term:

*   **[fix(pdf): correct case-sensitive file references](https://github.com/anthropics/skills/pull/538)** (#538): A straightforward but critical fix for case-sensitivity mismatches in the PDF skill that breaks usage on Linux/macOS.
*   **[fix(docx): prevent tracked change w:id collision](https://github.com/anthropics/skills/pull/541)** (#541): Resolves document corruption in the DOCX skill when adding tracked changes alongside existing bookmarks.
*   **[fix(skill-creator): warn on unquoted description with YAML special characters](https://github.com/anthropics/skills/pull/539)** (#539): Improves the skill creation validation pipeline by catching YAML parsing failures early.
*   **[skill-creator: fix Windows subprocess + encoding bugs](https://github.com/anthropics/skills/pull/1050)** (#1050): Essential 1-line fixes for Windows `PATHEXT` and encoding issues that currently break local skill evaluation on Windows 11.
*   **[feat: add testing-patterns skill](https://github.com/anthropics/skills/pull/723)** (#723): A comprehensive skill covering the full testing stack (Unit, React, Integration, E2E), addressing a core developer workflow.

## 4. Skills Ecosystem Insight
The community's most concentrated demand is for **reliable skill invocation/evaluation frameworks and robust context management (preventing bloat and enabling selective referencing), closely followed by enterprise-grade features like org-sharing and namespace security governance.**

---

# Claude Code Community Digest — 2026-06-07

## 1. Today's Highlights

Version **v2.1.168** landed with bug fixes and reliability improvements, but the community's attention is dominated by a cluster of **auto-compact regressions** reported across multiple provider configurations and a **macOS kernel memory leak** that scales dangerously with agent load. Meanwhile, the long-running **AGENTS.md feature request** continues to accumulate support (now 4,074 👍), making it the most-upvoted open issue by a wide margin.

## 2. Releases

- **[v2.1.168](https://github.com/anthropics/claude-code/releases/tag/v2.1.168)** — Bug fixes and reliability improvements. No detailed changelog published.

## 3. Hot Issues

| # | Issue | Why It Matters | Community Reaction |
|---|-------|---------------|-------------------|
| 1 | [**#6235** — Support AGENTS.md](https://github.com/anthropics/claude-code/issues/6235) | The industry is converging on `AGENTS.md` as a cross-agent standard; `CLAUDE.md` locks teams into one tool. | **4,074 👍 / 319 comments** — the single most-upvoted open issue. Zero official response to date. |
| 2 | [**#60366** — "Hi" flagged as policy violation](https://github.com/anthropics/claude-code/issues/60366) | Safety-filter false positives block benign conversation, eroding trust in the guardrail system. | 76 comments, users sharing similar triggers; no fix yet. |
| 3 | [**#60334** — Image processing failures burn tokens](https://github.com/anthropics/claude-code/issues/60334) | Repeated API errors on phantom images consumed ~70% of a user's 5-hour rate window — a direct cost problem. | 55 comments, 14 👍; closed but cost-recovery concerns linger. |
| 4 | [**#63875** — Recurring "tool call could not be parsed" errors](https://github.com/anthropics/claude-code/issues/63875) | Unrecoverable parse failures abort in-progress work on Windows, making long sessions unreliable. | 49 comments, **70 👍**; labeled `duplicate` but still open with no resolution. |
| 5 | [**#63670** — Up/Down arrow regression in multi-line input](https://github.com/anthropics/claude-code/issues/63670) | A TUI regression (~v2.1.15) makes editing wrapped prompts infuriating — arrows jump to history instead of moving the cursor. | 14 comments, has repro; cross-platform. |
| 6 | [**#66023** — Workflow spawned 46 Opus subagents, ~3M tokens, no confirmation](https://github.com/anthropics/claude-code/issues/66023) | A single workflow invocation ran up massive token spend with zero cost gate — a critical cost-control gap for agentic features. | 5 comments; filed today, likely to gain traction. |
| 7 | [**#66020** — macOS kernel zone leak scales with agent load](https://github.com/anthropics/claude-code/issues/66020) | `data.kalloc.1024` leak rate jumps from 21→1,027/sec under agent load; CLI panics at ~20 GB. Serious for power users. | 4 comments, has repro; freshly filed. |
| 8 | [**#65585** / [**#66022**] — Auto-compact regression for third-party & first-party providers](https://github.com/anthropics/claude-code/issues/65585) | Auto-compact stopped triggering at the expected ~168K token boundary, causing sessions to hit the 1M limit and fail. Two independent reports confirm the regression. | Together 5 comments; both tagged `regression`. |
| 9 | [**#66044** — No built-in credential-leak prevention](https://github.com/anthropics/claude-code/issues/66044) | Vanilla Claude Code will read private keys and credential files straight into the transcript — a security blind spot, especially in agentic workflows. | 2 comments; security-minded developers are concerned. |
| 10 | [**#65697** — Official Linux desktop build request](https://github.com/anthropics/claude-code/issues/65697) | No official Debian/Ubuntu build exists; Linux users rely on the CLI or unsupported workarounds. | **65 👍** in 2 days — fast growth. |

## 4. Key PR Progress

Only 5 PRs saw updates in the last 24 hours — activity is light.

| PR | Description | Status |
|----|-------------|--------|
| [**#65919**](https://github.com/anthropics/claude-code/pull/65919) | **docs: document `${CLAUDE_PLUGIN_ROOT}` limitation in subagents** — Subagents receive unresolved literal strings instead of expanded paths (affects ≤ 2.1.166). Adds a Known Limitations section with a resolution matrix. | Open |
| [**#65916**](https://github.com/anthropics/claude-code/pull/65916) | **docs: clarify `allowed-tools` vs `tools:` enforcement** — `allowed-tools` is auto-approval only (tools still callable with user prompt); `tools:` in subagent frontmatter is a hard boundary. Important behavioral distinction for plugin authors. | Open |
| [**#39370**](https://github.com/anthropics/claude-code/pull/39370) | **feat: frontend-design-system plugin** — Adds a plugin generating design specs (wireframes, OKLCH color theory, design tokens) before code. | Closed |
| [**#65666**](https://github.com/anthropics/claude-code/pull/65666) | **Fix dev container issues** — Resolves DNS firewall blocks and missing API-key injection in devcontainer builds. | Closed (merged) |
| [**#58673**](https://github.com/anthropics/claude-code/pull/58673) | Single-character title ("s"), no meaningful description. Likely spam. | Open |

## 5. Feature Request Trends

1. **AGENTS.md support** — By far the dominant request. Multiple duplicate issues (#31005 with 198 👍, others) all point to the same need: an agent-agnostic configuration standard. The community has been waiting since August 2025 with no official acknowledgment.

2. **Cost transparency & guardrails for agentic workflows** — Subagent spawning (#66023), token-burning retries (#60334), and missing cost confirmations indicate that agentic features need spending controls before they're production-ready.

3. **Built-in secret/credential leak prevention** — Native, default-on protection against reading sensitive files into the conversation context (#66044), especially critical as subagents roam the filesystem autonomously.

4. **Linux desktop support** — A fast-growing request (#65697) driven by the gap between the CLI (cross-platform) and the desktop app (macOS/Windows only).

5. **Per-agent context-window visibility** — Requests to surface token usage per background agent (#65792) to help users manage fleet-level spend and pre-empt compaction failures.

## 6. Developer Pain Points

- **Auto-compact regressions are causing session failures.** Multiple reports (#65585, #66022, #63197) confirm that compaction no longer triggers reliably for both first-party and third-party API providers, leading to hard context-limit crashes. This is a top reliability concern.

- **Cost control in agentic mode is insufficient.** Users are discovering that a single workflow invocation can spawn dozens of Opus subagents consuming millions of tokens with no confirmation gate. Combined with image-processing token waste, cost predictability is the #1 practical frustration.

- **Safety-filter false positives are blocking normal use.** The usage-policy filter triggers on benign prompts like "hi" (#60366) and biological-research terms (#66052), with no clear recourse or tuning mechanism.

- **TUI regressions degrade daily workflow.** The arrow-key bug (#63670) and vanishing permission prompts (#60194) make the interactive experience unreliable, especially during long sessions.

- **Platform-specific stability gaps on Windows and macOS.** Tool-call parse failures on Windows (#63875) and a kernel-level memory leak on macOS (#66020) disproportionately affect power users running heavy agent workloads.

</details>

<details>
<summary><strong>OpenAI Codex</strong> — <a href="https://github.com/openai/codex">openai/codex</a></summary>

## OpenAI Codex Community Digest — 2026-06-07

### 1. Today's Highlights
The Codex community is currently grappling with severe GPT-5.5 integration issues and critical Windows desktop bugs, while the core team pushes a significant architectural refactor of global instructions and connection handling. A critical regression causing GPT-5.5 to report a 2432-token context window has been closed, but users are still reporting model degradation and Bedrock disconnects. Meanwhile, several high-impact PRs merged today address SQLite corruption recovery and TUI deadlocks.

### 2. Releases
No new releases in the last 24 hours.

### 3. Hot Issues
1. **[#6465](https://github.com/openai/codex/issues/6465) [bug, extension, mcp] MCP servers not detected in VS Code extension**: A long-standing friction point where MCP servers work perfectly in the CLI but fail to be detected by the VS Code extension. High community engagement (57 comments, 30 👍) indicates this is a major blocker for extension-reliant workflows.
2. **[#18960](https://github.com/openai/codex/issues/18960) [bug, connectivity] Frequent reconnect loop in Codex App**: Users are hitting streaming failures where websockets are closed by the server before completion, causing 5-7 second reconnect loops. With 36 comments, this remains a top reliability concern for App users.
3. **[#26783](https://github.com/openai/codex/issues/26783) [bug, rate-limits, CLI, context] gpt-5.5 reports 2432-token context window**: A critical regression in CLI v0.137.0 where GPT-5.5 falsely reports a tiny context window, triggering infinite auto-compaction loops and rapidly draining usage limits. Now closed, likely via API/backend fixes.
4. **[#26843](https://github.com/openai/codex/issues/26843) [bug, app, performance] 137GB disk writes and macOS hard reboot**: A severe resource leak where a long-running Codex Desktop session wrote 137GB to disk, overwhelming WindowServer and forcing a hard system reboot.
5. **[#25319](https://github.com/openai/codex/issues/25319) [enhancement, session] Scope VS Code chats to workspace**: Users are frustrated that chat history leaks across projects. This feature request asks for workspace-scoped context isolation and has gained strong traction (18 👍).
6. **[#16717](https://github.com/openai/codex/issues/16717) [enhancement, windows-os] Configurable Windows agent shell**: Windows users are suffering because Codex hardcodes PowerShell, leading to worse model command generation compared to Bash. The community proposes a `[windows].agent_shell` config key (7 👍).
7. **[#26889](https://github.com/openai/codex/issues/26889) [design] Thread context pins for compaction**: An architectural proposal to preserve critical context across `/compact` or auto-compaction events, addressing the widespread pain of long-running sessions "forgetting" key constraints.
8. **[#26530](https://github.com/openai/codex/issues/26530) [bug, windows-os, mcp, sandbox] Windows UAC crash with Computer Use**: Enabling Computer Use on Windows regenerates a `node_repl` MCP config that crashes instantly due to `UAC ERROR_ELEVATION_REQUIRED`, blocking key desktop features.
9. **[#26860](https://github.com/openai/codex/issues/26860) [bug, custom-model, connectivity] GPT-5.5 via Bedrock stops mid-task**: Custom provider regression where GPT-5.5 xhigh silently stops during execution via Amazon Bedrock, while GPT-5.4 xhigh works flawlessly.
10. **[#26876](https://github.com/openai/codex/issues/26876) [bug, model-behavior] GPT 5.5 degradation over time**: Users are noticing a material degradation in GPT-5.5's performance on complex engineering workflows over sustained sessions, separate from context compaction issues.

### 4. Key PR Progress
1. **[#26852](https://github.com/openai/codex/pull/26852) fix(app-server): avoid blocking connection cleanup**: Directly addresses the reconnect loops in Issue #18960. It stops stuck RPCs from blocking the processing of replacement connections when the event queue fills up.
2. **[#26859](https://github.com/openai/codex/pull/26859) fix: Auto-recover from corrupted sqlite databases**: Handles recent SQLite corruption issues by automatically recovering from irreparably corrupted root databases, rather than hard-failing.
3. **[#26880](https://github.com/openai/codex/pull/26880) preserve fsmonitor for worktree Git reads**: Fixes a performance regression where Codex disabled `core.fsmonitor` for internal Git commands, causing painfully slow full scans in large repositories.
4. **[#26831](https://github.com/openai/codex/pull/26831) Add global instructions contributor API**: Introduces an extension point for hosts to supply global instructions, decoupling them from core `Config` loading.
5. **[#26832](https://github.com/openai/codex/pull/26832) Add CODEX_HOME instructions contributor**: Moves `CODEX_HOME` instruction discovery out of `codex-core` into a dedicated crate, part of the broader global instructions refactor.
6. **[#26833](https://github.com/openai/codex/pull/26833) Persist structured instruction snapshots**: Ensures history-sharing threads retain the exact instructions active when their history was produced, fixing compaction/fork consistency.
7. **[#26834](https://github.com/openai/codex/pull/26834) Adopt global instructions contributors**: Completes the migration of global instruction loading out of core `Config`, allowing hosts to choose their contributor and prevent implicit state during thread creation.
8. **[#26839](https://github.com/openai/codex/pull/26839) Block project config permission overrides**: A security fix (BUGB 15876) that prevents project-level configs from overriding critical security boundaries like sandbox mode and approval policies.
9. **[#26754](https://github.com/openai/codex/pull/26754) Prepare side threads off the TUI event loop**: Fixes a deadlock in the TUI where spawning a `/side` conversation during heavy event generation would freeze the interface.
10. **[#26840](https://github.com/openai/codex/pull/26840) Add typed cross-platform path URIs**: Introduces stable, opaque path identifiers that work across local and remote environments without misinterpreting foreign path syntax.

### 5. Feature Request Trends
- **Context Preservation & Isolation**: There is a strong push for finer control over context windows. Users want workspace-scoped chats (#25319) and the ability to "pin" crucial instructions so they aren't lost during context compaction (#26889).
- **Workflow Efficiency**: Re-typing common prompts remains tedious. The community is asking for an in-app Prompt Snippets panel for high-frequency workflows (#26467).
- **OS-Specific Control**: Windows developers want parity with Unix systems, specifically the ability to swap the hardcoded PowerShell agent shell for Git Bash (#16717) to improve model code-generation accuracy.

### 6. Developer Pain Points
- **Windows Desktop Instability**: The Windows Codex App is experiencing severe issues, including transparent/invisible UI rendering (#26310, #26790), UAC crashes during Computer Use (#26530), and UTF-8 mojibake when reading files via PowerShell (#23044).
- **Connection Reliability**: Streaming failures and reconnect loops remain a top pain point across both the App and CLI (#18960, #13811, #24920), often resulting in lost context or truncated responses.
- **GPT-5.5 Regressions**: The rollout of GPT-5.5 has introduced significant friction, manifesting as misreported context windows causing usage drain (#26783), silent mid-task stops on AWS Bedrock (#26860), and general quality degradation over time (#26876).
- **MCP Extension Sync**: MCP servers working in the CLI but failing to load in the VS Code extension (#6465) remains a persistent blocker for developers relying on tool integrations.

</details>

<details>
<summary><strong>Gemini CLI</strong> — <a href="https://github.com/google-gemini/gemini-cli">google-gemini/gemini-cli</a></summary>

# Gemini CLI Community Digest — 2026-06-07

## 1. Today's Highlights
The Gemini CLI community is currently focused on improving agent reliability and security, with significant attention on persistent agent hangs and subagent misreporting. Security and robustness are also taking center stage in recent PRs, addressing command injection vulnerabilities and regex-based stack overflows. Strategically, the maintainers are heavily investing in AST-aware tooling and robust component-level evaluations to enhance the agent's code comprehension capabilities.

## 2. Releases
*No new releases were published in the last 24 hours.*

## 3. Hot Issues

1. **Generalist agent hangs indefinitely** [#21409](https://github.com/google-gemini/gemini-cli/issues/21409): A high-impact bug (👍 8) where deferring to the generalist agent causes the CLI to hang forever. Users report having to manually instruct the model to avoid sub-agents as a workaround.
2. **Robust component-level evaluations** [#24353](https://github.com/google-gemini/gemini-cli/issues/24353): An EPIC tracking the expansion of "behavioral evals." With 76 tests already generated, this tracks the infrastructure needed for more rigorous, component-level agent testing.
3. **Assess AST-aware file reads and mapping** [#22745](https://github.com/google-gemini/gemini-cli/issues/22745): An investigation into whether AST-aware tools can reduce token noise and misaligned reads. The community views this as a critical step toward better codebase navigation.
4. **VS Code UI lockup from globalState storage** [#27132](https://github.com/google-gemini/gemini-cli/issues/27132): The Gemini Code Assist extension blocks the main thread during long sessions or history reloads, causing the VS Code UI to freeze completely (👍 2).
5. **Subagent falsely reports success on MAX_TURNS** [#22323](https://github.com/google-gemini/gemini-cli/issues/22323): Subagents hitting the maximum turn limit report `status: "success"` and `Termination Reason: "GOAL"`, hiding the fact that the task was interrupted and incomplete.
6. **Gemini ignores custom skills and sub-agents** [#21968](https://github.com/google-gemini/gemini-cli/issues/21968): Users are frustrated that the model rarely invokes custom skills (e.g., gradle, git) unless explicitly instructed, limiting the utility of the skills framework.
7. **Auto Memory lacks deterministic redaction** [#26525](https://github.com/google-gemini/gemini-cli/issues/26525): The background extraction agent processes transcript content before redacting secrets, posing a security risk. This issue also highlights excessive logging of skill executions.
8. **Auto Memory retries low-signal sessions indefinitely** [#26522](https://github.com/google-gemini/gemini-cli/issues/26522): The memory system creates an infinite loop where unprocessed, low-signal sessions keep getting surfaced to the extraction agent.
9. **Shell command execution stuck on "Waiting input"** [#25166](https://github.com/google-gemini/gemini-cli/issues/25166): A core bug (👍 3) where the CLI hangs after a simple shell command completes, incorrectly waiting for user input.
10. **400 error with > 128 tools** [#24246](https://github.com/google-gemini/gemini-cli/issues/24246): The CLI crashes with a 400 API error when overloaded with tools, highlighting a need for smarter tool-scope filtering.

## 4. Key PR Progress

1. **fix(security): prevent command injection in findCommand** [#27575](https://github.com/google-gemini/gemini-cli/pull/27575): Replaces vulnerable shell-interpolated `execSync` calls with safe `spawnSync`/`spawn` to prevent command injection via shell metacharacters.
2. **fix(at-command): prevent stack overflow from regex backtracking** [#27580](https://github.com/google-gemini/gemini-cli/pull/27580): Replaces the regex-based `@` command parser with an iterative scanner to prevent catastrophic backtracking on large pasted inputs.
3. **fix(cli): fall back for oversized bug report URLs** [#27591](https://github.com/google-gemini/gemini-cli/pull/27591): Fixes a crash on Android/Termux where `/bug` reports exceed the OS deep-link/intent size limits.
4. **fix(core): keep auto visible without preview access** [#27718](https://github.com/google-gemini/gemini-cli/pull/27718): Ensures the `auto` model alias remains visible in the `/model` menu for users without preview access when dynamic model config is enabled.
5. **feat(a2a-server): Add detached/background task execution mode** [#15674](https://github.com/google-gemini/gemini-cli/pull/15674): Introduces fire-and-forget background task spawning with timeouts and worker listing to the a2a-server (Closed/Merged).
6. **Support global cross-folder session resume** [#23490](https://github.com/google-gemini/gemini-cli/pull/23490): Allows `gemini --resume <session-id>` to work across different folders rather than being restricted to the original project directory (Closed/Merged).
7. **fix(core): parse tools.callCommand before discovered tool execution** [#27405](https://github.com/google-gemini/gemini-cli/pull/27405): Parses `tools.callCommand` into program/argv before passing to the sandbox, fixing an execution bug (Closed/Merged).
8. **fix(acp): accept string protocolVersion during initialize** [#27398](https://github.com/google-gemini/gemini-cli/pull/27398): Normalizes date-style/string protocol versions to numeric formats during ACP initialization, improving SDK compatibility (Closed/Merged).
9. **Fix Node 20 Compatibility and Windows symlink Test Failures** [#27385](https://github.com/google-gemini/gemini-cli/pull/27385): Addresses a production crash on Node 20.x by replacing `URL.parse` and resolves flaky Windows symlink tests (Closed/Merged).
10. **docs: clarify GEMINI_CLI_HOME settings path** [#27395](https://github.com/google-gemini/gemini-cli/pull/27395): Documents that user settings remain under `.gemini/settings.json` even when `GEMINI_CLI_HOME` is overridden (Closed/Merged).

## 5. Feature Request Trends

*   **AST-Aware Code Navigation:** A strong, multi-issue push (#22745, #22746, #22747) for integrating AST-aware CLI tools (like `tilth`, `glyph`, or `ast-grep`) to allow precise method-bound reads and syntax-based searches, reducing token waste.
*   **Agent Backgrounding & Multitasking:** Users want the ability to send local sub-agents to the background (#22741) using shortcuts like `Ctrl+B` for non-blocking tasks like linting or building.
*   **Destructive Behavior Guardrails:** Requests for the agent to avoid dangerous commands (#22672) like `git reset --force` or unguarded DB modifications, preferring safer alternatives.
*   **Improved Agent Self-Awareness:** Demands (#21432) for the CLI to understand its own mechanics, flags, and hotkeys well enough to guide users without hallucinating its capabilities.

## 6. Developer Pain Points

*   **Agent Reliability & Deception:** The CLI frequently hangs when invoking sub-agents (#21409, #25166), and worse, falsely reports task success when it hits internal limits (#22323), breaking user trust.
*   **Poor Sub-Agent Steering:** The model struggles to autonomously leverage custom skills and sub-agents (#21968), requiring explicit hand-holding, while simultaneously sometimes running agents without permission (#22093).
*   **Environment & Platform Quirks:** Heavy friction exists on specific platforms, particularly VS Code extension lockups (#27132) and Wayland incompatibility with the browser agent (#21983).
*   **Auto Memorybrittle Behavior:** The Auto Memory system is currently a source of frustration, creating infinite retry loops for low-signal sessions (#26522) and failing to deterministically redact secrets before model processing (#26525).

</details>

<details>
<summary><strong>GitHub Copilot CLI</strong> — <a href="https://github.com/github/copilot-cli">github/copilot-cli</a></summary>

# GitHub Copilot CLI Community Digest — 2026-06-07

## 1. Today's Highlights
No new releases were shipped today, but the community was highly active around platform stability and model flexibility. A high-severity WSL2 regression causing massive CPU spikes and frozen TUIs garnered urgent attention, alongside critical bugs in sub-agent execution with GPT-5.5 and MCP session handling. On the feature front, strong momentum continues behind multi-model BYOK switching and enhanced lifecycle hooks for agent customization.

## 2. Releases
No new releases in the last 24 hours.

## 3. Hot Issues
1. **[High Severity] WSL2 CPU Spin & Frozen TUI** ([#3700](github/copilot-cli Issue #3700)): A severe regression where the MainThread spins at ~215% CPU while idle on WSL2, rendering the TUI unusable until restarted. High impact for Windows developers.
2. **Feature Request: `awaitingUserInput` hook** ([#1128](github/copilot-cli Issue #1128)): Requesting a hook that fires when the CLI is waiting for input. Highly requested (👍 27) to bridge a gap in the agent lifecycle for custom automations.
3. **Support pasting images from clipboard** ([#1276](github/copilot-cli Issue #1276)): Users want to paste screenshots (bugs, logs, code) directly into prompts. Gained significant traction (👍 8, 11 comments) as multi-modal workflows become standard.
4. **Background sub-agent hangs with GPT-5.5** ([#3547](github/copilot-cli Issue #3547)): Dispatched background agents silently hang at `total_turns=0` when using `model="gpt-5.5"`. Highlights growing pains with newest model integrations.
5. **Allow `/model` to switch BYOK/local providers mid-session** ([#3709](github/copilot-cli Issue #3709)): The `/model` picker currently ignores local BYOK providers, forcing users to kill sessions to switch models via environment variables.
6. **Multiple BYOK model capability** ([#3282](github/copilot-cli Issue #3282)): Echoing #3709, users find the single `COPILOT_MODEL` env var too restrictive for comparing local models. 
7. **Autopilot scope creep and ignored "stop" commands** ([#3655](github/copilot-cli Issue #3655)): In autopilot mode, agents self-answer their own clarifying questions and execute unrequested actions, even after explicit stop commands. Raises safety and control concerns.
8. **WSL 40-80s startup delays** ([#3652](github/copilot-cli Issue #3652)): `CopilotCLIChatSessionContentProvider.listSessions` causes massive lag for VS Code + WSL users, making the tool frustrating to start.
9. **Remote MCP OAuth startup fan-out** ([#3706](github/copilot-cli Issue #3706)): Remote HTTP MCP servers requiring OAuth are initialized repeatedly (79 times in one session), triggering rate limits and repeated auth prompts.
10. **Escape key discards queued prompts** ([#3692](github/copilot-cli Issue #3692)): Pressing Escape to cancel a running task unexpectedly discards the user's pending queued prompt rather than shifting focus to it.

## 4. Key PR Progress
*Note: Only one pull request was updated in the last 24 hours, and it appears to be a low-quality/spam submission.*
1. **Add files via upload** ([#3708](github/copilot-cli PR #3708)): Opened by `panchofrancisco1987-ui` with no description, code, or context. Awaiting triage/closure.

## 5. Feature Request Trends
*   **In-Session Model Agility:** Users are increasingly frustrated by the rigid single-model constraint per session. There is a strong push to make the `/model` command dynamic enough to include local/BYOK providers, allowing seamless model switching without dropping context ([#3282](github/copilot-cli Issue #3282), [#3709](github/copilot-cli Issue #3709)).
*   **Multi-Modal & Rich Input:** As AI coding assistants evolve, text-only input is becoming a bottleneck. Native support for image pasting from the OS clipboard is heavily requested to support visual debugging workflows ([#1276](github/copilot-cli Issue #1276)).
*   **Finer-Grained Lifecycle Hooks:** Developers want deeper integration into the agent's state machine. The demand for an `awaitingUserInput` hook complements existing hooks, enabling external integrations (like audio notifications or UI state changes) when the CLI is ready for the user ([#1128](github/copilot-cli Issue #1128)).
*   **Cost Affordability:** With token-based usage, heavy users are feeling the pinch. The community is asking for lower-cost or open-weight model options to affordably scale daily usage ([#3707](github/copilot-cli Issue #3707)).

## 6. Developer Pain Points
*   **Windows/WSL Platform Instability:** Windows developers are experiencing severe regressions, from a 215% CPU idle spin freezing the TUI ([#3700](github/copilot-cli Issue #3700)) to nearly minute-long startup delays ([#3652](github/copilot-cli Issue #3652)). WSL2 support currently feels unstable.
*   **Agent Autonomy & Control:** Users are experiencing "scope creep" in autopilot mode where the agent runs away with tasks, ignores explicit "stop" commands, and self-answers clarifying questions ([#3655](github/copilot-cli Issue #3655)). Additionally, aggressive cancellation (Escape) destroys user work (queued prompts) rather than pausing ([#3692](github/copilot-cli Issue #3692)).
*   **MCP Integration Friction:** The MCP client needs robustness improvements. It currently fails to persist `Mcp-Session-Id` headers (causing 400 errors) ([#3668](github/copilot-cli Issue #3668)) and aggressively re-initializes OAuth for remote MCP servers, hitting rate limits ([#3706](github/copilot-cli Issue #3706)).
*   **Silent Failures on New Models:** Using the latest models (like GPT-5.5) in programmatic agent workflows results in silent hangs rather than graceful errors, making debugging background tasks difficult ([#3547](github/copilot-cli Issue #3547)).

</details>

<details>
<summary><strong>Kimi Code CLI</strong> — <a href="https://github.com/MoonshotAI/kimi-cli">MoonshotAI/kimi-cli</a></summary>

# Kimi Code CLI Community Digest — 2026-06-07

## 1. Today's Highlights
The Kimi CLI community is currently dominated by friction surrounding the transition from `kimi-cli` to the new `kimi-code` client. Users are raising serious concerns about fragmented ecosystems, broken migration paths, and regressions in agent quality. On the development side, PR activity focuses on bolstering client stability, specifically addressing persistent MCP connection crashes and flaky image path handling in the shell.

## 2. Releases
*(Omitted — no new releases in the last 24 hours)*

## 3. Hot Issues
*(Note: 3 issues updated in the last 24 hours; all are highlighted below due to their critical nature)*

*   **[#2381](https://github.com/MoonshotAI/kimi-cli/issues/2381) Why abandon kimi-cli and redo kimi code?**  
    *Why it matters:* This closed issue captures a growing sentiment among early adopters who feel blindsided by the pivot from `kimi-cli` to `kimi-code`. The author argues that splitting the community and altering core functionalities betrays the trust of users who adopted the CLI for long-term production work. The community reaction highlights a broader trust issue regarding MoonshotAI's product continuity.
*   **[#2437](https://github.com/MoonshotAI/kimi-cli/issues/2437) Migration Feedback: unclear state migration, quota attribution confusion, and agent quality regression**  
    *Why it matters:* Opened today, this is a critical bug report detailing the technical failures of moving from `kimi-cli v1.47.0` to `kimi-code v0.11.0`. The user reports broken state migration, lost quota attribution, and a perceived regression in agent logic. This issue is a blocker for power users attempting the transition.
*   **[#2436](https://github.com/MoonshotAI/kimi-cli/issues/2436) [bug] Installation failed. The new Kimi Code is installed ✓ Kimi can't seem to make up her mind.**  
    *Why it matters:* Also opened today, this issue highlights a frustrating versioning clash. After installing the new `kimi-code`, running `kimi-cli -V` still reports `v1.47.0` using the `kimi-k2.6` model. The environment conflicts are causing installation failures and user confusion over which client is actually active.

## 4. Key PR Progress
*(Note: 2 PRs updated in the last 24 hours; both are highlighted below)*

*   **[#1769](https://github.com/MoonshotAI/kimi-cli/pull/1769) fix: graceful degradation when MCP server fails to connect**  
    *Description:* Addresses a critical stability flaw where an `MCPRuntimeError` (e.g., from a port conflict between TUI and Web UI) would crash the worker and leave the frontend stuck in an infinite "thinking" state. The fix catches the error in `_agent_loop()`, allowing the CLI to degrade gracefully instead of hanging.
*   **[#2183](https://github.com/MoonshotAI/kimi-cli/pull/2183) fix(shell): attach dropped image paths eagerly**  
    *Description:* Resolves a race condition where short-lived local image paths would be missed by the `ReadMediaFile` function. The updated logic eagerly scans user text for image paths upon prompt submission and reads them immediately into an `ImageURLPart`, ensuring multimodal inputs are reliably attached before the context is processed.

## 5. Feature Request Trends
Based on the recent issues, the strongest "feature request" is not a new capability, but **seamless migration and parity**. Users are explicitly demanding:
*   **State and Quota Portability:** Automatic and clear migration of user state, authentication, and quota attribution from the old `kimi-cli` binary to the new `kimi-code` binary.
*   **Feature Parity Assurance:** Guarantees that the new `kimi-code` agent performs at least as well as the legacy `kimi-cli` agent, without logic regressions.

## 6. Developer Pain Points
*   **Ecosystem Fragmentation:** Developers are deeply frustrated by the hard pivot from `kimi-cli` to `kimi-code`. Replacing a trusted tool with a separate client rather than iterating on the existing one makes developers hesitant to rely on Kimi for production workflows.
*   **Broken Migration Paths:** Moving to `kimi-code v0.11.0` currently breaks quota tracking, fails to cleanly replace the older `v1.47.0` binary, and leaves the system in an ambiguous state regarding which CLI is actually executing commands.
*   **MCP Instability:** While being actively patched in PR #1769, uncaught MCP errors crashing the agent loop and freezing the UI remain a major pain point for users running complex toolchains.

</details>

<details>
<summary><strong>OpenCode</strong> — <a href="https://github.com/anomalyco/opencode">anomalyco/opencode</a></summary>

# OpenCode Community Digest — 2026-06-07

## 1. Today's Highlights

OpenCode's community is experiencing significant friction around **compaction and context loss** — multiple issues and PRs today converge on the theme that long sessions degrade AI output quality, with compaction either failing outright (especially with Anthropic extended thinking) or silently discarding critical context. Meanwhile, **billing complaints around the ZEN payment flow** continue to escalate, with users reporting scam-like redirect behavior and unresponsive support. On the positive side, the contributor community is active with a wave of targeted bug-fix PRs addressing TUI stability, provider compatibility, and permission handling.

---

## 2. Releases

No new releases in the last 24 hours.

---

## 3. Hot Issues

1. **[#30811] Code quality degrades in long conversations — compaction loses context, no auto-verification after edits** — A deeply detailed issue identifying five compaction-related problems (context discard, no edit verification, drift). Zero thumbs-up but 13 comments signal active debate on a core architectural flaw. [→ Issue](https://github.com/anomalyco/opencode/issues/30811)

2. **[#30680] OpenCode enters auto-compaction loop, stops generating responses** — Compaction consumes tokens in an infinite loop even in empty directories, eventually halting all output. Directly impacts usability for any long session. 7 comments. [→ Issue](https://github.com/anomalyco/opencode/issues/30680)

3. **[#31188] Compaction fails on first attempt with Anthropic extended thinking blocks** — `/compact` always errors when `thinking`/`redacted_thinking` blocks are present, because Anthropic rejects requests that don't start with a user boundary. A precise, well-diagnosed provider-interop bug. 2 comments. [→ Issue](https://github.com/anomalyco/opencode/issues/31188)

4. **[#31048] Anthropic compacted tool histories need a leading user boundary** — Closely related to #31188: after compaction or session import, Anthropic conversations can start with an assistant message, violating the API contract. 2 comments. [→ Issue](https://github.com/anomalyco/opencode/issues/31048)

5. **[#2242] Sandbox the agent — restrict file access outside project directory** (OPEN, 60 comments, 51 👍) — A long-standing, highly upvoted request for seatbelt-style sandboxing (like gemini-cli/codex-cli). Still unresolved and clearly a top community priority. [→ Issue](https://github.com/anomalyco/opencode/issues/2242)

6. **[#26508] & [#28226] & [#31223] — ZEN payment scam complaints** — Three separate issues from users who were redirected to ZEN instead of GO, received unusable API keys, and got no support response. Users are filing chargebacks. This is a billing/UX trust crisis. [#26508](https://github.com/anomalyco/opencode/issues/26508) · [#28226](https://github.com/anomalyco/opencode/issues/28226) · [#31223](https://github.com/anomalyco/opencode/issues/31223)

7. **[#31222] Agent can't view uploaded images** — Uploaded images aren't processed by the agent; it reads only text values. A basic multimodal gap. 4 comments. [→ Issue](https://github.com/anomalyco/opencode/issues/31222)

8. **[#31204] `session_message.seq` NOT NULL constraint crash after agent switch** — Recent database migrations (June 3–5) broke agent-switched sessions with a SQLite constraint error. A regression in the latest update. 2 comments. [→ Issue](https://github.com/anomalyco/opencode/issues/31204)

9. **[#11830] Multi-Account OAuth with Auto-Relogin** (22 comments, 20 👍) — Feature request for OAuth credential rotation across multiple accounts to avoid rate-limit interruptions. High demand from power users. [→ Issue](https://github.com/anomalyco/opencode/issues/11830)

10. **[#31225] Tool call schema `type` becomes `null` via cc-switch + OpenCode Go** — When using cc-switch as a local proxy to Claude Code, parameter schema types resolve to `null`, breaking all tool calls. Highlights proxy/compatibility fragility. 2 comments. [→ Issue](https://github.com/anomalyco/opencode/issues/31225)

---

## 4. Key PR Progress

1. **[PR #26861] fix(tui): Old messages disappearing during long sessions** — Implements lazy-scroll loading (50 messages at a time on scroll-up) to prevent TUI rendering collapse in extended sessions. Targets the long-standing #7380. [→ PR](https://github.com/anomalyco/opencode/pull/26861)

2. **[PR #26477] fix(provider): keep Claude prefill requests user-final** — Fixes two ways Claude requests can resolve to the wrong provider-facing shape, including stray tool-result messages that violate the Anthropic API contract. Directly relates to compaction issues #31048 and #31188. [→ PR](https://github.com/anomalyco/opencode/pull/26477)

3. **[PR #31210] fix(tui): scope non-git sessions by directory, not hierarchical path** — Replaces the broken hierarchical path filter introduced in #24849 with exact-directory matching, closing **6 issues** (#8836, #18890, #19340, #26099, #28972, #9881). High-impact fix for session visibility. [→ PR](https://github.com/anomalyco/opencode/pull/31210)

4. **[PR #31211] fix(tui): replace @solid-primitives/scheduled with manual debounce** — The `conditions: ["node"]` build change set `isServer=true`, causing `@solid-primitives/scheduled` to return a no-op and break session search filtering. Manual debounce restores functionality. Closes #31182. [→ PR](https://github.com/anomalyco/opencode/pull/31211)

5. **[PR #31216] fix: convert permission defects to typed failures for `continue_loop_on_deny`** — Permission rejections previously went through `Effect.orDie`, converting recoverable rejects into panics. This PR makes them typed `ToolFailure` results so task loops can continue. Closes #31108. [→ PR](https://github.com/anomalyco/opencode/pull/31216)

6. **[PR #31230] fix: keep read path validation model-visible** — Converts read-path validation errors into `ToolFailure` results the model can see and react to, instead of dying silently inside the runner. Adds regression tests. [→ PR](https://github.com/anomalyco/opencode/pull/31230)

7. **[PR #31238] fix(plugin): fall back when live transport is unauthorized** — Plugin clients using the live HTTP listener now gracefully fall back on 401/403 instead of failing. Closes #31237. [→ PR](https://github.com/anomalyco/opencode/pull/31238)

8. **[PR #29279] fix(provider): emit file metadata instead of error when model lacks image support** — Non-vision models receiving image attachments currently get an error string; this PR converts it to structured metadata so the model can inform the user gracefully. Closes #29216. [→ PR](https://github.com/anomalyco/opencode/pull/29279)

9. **[PR #31232] fix: agent stuck in plan mode in v2 desktop** — When "Show custom agents" is disabled, the selected agent mode incorrectly defaults to plan mode. This PR ensures it falls back to "Build." Closes #31231. [→ PR](https://github.com/anomalyco/opencode/pull/31232)

10. **[PR #31208] experiment: better web picker using @pierre/tree** — An experimental PR exploring a shared tree browser component for desktop v2 file/directory selection with lazy server-side navigation, keyboard accessibility, and path completion. A forward-looking UX experiment. [→ PR](https://github.com/anomalyco/opencode/pull/31208)

---

## 5. Feature Request Trends

- **Agent sandboxing and security boundaries** — The #2242 sandboxing issue (51 👍) remains one of the most-upvoted open requests. The symlink external directory consent request (#30788) and "deny tool use with message" feature (#7817) further reinforce demand for fine-grained permission and isolation controls.
- **Context preservation across long sessions** — Multiple issues (#30811, #30680, #31188, #31048) converge on the same theme: compaction is destructive, unverified, and provider-incompatible. Users want automatic verification after edits, smarter context summarization, and provider-aware compaction.
- **Multi-account and rate-limit resilience** — OAuth credential rotation (#11830, 20 👍) and usage limit adjustments (#28846, 77 👍) reflect power users hitting ceilings and wanting seamless failover.
- **Local model integration** — Ollama users report ignored queries (#25678), inability to generate files (#29996), and CLI hangs (#20908). Local model support clearly needs hardening.
- **Cross-platform fidelity** — Windows-specific bugs (LF line endings breaking .bat files in #31224, crash-on-start in #29906, garbled TUI text in #23914) show the Windows experience lags significantly.

---

## 6. Developer Pain Points

- **Compaction is the single biggest reliability concern.** It breaks provider contracts (Anthropic), loops infinitely consuming tokens, and silently degrades output quality. No PR yet addresses the core architectural issue — current fixes are narrow and reactive.
- **Billing and support are eroding trust.** The ZEN redirect issue has produced at least 3 separate scam reports and chargeback filings. Zero official response on 12-day-old refund requests (#29182). This is a trust problem, not just a bug.
- **Windows is a second-class platform.** From line-ending bugs to start-up crashes to TUI rendering corruption, Windows users face a disproportionate number of blocking issues with no clear ownership.
- **Error visibility is poor.** Multiple PRs today (#31216, #31230, #29279, #31228) fix cases where errors die silently or produce unhelpful output, suggesting a systemic pattern of swallowed failures across the toolchain.
- **Ollama/local model support is unreliable.** Users cannot complete basic workflows (read, generate, respond) with local models, making the "self-hosted" value proposition unfulfilled.

</details>

<details>
<summary><strong>Pi</strong> — <a href="https://github.com/badlogic/pi-mono">badlogic/pi-mono</a></summary>

## Pi Community Digest — 2026-06-07

### 1. Today's Highlights
Provider compatibility and core agent resilience dominate today's activity, with critical breakages reported for Anthropic's Opus 4.8 adaptive thinking and OpenAI's Codex transport. On the ecosystem front, extension developers are pushing heavily for a more expansive public API, requesting the exposure of internal context methods, RPC types, and session lifecycle hooks. Meanwhile, a significant PR by `mitsuhiko` introducing a workspace approval system for loading `.pi` extensions signals a major upcoming security enhancement for the platform.

### 2. Releases
No new releases were recorded in the last 24 hours.

### 3. Hot Issues
*   **[#5223](https://github.com/earendil-works/pi/issues/5223) Anthropic provider modifies thinking blocks, causing 400 with Opus 4.8:** Multi-turn conversations with Claude Opus 4.8 (adaptive thinking) fail mid-session due to invalid `thinking` blocks in the latest assistant message. This high-engagement issue (15 comments, 6 👍) highlights the fragility of provider-specific reasoning API implementations. 
*   **[#5427](https://github.com/earendil-works/pi/issues/5427) OpenAI Codex transport timeout issues:** Users experience persistent "Codex SSE response headers timed out after 10000ms" errors during long sessions, making Codex models unusable until a restart. Community reaction indicates this is a blocking issue for ChatGPT sub users.
*   **[#5188](https://github.com/earendil-works/pi/issues/5188) Shift+Enter submits instead of creating a new line:** A high-friction TUI bug where `shift+enter` submits rather than inserts a newline, despite explicit keybinding configuration. While `ctrl+j` works, this violates standard terminal UX expectations.
*   **[#5464](https://github.com/earendil-works/pi/issues/5464) Local models: 3-5 minute "Working" status latency:** When using Ollama (e.g., `ministral3:8b`), Pi introduces an unreasonable delay before simple messages mid-session. This severely impacts the local-first developer experience.
*   **[#5469](https://github.com/earendil-works/pi/issues/5469) Feature request: Collapse MCP tool results by default:** Heavy MCP workflows clutter the TUI with massive expanded blocks (fetch, search). Users are requesting a `settings.json` toggle to collapse these by default to maintain terminal readability.
*   **[#4160](https://github.com/earendil-works/pi/issues/4160) Pi extensions do not play nice with Bun:** Installing extensions fails on systems using Bun without Node/npm installed, as the CLI hardcodes `npm` for package resolution. This limits runtime flexibility for the JS/TS community.
*   **[#5456](https://github.com/earendil-works/pi/issues/5456) openai-responses provider ignores `compat.supportsDeveloperRole`:** When `model.reasoning` is enabled, the provider forces `role: "developer"` regardless of model config, breaking compatibility with providers that don't support this OpenAI-specific role.
*   **[#5463](https://github.com/earendil-works/pi/issues/5463) Auto-compaction after final turn throws error:** Auto-compaction triggered after a normal assistant turn crashes the agent with `Cannot continue from message role: assistant`. A critical hit to session stability.
*   **[#5445](https://github.com/earendil-works/pi/issues/5445) `_prepareRetry` crashes on retryable errors after `end_turn`:** When a 529 or rate limit error occurs after a continuation, the retry logic removes the error message but fails when hitting a previous `end_turn` assistant message, killing the session.
*   **[#5468](https://github.com/earendil-works/pi/issues/5468) MiniMax-M3 tool replay sends unknown tool IDs:** Long sessions with `minimax-cn` fail when tool replay sends a `tool_result` with an ID the server no longer recognizes, requiring manual model switch or compaction to recover.

### 4. Key PR Progress
*(Note: Only 4 PRs were updated in the last 24 hours; the 3 substantial ones are highlighted below)*
*   **[#5332](https://github.com/earendil-works/pi/pull/5332) feat(config): Approval system for workspaces:** A major security enhancement by `mitsuhiko` requiring user approval (or `-f` flag) before loading extensions from `.pi` and `.pi.user` directories interactively. It also introduces `.pi.user` as a dedicated folder for user extensions that shouldn't be overridden.
*   **[#5467](https://github.com/earendil-works/pi/pull/5467) Include models.json path in migration parse errors:** Improves developer experience by attaching the absolute file path to malformed `models.json` migration errors, making configuration debugging significantly easier.
*   **[#5465](https://github.com/earendil-works/pi/pull/5465) feat: add mineru document-parsing skill:** Adds a new Agent Skill under `.pi/skills/mineru/` integrating the MinerU CLI for both local and URL-based document parsing, complete with polling and extract capabilities.

### 5. Feature Request Trends
*   **Expanding the Extension API Surface:** Extension developers consistently hit walls with Pi's internal encapsulation. Recent requests demand the export of core types and hooks like [`RpcExtensionUIRequest`](https://github.com/earendil-works/pi/issues/5455), [`runAgentSession`](https://github.com/earendil-works/pi/issues/5444), and [`waitForIdle`/`reload` on ExtensionContext](https://github.com/earendil-works/pi/issues/5443). 
*   **TUI Output & UX Customization:** There is a strong desire for user-controlled UI density and behavior. Requests include collapsing MCP outputs by default, [configurable image paste directories](https://github.com/earendil-works/pi/issues/5414) to prevent tmpdir cleanup data loss, and [custom cost units](https://github.com/earendil-works/pi/issues/4578) for non-USD credit systems.
*   **Agent Behavior Modularity:** Users want granular control over the agent's internals, such as [opting out of specific built-in tools](https://github.com/earendil-works/pi/issues/5447) (read, bash, grep) from the sandbox, and customizing the [compaction system prompt](https://github.com/earendil-works/pi/issues/5401) which currently hardcodes "AI coding assistant."

### 6. Developer Pain Points
*   **Provider API Fragility:** Rapid evolution in LLM provider APIs (especially Anthropic's thinking blocks and OpenAI's Codex SSE transport) is breaking Pi sessions mid-flight. The agent struggles to gracefully handle provider-specific quirks like `redacted_thinking` and strict role requirements.
*   **Extension API Opacity:** Extension authors are frequently forced to "hijack internal prototypes" to accomplish basic tasks because the public API hides crucial context methods and RPC types. This creates brittle extensions that break across Pi updates.
*   **Core Agent State Management:** The agent's internal state machine for message roles and continuations is prone to crashing during edge cases—specifically during auto-compaction and retry logic after rate limits. Developers experience hard session kills instead of graceful recoveries.

</details>

<details>
<summary><strong>Qwen Code</strong> — <a href="https://github.com/QwenLM/qwen-code">QwenLM/qwen-code</a></summary>

# Qwen Code Community Digest — 2026-06-07

## 1. Today's Highlights
Qwen Code accelerates its daemon-mode evolution with massive feature merges into `main` and substantial progress on ACP (Agent Client Protocol) transport parity. The latest v0.17.1 nightly release lands alongside critical fixes for memory compaction and OOM prevention in long-running sessions. Developers should also note significant community traction around model routing flexibility and persistent UI rendering issues in compact mode.

## 2. Releases
- **[v0.17.1-nightly.20260607.cef26a86a](https://github.com/QwenLM/qwen-code/releases/tag/v0.17.1-nightly.20260607.cef26a86a)**: Includes the `v0.17.1` release chore and a fix to skip thought parts during copy output in the CLI ([PR #4742](https://github.com/QwenLM/qwen-code/pull/4742)).

## 3. Hot Issues
1. **[BUG: Compact mode tool merge causes full-screen flash](https://github.com/QwenLM/qwen-code/issues/4794)** (#4794): Ink's `<Static>` component re-renders violently when `mergeCompactToolGroups` shrinks the history array, causing a jarring flash on every tool batch. A high-priority UX blocker for TUI users.
2. **[v0.17 Infinite loop on memory save & @image passivity](https://github.com/QwenLM/qwen-code/issues/4700)** (#4700): The agent enters an infinite `readFile` loop when saving memories, and ignores `@`-attached images unless explicitly commanded to look at them.
3. **[modelProviders: shared baseUrl cannot be set once](https://github.com/QwenLM/qwen-code/issues/4813)** (#4813): Config friction where users must duplicate the `baseUrl` for every model pointing to the same endpoint (e.g., local vLLM), instead of setting it once.
4. **[TUI mode sudden interrupt causes context amnesia](https://github.com/QwenLM/qwen-code/issues/4740)** (#4740): Specific models (DeepSeek, Meituan) abruptly interrupt during execution and lose prior context/memory upon continuation.
5. **[Task interruption without auto-continuation](https://github.com/QwenLM/qwen-code/issues/4278)** (#4278): Agent stops mid-task and fails to resume execution automatically, requiring manual intervention.
6. **[Vim mode keybinding leaks & Esc conflicts](https://github.com/QwenLM/qwen-code/issues/4675)** (#4675): Esc leaks from Vim INSERT mode to AppContainer (triggering exit prompts/interruptions), and Enter fails to send in NORMAL mode.
7. **[LAN usage stuck at initialization](https://github.com/QwenLM/qwen-code/issues/4550)** (#4550): In air-gapped or enterprise LANs, the CLI hangs indefinitely at init due to unreachable external endpoints.
8. **[UI friction for Custom Provider model additions](https://github.com/QwenLM/qwen-code/issues/4814)** (#4814): The initial setup wizard lacks a streamlined way to append new models to existing Custom Providers post-init.
9. **[Windows SMB shared folder access failure](https://github.com/QwenLM/qwen-code/issues/4720)** (#4720): Qwen Code fails to access SMB paths and injects erroneous spaces into absolute paths on Windows.
10. **[Foreground sleep interception blocks rate-limit backoff](https://github.com/QwenLM/qwen-code/issues/4707)** (#4707): Shell sleep interception prevents agents from using `sleep` for legitimate API rate-limit retry/backoff strategies.

## 4. Key PR Progress
1. **[feat(daemon): merge daemon-mode feature batch into main](https://github.com/QwenLM/qwen-code/pull/4490)** (#4490): A massive integration of `daemon_mode_b_main` → `main` (+115k/−12k LOC), bundling the core v0.16-alpha daemon feature set.
2. **[feat(serve): ACP/REST parity — 29 new _qwen/* methods](https://github.com/QwenLM/qwen-code/pull/4827)** (#4827): Adds 29 new `_qwen/*` dispatch methods to the daemon, achieving full ACP/REST parity and production hardening.
3. **[fix(core): prevent OOM by compacting API/UI history](https://github.com/QwenLM/qwen-code/pull/4824)** (#4824): Introduces targeted microcompaction on Hook messages and under memory pressure to prevent old-space exhaustion in long-running sessions.
4. **[fix(tui): skip cross-group tool merge in <Static> mode](https://github.com/QwenLM/qwen-code/pull/4795)** (#4795): Directly addresses Issue #4794 by disabling data-level merges when `useTerminalBuffer` is false, eliminating the compact mode flash.
5. **[feat(memory): add user-level auto-memory](https://github.com/QwenLM/qwen-code/pull/4764)** (#4764): Implements `~/.qwen/memories/` for cross-project user facts/preferences, preventing the agent from re-learning style/background per repo.
6. **[fix(auth): time out Qwen OAuth refresh](https://github.com/QwenLM/qwen-code/pull/4829)** (#4829): Prevents CLI startup from hanging indefinitely if the OAuth refresh endpoint accepts the connection but never returns.
7. **[fix(core): preserve shared baseUrl on auth refresh](https://github.com/QwenLM/qwen-code/pull/4828)** (#4828): Stops the auth refresh flow from overwriting a user-configured shared `baseUrl` with provider defaults.
8. **[feat(serve): add POST /session/:id/branch](https://github.com/QwenLM/qwen-code/pull/4812)** (#4812): Enables programmatic session forking for remote clients via the daemon API without history replay.
9. **[fix(core): coerce non-string tool params to strings](https://github.com/QwenLM/qwen-code/pull/4793)** (#4793): Patches `SchemaValidator` rejections from self-hosted LLMs (vLLM, LMStudio) that incorrectly return numbers/booleans for string tool params.
10. **[fix(clipboard): use platform-native tools for image paste on Linux](https://github.com/QwenLM/qwen-code/pull/4647)** (#4647): Replaces the buggy native module with `wl-paste`/`xclip`, fixing image pasting in WSL2/Wayland environments.

## 5. Feature Request Trends
- **Resilient Model Routing & Fallback**: Significant demand for intelligent routing between local and cloud models ([#4640](https://github.com/QwenLM/qwen-code/issues/4640), [#1206](https://github.com/QwenLM/qwen-code/issues/1206)), and fallback model support to maintain session continuity during provider outages/rate-limits ([#4830](https://github.com/QwenLM/qwen-code/issues/4830)).
- **Daemon & ACP Protocol Expansion**: Strong push for closing the capability gap between the interactive CLI and the `qwen serve` HTTP/SSE surface ([#4514](https://github.com/QwenLM/qwen-code/issues/4514)), and full Streamable HTTP/WebSocket transport alignment ([#4782](https://github.com/QwenLM/qwen-code/issues/4782)).
- **Streamlined Provider Configuration**: Users want an easier way to manage multi-model endpoints, specifically avoiding `baseUrl` duplication ([#4813](https://github.com/QwenLM/qwen-code/issues/4813)) and better UI flows for appending models to custom providers ([#4814](https://github.com/QwenLM/qwen-code/issues/4814)).

## 6. Developer Pain Points
- **UI Rendering & Flickering**: The Ink-based TUI struggles with array rebasing in compact mode, causing severe full-screen flashes on Windows and Unix ([#4561](https://github.com/QwenLM/qwen-code/issues/4561), [#4794](https://github.com/QwenLM/qwen-code/issues/4794)).
- **Context Loss & OOM**: Long-running agents suffer from memory leaks ([#4824](https://github.com/QwenLM/qwen-code/pull/4824)) and sudden execution interrupts that result in "amnesia" where the agent completely forgets the ongoing task context ([#4740](https://github.com/QwenLM/qwen-code/issues/4740), [#4278](https://github.com/QwenLM/qwen-code/issues/4278)).
- **Terminal Keybinding Conflicts**: Interoperability between Qwen's keybindings and Vim-mode / IDE terminals (like PyCharm) is frustrating, leading to accidental CLI exits or unresponsive states ([#4675](https://github.com/QwenLM/qwen-code/issues/4675), [#4586](https://github.com/QwenLM/qwen-code/issues/4586)).
- **Offline/Restricted Network Bootstrapping**: The CLI's hard dependency on internet connectivity during initialization blocks adoption in secure/air-gapped enterprise environments ([#4550](https://github.com/QwenLM/qwen-code/issues/4550)).

</details>

<details>
<summary><strong>DeepSeek TUI</strong> — <a href="https://github.com/Hmbown/DeepSeek-TUI">Hmbown/DeepSeek-TUI</a></summary>

# DeepSeek TUI Community Digest — 2026-06-07

## 1. Today's Highlights
The community is heavily focused on the **v0.9.0 "stewardship" milestone**, driven by a major architectural refactor of the command dispatch system from a monolithic match pattern to a modular strategy pattern. Contributor **HUQIANTAO** submitted a massive sweep of 4 PRs addressing 30+ critical bugs across concurrency, security, and silent error handling, signaling an active stabilization phase. Additionally, incremental improvements to multi-provider support (Hugging Face) and cache optimization highlight a push toward robustness before the next release.

## 2. Releases
*No new releases were published in the last 24 hours.*

## 3. Hot Issues
*(Note: Only 6 issues were updated in the last 24h; all are included below.)*

*   **[#2791 [OPEN] Refactor command dispatch from monolithic match to modular strategy pattern](https://github.com/Hmbown/CodeWhale/issues/2791)**
    *Why it matters:* This is the architectural cornerstone for v0.9.0. The current monolithic command structure has become a maintenance bottleneck. Refactoring to a strategy pattern will unblock future feature work and community contributions. (6 comments)
*   **[#2870 [OPEN] EPIC: staged command-boundary refactor for #2791](https://github.com/Hmbown/CodeWhale/issues/2870)**
    *Why it matters:* Tracks the incremental, mergeable layers for the command refactor. This staged approach prevents massive merge conflicts and ensures the main branch remains stable during the transition. (2 comments)
*   **[#2886 [OPEN] Enhancement: add Gherkin acceptance E2E coverage for tool lifecycle](https://github.com/Hmbown/CodeWhale/issues/2886)**
    *Why it matters:* Proposes adding E2E acceptance testing *before* migrating more routing code. This is a community best-practice to ensure the command refactor doesn't introduce behavioral regressions. (1 comment)
*   **[#2706 [OPEN] v0.9.0 Hugging Face provider polish: route validation, aliases, docs drift](https://github.com/Hmbown/CodeWhale/issues/2706)**
    *Why it matters:* Multi-provider support is a key feature, but the Hugging Face route currently suffers from undocumented aliases and validation gaps. This issue tracks the necessary polish for the v0.9.0 release. (1 comment)
*   **[#2872 [OPEN] CI process hangs at verify step (Smoke Tests)](https://github.com/Hmbown/CodeWhale/issues/2872)**
    *Why it matters:* A blocking issue for contributors. The CI pipeline hangs indefinitely during health checks on localhost, requiring manual cancellation. This severely slows down the PR merge velocity. (1 comment)
*   **[#2863 [CLOSED] French AZERTY @ key conflicts with Alt-@ sidebar shortcut](https://github.com/Hmbown/CodeWhale/issues/2863)**
    *Why it matters:* Highlights an i18n/layout accessibility bug where standard text input is hijacked by TUI shortcuts. Though closed, it serves as a reminder to handle international keyboard layouts gracefully. (1 comment)

## 4. Key PR Progress

*   **[#2883 fix: concurrency bugs - mutex handling, thread spawning, and resource management](https://github.com/Hmbown/CodeWhale/pull/2883)**
    Fixes 5 critical concurrency bugs, including mutex poisoning cascading crashes and thread exhaustion, which are vital for long-running TUI stability.
*   **[#2881 fix: error handling — log instead of silently swallowing errors](https://github.com/Hmbown/CodeWhale/pull/2881)**
    Addresses 11 instances where errors were silently discarded via `let _ =` or `.ok()`. This dramatically improves debuggability for end-users and developers.
*   **[#2882 fix: security bugs in execution policy, approval mapping, and tool input validation](https://github.com/Hmbown/CodeWhale/pull/2882)**
    Plugs 5 security vulnerabilities, including a whitespace bypass for deny rules in the execution policy engine and HTTP API approval mapping flaws.
*   **[#2880 fix: critical bugs in tools, client, and commands](https://github.com/Hmbown/CodeWhale/pull/2880)**
    Resolves 9 critical bugs causing panics and data corruption, notably a UTF-8 boundary panic in `clean_pdf_text` and incorrect command behaviors.
*   **[#2884 fix: client response handling and desktop tray icon safety](https://github.com/Hmbown/CodeWhale/pull/2884)**
    Fixes 5 client/desktop bugs, resolving connection pool leaks (unconsumed response bodies) and Tauri component lifecycle issues.
*   **[#2887 Add Gherkin acceptance E2E harness example](https://github.com/Hmbown/CodeWhale/pull/2887)**
    Implements the first Gherkin-style acceptance test layer as requested in #2886, laying the testing groundwork for the command refactor.
*   **[#2878 Layer 2: add command parity harness](https://github.com/Hmbown/CodeWhale/pull/2878)**
    Adds a command registry parity harness for metadata completeness and dispatch checks, ensuring the new modular commands match the old monolith's behavior.
*   **[#2874 feat(cache): slim runtime_prompt to minimal tag, move policy descriptions to system prompt](https://github.com/Hmbown/CodeWhale/pull/2874)**
    Optimizes token/cache usage by reducing the byte-stable system prompt footprint, preventing prefix-cache invalidation while retaining policy context.
*   **[#2869 fix(tui): list saved models from all providers in /model picker](https://github.com/Hmbown/CodeWhale/pull/2869)**
    Improves multi-provider UX by ensuring custom models saved under non-active providers (e.g., Moonshot) are visible in the `/model` picker.
*   **[#2873 feat(config): add hotbar slot persistence](https://github.com/Hmbown/CodeWhale/pull/2873)**
    Introduces `[[hotbar]]` persistence in config for slots 1-8, paving the way for customizable TUI key-dispatch actions instead of hardcoded bindings.

## 5. Feature Request Trends

*   **Modular Command Architecture:** The dominant trend is the push to break down the monolithic command dispatcher (#2791, #2870) into isolated, focused modules with clearly defined boundaries and parity harnesses.
*   **Multi-Provider Parity:** Consistent requests to treat non-DeepSeek providers (like Hugging Face and Moonshot) as first-class citizens, featuring proper route validation, token aliasing (#2706), and UI visibility (#2869).
*   **Customizable UI & Keybindings:** Demand for user-configurable interfaces is rising, evidenced by requests for hotbar persistence (#2873) and resolving rigid keybinding conflicts with international layouts (#2863).
*   **Advanced Execution Policies:** Features around "ask-only" permissions (#2885) and Pro Plan routing profiles (#1865) show a trend toward granular, user-controlled agent autonomy.

## 6. Developer Pain Points

*   **Silent Failures Masking Bugs:** The prevalence of silently swallowed errors (addressed in #2881) has been a major pain point, making it incredibly difficult for developers and users to diagnose failures and data loss.
*   **Runtime Instability under Concurrency:** Frequent mutex poisoning, thread exhaustion, and panics during async operations (#2883, #2880) indicate that the TUI's concurrent state management is currently brittle.
*   **CI Pipeline Unreliability:** The CI process hanging at the smoke test verify step (#2872) is a significant bottleneck, eroding contributor confidence and delaying merges.
*   **Nix/Sandbox Build Fragility:** Flaky tests failing in read-only environments like Nix sandboxes (#2876, #2877) show that the app's cache and configuration assumptions are too tightly coupled to writable `$HOME` directories.

</details>

---
*This digest is auto-generated by [agents-radar](https://github.com/yanzi6039/agents-radar).*