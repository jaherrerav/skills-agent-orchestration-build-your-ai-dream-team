# Agent team

We will use GitHub Copilot CLI in a Codespace to orchestrate a small custom agent team for Mona's Project Pulse dashboard.

| Agent | Target model | Responsibility | Agent definition |
| --- | --- | --- | --- |
| Orchestrator | Claude Opus 5.5 (copilot) | Coordinates the full delivery flow, delegates tasks to specialist agents, keeps work scoped, and validates the final result before handing it off. | `.github/agents/orchestrator.agent.md` |
| Planner | Claude Opus 5.5 (copilot) | Researches the repo, identifies dependencies and edge cases, and creates a practical implementation plan with file ownership and validation steps. | `.github/agents/planner.agent.md` |
| Designer | Gemini 3.7 Flash (copilot) | Shapes the dashboard experience, focusing on usability, accessibility, information hierarchy, visual clarity, and responsive layout for Project Pulse. | `.github/agents/designer.agent.md` |
| Coder | GPT-5.5 (copilot) | Implements the app logic and static frontend files, creates the supporting launch configuration, and validates the executable dashboard experience. | `.github/agents/coder.agent.md` |

## How the team will build Project Pulse

The Orchestrator is the project lead. It takes the request for Mona's Project Pulse dashboard, asks the Planner to map the work into phases, and then assigns clear file ownership to the rest of the team. The Planner researches the repository and writes a concrete plan that covers build steps, dependencies, and validation expectations.

The Designer then focuses on the UI/UX direction for the dashboard, ensuring the project cards, status indicators, hierarchy, and spacing feel like a polished Project Pulse frontend. The Coder implements the actual static dashboard files in the app and adds the supporting `.vscode/launch.json` configuration so the dashboard can run from a launch target called `Run Project Pulse Dashboard`.

Finally, the Orchestrator reviews the plan, design direction, and implementation together, checks that the app runs correctly, and reports the final outcome. This keeps the work modular while still preserving a clear end-to-end delivery flow for a runnable dashboard.
