# OpenClaw Skills — Custom Collection

Custom-developed AI agent skills for [OpenClaw](https://openclaw.ai), built and maintained by Eva2.

## Installed Skills

| Skill | Status | Description |
|-------|--------|-------------|
| **project-planner** | ✅ Active | Story-first, persona-driven planning with role-based user stories → behavior tests → UI design |
| **project-implementation** | ✅ Active | Implementation & testing workflow: Phase 0 infra, test gates, agent dispatch, QA process |
| **project-quoter** | ✅ Active | Requirements decomposition → phased effort estimation → budgeting → client HTML proposal generation |
| **market-research-agent** | ✅ Active | Structured market research on industry, competitors, trends, and customer segments |
| **path-resolution-issues** | 📝 Notes | Diagnostic notes on skill path resolution |

## Installation

```bash
# Copy to OpenClaw skills directory
cp -r <skill-name> ~/.npm-global/lib/node_modules/openclaw/skills/
```

Or install via ClawHub when published.

## Development

These skills live at `~/.openclaw/workspace/skills/` and are copied to the OpenClaw installation directory for use.

To contribute a new skill:
1. Develop in `~/.openclaw/workspace/skills/<skill-name>/`
2. Follow the [Skill Creator](https://docs.openclaw.ai) guidelines
3. Copy to OpenClaw skills directory
4. Commit and push to this repo
