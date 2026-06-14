# AgentOGenesis — Implementation Plan (MVP)

Self-improving multi-agent development system built as an Antigravity Plugin.

---

## Summary of Decisions

| # | Decision | Choice |
|---|----------|--------|
| 1 | **Goal** | Practical dev tool — multi-agent team for coding, testing, review, with self-improving prompts |
| 2 | **Form** | Antigravity Plugin (`.md` files + `plugin.json` + hooks) |
| 3 | **UX** | Skill (SKILL.md) — main agent becomes orchestrator seamlessly |
| 4 | **Team (MVP)** | Coder + Tester + Reviewer + Editor (+ Orchestrator skill) |
| 5 | **Team (Later)** | + Architect + Researcher + Documentator |
| 6 | **Self-improvement trigger** | Two-level: autonomous fixes (errors) + user approval (optimizations/features) |
| 7 | **Editor scope** | Can modify: `.agent.md`, `SKILL.md`, `rules/`. Critical files only with approval |
| 8 | **Memory** | Extended: logs + metrics + project profiles |
| 9 | **Workflow** | Flexible + learnable: mandatory steps + flexible steps, orchestrator is learnable too |
| 10 | **TDD order** | Tester writes tests BEFORE Coder, isolated from code (AgentCoder pattern) |
| 11 | **Retry threshold** | 2 Coder attempts before Editor intervention |
| 12 | **Language** | Prompts in English, user communication in user's language |
| 13 | **Launch** | MVP first (5 components), then expand |
| 14 | **Memory scope** | Hybrid — global plugin + project-level overrides |
| 15 | **Agent files** | Static `.agent.md` files (editable by Editor), invoked via `define_subagent` |
| 16 | **Modification style** | Hybrid: additive rules autonomously, full rewrite with approval |
| 17 | **Drift protection** | CTD + Schema Handoff + Validation Checkpoints + No Telephone Game + Drift Detection |
| 18 | **Target language** | Node.js / TypeScript |
| 19 | **Execution Mode** | Support for autonomous and `/step` (interactive approval) modes |
| 20 | **Knowledge Base** | RAG integration for React/Next.js to provide context to Coder/Architect |

---

## Proposed Changes

### Plugin Structure

```
agentogenesis/                          # Plugin root
├── plugin.json                         # Plugin manifest
├── GEMINI.md                           # Global system context
│
├── agents/                             # Agent definitions
│   ├── coder.agent.md                  # 💻 Coder agent
│   ├── tester.agent.md                 # 🧪 Tester agent
│   ├── reviewer.agent.md               # 🔍 Reviewer agent
│   └── editor.agent.md                 # ✏️ Editor agent (self-improvement)
│
├── skills/
│   └── orchestrate/
│       ├── SKILL.md                    # 🧠 Orchestration skill (main workflow)
│       └── templates/
│           ├── ctd-template.md         # Canonical Task Document template
│           └── handoff-template.md     # Structured handoff template
│
├── rules/
│   ├── safety-guardrails.md            # Safety rules for Editor
│   └── coding-standards.md            # Default coding standards
│
└── memory/
    ├── error-log.md                    # Error journal
    ├── improvements.md                 # Improvement history
    ├── metrics.md                      # Aggregated metrics
    └── lessons-learned.md             # Distilled lessons
```

---

### Component 1: Plugin Manifest ✅

#### [NEW] plugin.json

Standard Antigravity plugin manifest with name, version, description.

---

### Component 2: Global Context ✅

#### [NEW] GEMINI.md

Global system instructions that apply to all agents:
- System identity: "AgentOGenesis — self-improving multi-agent development team"
- Language rules: prompts in English, communicate with user in their language
- Inter-agent communication: always in English
- CTD protocol: every task starts with Canonical Task Document
- Safety: max retry limits, no infinite loops
- Memory: agents must log errors and read lessons before starting work

---

### Component 3: Orchestration Skill ✅

#### [NEW] skills/orchestrate/SKILL.md

The main orchestration workflow. Activated when user asks to write, test, or review code.

**Execution Modes:**
- **Autonomous Mode** (default): Runs the workflow below seamlessly without user interruption.
- **Interactive Mode** (`/step` command): The orchestrator pauses before invoking the next agent and explicitly asks the user for confirmation (e.g., "Ready to invoke Tester. Proceed?").

**Workflow (TDD-first):**

```
1. Parse user request → Create CTD (Canonical Task Document)
2. [MANDATORY] Invoke Tester → write tests from CTD spec (NO access to code!)
3. [MANDATORY] Invoke Coder → write code to pass tests (receives CTD + test specs)
4. [MANDATORY] Run tests → check pass/fail
5. [IF FAIL] → Return to Coder with feedback (attempt 2)
6. [IF FAIL AGAIN] → Invoke Editor to analyze + improve Coder prompt
7. [MANDATORY] Invoke Reviewer → quality check against CTD
8. [IF REJECTED] → Return to Coder with review feedback
9. [FLEXIBLE] Invoke other agents as needed (Researcher, Documentator)
10. Validation checkpoint: verify result matches original CTD
11. Update memory (error-log, metrics)
```

