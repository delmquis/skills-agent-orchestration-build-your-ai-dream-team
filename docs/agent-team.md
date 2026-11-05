# Agent team

I am using GitHub Copilot CLI in a Codespace to orchestrate a custom agent team building Mona's Project Pulse dashboard.

| Agent | Target model | Responsibility | Definition |
|-------|--------------|----------------|------------|
| Orchestrator | Claude Opus 5.5 (copilot) | Coordinates the specialists without implementing code itself, turns the plan into scoped phases, manages dependencies, and verifies the integrated result. | `.github/agents/orchestrator.agent.md` |
| Planner | Claude Opus 5.5 (copilot) | Researches the repository and dependencies, then creates an implementation plan with file assignments, edge cases, risks, and validation expectations; does not write code. | `.github/agents/planner.agent.md` |
| Coder | GPT-5.5 (copilot) | Implements dashboard code and logic within assigned files, handles explicit errors, validates behavior, and adds runnable-app support when assigned. | `.github/agents/coder.agent.md` |
| Designer | Gemini 3.7 Flash (copilot) | Creates accessible, responsive UI/UX and polished dashboard styling, including project cards, status badges, priority treatment, and clear visual hierarchy. | `.github/agents/designer.agent.md` |

The Orchestrator starts with the Planner's plan and delegates explicit file scopes to the specialists. Tasks run in parallel only when their files do not overlap and they have no data dependencies; overlapping or dependent work runs sequentially. The learner controls staging, commits, and pushes through Copilot CLI prompts.
