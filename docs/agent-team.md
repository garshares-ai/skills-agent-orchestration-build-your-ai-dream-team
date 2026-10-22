# Agent team

## Mona's Project Pulse dashboard

GitHub Copilot CLI in a Codespace will orchestrate this work, coordinating the custom agents below.

| Agent | Target model | Responsibility | Definition |
| --- | --- | --- | --- |
| **Orchestrator** | Claude Opus 5.5 (copilot) | Breaks the dashboard work into phases, delegates scoped tasks to the specialists, coordinates dependencies, and verifies the integrated result. | `.github/agents/orchestrator.agent.md` |
| **Planner** | Claude Opus 5.5 (copilot) | Researches the repository and requirements, then produces an implementation plan with file assignments, dependencies, edge cases, and validation expectations. Does not write code. | `.github/agents/planner.agent.md` |
| **Designer** | Gemini 3.7 Flash (copilot) | Shapes the dashboard's UX, accessibility, information hierarchy, responsive behavior, and visual styling, including clear project cards and status/priority treatments. | `.github/agents/designer.agent.md` |
| **Coder** | GPT-5.5 (copilot) | Implements the assigned application logic and runnable-app support, following existing patterns and validating the changes. | `.github/agents/coder.agent.md` |