#### [NEW] skills/orchestrate/templates/ctd-template.md

Canonical Task Document template with XML structure:
```xml
<task id="{{uuid}}">
  <original_request>{{verbatim user request}}</original_request>
  <requirements>
    - {{requirement 1}}
  </requirements>
  <constraints>
    - {{constraint 1}}
  </constraints>
  <context>
    - {{relevant project info}}
  </context>
</task>
```

#### [NEW] skills/orchestrate/templates/handoff-template.md

Schema-based handoff template:
```xml
<handoff from="{{agent}}" to="{{agent}}">
  <artifacts>
    <file path="{{path}}" action="created|modified"/>
  </artifacts>
  <key_decisions>{{decisions made}}</key_decisions>
  <concerns>{{potential issues}}</concerns>
  <ctd_compliance>{{checklist of CTD requirements covered}}</ctd_compliance>
</handoff>
```

---

### Component 4: Coder Agent ✅

#### [NEW] agents/coder.agent.md

**Role**: Write production-quality TypeScript/Node.js code.

**Key instructions**:
- Receives CTD + test specifications (written by Tester)
- **Knowledge Base (RAG)**: Must consult provided RAG systems when working with React, Next.js, or complex frameworks to get up-to-date documentation and project-specific architecture context.
- Goal: make all tests pass
- Must follow coding standards from `rules/`
- Output: code files + structured handoff

---

### Component 5: Tester Agent

#### [NEW] agents/tester.agent.md

**Role**: Write comprehensive tests BEFORE code exists. TDD-first approach.

**Key instructions**:
- Receives ONLY CTD (NO code access!)
- Context isolation from Coder (prevents "green-by-design" tests)
- Tests edge cases, error handling, types

**Critical rule**: `You MUST NOT have access to the implementation code. You write tests based ONLY on the specification (CTD).`

---

### Component 6: Reviewer Agent

#### [NEW] agents/reviewer.agent.md

**Role**: Code review for quality, style, patterns, security. Last quality gate.

**Key instructions**:
- Receives CTD + code + test results
- Checks: CTD compliance, code quality, TypeScript best practices, security
- **Drift Detection**: final check that code matches ORIGINAL request
- Output: APPROVE or REJECT with structured feedback

---

### Component 7: Editor Agent (Self-Improvement Engine)

#### [NEW] agents/editor.agent.md

**Role**: Analyze errors and improve agent prompts. The self-improvement engine.

**Two-level decision model**:

| Change Type | Autonomy | Action |
|-------------|----------|--------|
| **Bug fix** (pattern-based error) | Autonomous | Add rule to `<!-- LEARNED_RULES -->` section |
| **Optimization** (prompt restructuring) | Requires approval | Present proposal artifact with justification |

**What Editor CANNOT modify autonomously**:
- `skills/orchestrate/SKILL.md` — orchestration logic (approval required)
- `GEMINI.md` — global system context (approval required)
- `editor.agent.md` — itself! (approval required to improve its own prompt)

---

### Component 8: Memory System

#### [NEW] memory/metrics.md

Aggregated performance metrics. Maintained completely by the Editor agent. To ensure accuracy, the Editor outputs metrics in a strict markdown schema, analyzing the `error-log.md` autonomously.

---

## Drift Protection Mechanisms

Built into the orchestration skill and agent prompts:

| Mechanism | Where | How |
|-----------|-------|-----|
| **Canonical Task Document** | `skills/orchestrate/` | Created once, passed to ALL agents verbatim |
| **Schema Handoff** | Agent prompts | Structured XML format for inter-agent communication |
| **Validation Checkpoints** | Orchestrator skill | After each step, verify against CTD requirements |
| **No Telephone Game** | Agent prompts | Rule: "Never rephrase the task. Reference the CTD" |
| **Drift Detection** | Reviewer | Final check: "Does code match the ORIGINAL request?" |

---

## Resolved Architecture Decisions

> [!TIP]
> **Project-level overrides**: Implemented using a layered approach. The plugin lives globally in `~/.gemini/config/plugins/agentogenesis/`, but it reads project-specific rules from the local project's `.agents/agentogenesis/` or `AGENTS.md` to merge with the global context.

> [!TIP]
> **Editor self-modification**: The Editor is allowed to propose improvements to its OWN prompt (`editor.agent.md`), but cannot apply them autonomously. It must request user approval.

> [!TIP]
> **Interactive Workflow**: A `/step` command is added to force the orchestrator to request explicit user permission before transitioning between any agents (e.g., from Tester to Coder). When run normally, the orchestration is fully autonomous.

> [!TIP]
> **RAG Integration**: Code generating agents are instructed to leverage available RAG systems (like Next.js/React context) as their primary knowledge base during the coding phase.
