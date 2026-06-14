# AgentOGenesis

Self-improving multi-agent development system built as an Antigravity Plugin.

Orchestrates **Coder**, **Tester**, **Reviewer**, and **Editor** agents with a TDD-first workflow, drift protection, and autonomous prompt improvement.

## Plugin Structure

```
agentogenesis/
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
│       ├── SKILL.md                    # 🧠 Orchestration skill
│       └── templates/
│           ├── ctd-template.md         # Canonical Task Document template
│           └── handoff-template.md     # Structured handoff template
│
├── rules/
│   ├── safety-guardrails.md            # Safety rules for Editor
│   └── coding-standards.md             # Default coding standards
│
└── memory/
    ├── error-log.md                    # Error journal
    ├── improvements.md                 # Improvement history
    ├── metrics.md                      # Aggregated metrics
    └── lessons-learned.md              # Distilled lessons
```

## Status

🚧 **MVP in development** — Steps 1–2 complete (plugin manifest, scaffold, global context).

## Author

Karen Pirumyan
