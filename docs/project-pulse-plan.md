# Project Pulse dashboard implementation plan

## Goal

Build Mona's lightweight, static Project Pulse dashboard so contributors can scan active projects, owners, status, recent activity, and priority/risk at a glance. The dashboard must open as a UI (not a directory listing) from VS Code's **Run Project Pulse Dashboard** configuration.

## Repository context and constraints

- The dashboard brief is `.github/project-pulse-brief.md`; `.github/steps/3-step.md` and `.github/workflows/3-step.yml` define the expected app content and automated checks.
- This repository has no existing `app/` implementation or app-specific package/build setup. Keep the solution dependency-free: HTML, CSS, JSON, and Python's standard-library HTTP server.
- Keep the assigned files scoped to the four required deliverables. Do not add a framework, package manifest, or unrelated tooling.

## Responsibilities and file ownership

| Owner | Assignment | Responsibilities |
| --- | --- | --- |
| **Designer** | `app/styles.css` (exclusive edit owner) | Define a polished, responsive visual system and information hierarchy; style `.dashboard`, `.project-card`, status badges, and priority/risk treatments; ensure readable contrast, visible focus, and reduced-motion-friendly behavior. Share the class/structure contract with Coder before markup is finalized. |
| **Coder** | `app/index.html`, `app/project-data.json`, `.vscode/launch.json` (exclusive edit owner) | Build semantic dashboard markup and render the JSON data into project cards; provide loading, empty, and error states; supply representative data; create a strict-JSON VS Code launch configuration with the required name, app working directory, server command, and `index.html` browser target. Integrate Designer's agreed class hooks without editing the stylesheet. |
| **Orchestrator** | Coordination; no additional deliverable | Confirm the markup/data/style contract, delegate the bounded tasks below, resolve integration issues, and run the final checks. Keep generated changes limited to the four deliverables and this plan. |

## Dashboard structure and data behavior

- `app/index.html` should have the exact document/page title **Project Pulse**, a clear page heading and short contributor-oriented introduction, and a main `.dashboard` region containing the project list.
- Load `app/project-data.json`, whose top-level value is an object with a `projects` array. Each project must provide `name`, `owner`, `status`, `recentActivity`, and `priority`; use several realistic, concise sample projects so the card layout can be evaluated at multiple viewport widths.
- Render one `.project-card` per project. Present the project name prominently, owner as supporting information, status as readable text plus a badge, recent activity as a short summary, and priority/risk with both a text label and a distinct visual treatment. Do not rely on color alone.
- Use semantic headings and list/card structure, descriptive labels, keyboard-accessible content, and sufficient contrast. Make the grid collapse naturally on narrow screens and preserve comfortable text spacing.
- If data is loading, unavailable/invalid, or contains no projects, show a clear status message rather than a blank region or broken page. Insert data as text (not trusted HTML) and handle fetch errors explicitly.

## Ordered implementation steps and dependencies

1. **Agree on the UI/data contract — Orchestrator with Designer and Coder.** Confirm the `.dashboard` and `.project-card` hooks, card content order, badge/priority labels, and responsive expectations against the brief and workflow checks. This is a short coordination gate; no implementation files need to be shared.
2. **Build independent foundations — Designer and Coder in parallel.**
   - **Designer:** implement the stylesheet and responsive/accessibility treatments in `app/styles.css` using the agreed hooks.
   - **Coder:** create `app/project-data.json` with the required schema and representative entries, and `.vscode/launch.json` with the exact launch behavior described below.
   - These tasks are independent after the contract is set; neither edits the other's files.
3. **Implement the page and data rendering — Coder, after Step 1.** Create `app/index.html`, reference `styles.css` and `project-data.json`, and render the cards and explicit loading/empty/error states. Coder can work alongside Step 2, but must use the agreed contract and then verify the Designer's stylesheet hooks. No file ownership overlap is needed.
4. **Integrate and validate — Orchestrator with both agents as needed, after Steps 2–3.** Check required files and content, validate both JSON files, run the local preview using the launch configuration, correct any integration defects within the assigned file ownership, and confirm the browser shows the dashboard.

**Parallel vs. sequential:** Designer styling and Coder's data/launch setup can proceed in parallel once the shared UI contract is agreed. Coder's markup/rendering can also proceed in parallel with those foundations if it follows the contract, but final integration must wait for all deliverables. Runtime and cross-file checks are sequential after integration; the final launch smoke test depends on the HTML, stylesheet, and data being present.

## Launch configuration requirements

- In `.vscode/launch.json`, add a configuration named **Run Project Pulse Dashboard** using strict JSON (no comments).
- Serve the `app/` directory as the server working directory (`${workspaceFolder}/app`) with `python3 -m http.server 5500`.
- Configure `serverReadyAction` to open `http://localhost:%s/index.html`, so the browser lands on the dashboard page rather than the server's directory index.
- Stop the preview server after the smoke test.

## Validation expectations

1. Confirm all four required files exist and the page references both `styles.css` and `project-data.json`.
2. Run `python3 -m json.tool app/project-data.json` and `python3 -m json.tool .vscode/launch.json`; verify every project has all five required fields and the top-level `projects` array.
3. Inspect the markup and stylesheet for the exact page title, visible `.project-card` entries, `.dashboard` and `.project-card` styles, responsive behavior, status/activity/priority presentation, and polished card treatments including `border-radius` and `box-shadow`. These phrases/selectors are checked by `.github/workflows/3-step.yml`.
4. Review keyboard access, heading order, contrast, narrow-screen layout, and readable non-color status/priority labels. Exercise loading failure and empty-data behavior.
5. In VS Code, start **Run Project Pulse Dashboard** and verify the browser opens `index.html` at the configured server URL and displays project cards instead of a directory listing. Automated workflow checks validate file/content/JSON requirements but do not run the app, so this manual smoke test is required.
