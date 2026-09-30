# Project Pulse agent team

I will use GitHub Copilot CLI in a GitHub Codespace to orchestrate this custom agent team for Mona's Project Pulse dashboard:

| Agent | Model | Responsibility | Definition |
| --- | --- | --- | --- |
| **Orchestrator** | Claude Opus 4.7 | Coordinates the project, asks the Planner for an implementation plan, assigns specialists non-overlapping file scopes, and integrates and reports the result. | `.github/agents/orchestrator.agent.md` |
| **Planner** | Claude Opus 4.7 | Researches the repository and produces an actionable plan with phases, file assignments, dependencies, parallel work, edge cases, and validation expectations. | `.github/agents/planner.agent.md` |
| **Designer** | Gemini 3.1 Pro | Guides the dashboard's usability, information hierarchy, accessibility, responsive layout, and polished visual design. | `.github/agents/designer.agent.md` |
| **Coder** | GPT-5.5 | Implements the assigned dashboard code and supporting runnable-app configuration, following repository patterns and validating the changes. | `.github/agents/coder.agent.md` |

The Orchestrator will use the Planner's plan to define clear assignments and dependencies. The Designer and Coder can work in parallel when their file scopes do not overlap; dependent or overlapping work will happen in sequence. The Orchestrator will then integrate and verify the dashboard before handing it back.
