# Project Pulse Dashboard Implementation Plan

## Goal

Build Mona's lightweight, contributor-friendly Project Pulse dashboard as a small static app. Contributors should be able to scan project names, owners, current status, recent activity, priority or risk, and a short, useful summary. The UI should use accessible, responsive project cards and status badges with clear information hierarchy and readable spacing.

## Team and responsibilities

Use the custom agent definitions in `.github/agents/`:

- **Orchestrator** (`.github/agents/orchestrator.agent.md`) coordinates assignments, keeps file scopes explicit, manages phase transitions, integrates the work, and validates the finished dashboard. It does not implement the app.
- **Planner** (`.github/agents/planner.agent.md`) researches and creates plans only. It identifies phases, dependencies, risks, and validation; it does not write implementation code.
- **Designer** (`.github/agents/designer.agent.md`) owns visual design and `app/styles.css`. It defines the information hierarchy, polished card and badge styling, contrast, accessibility, and responsive behavior, including the shared `.dashboard` and `.project-card` hooks.
- **Coder** (`.github/agents/coder.agent.md`) owns `app/index.html`, `app/project-data.json`, and `.vscode/launch.json`. It implements semantic dashboard markup, loads and renders the data, provides valid sample JSON, and configures a runnable preview that opens the dashboard rather than a directory listing.

## Shared implementation contract

Before implementation, the Orchestrator agrees and records a contract for the HTML, CSS, data, and preview:

- `app/project-data.json` contains a top-level `projects` array. Every project has non-empty `name`, `owner`, `status`, `recentActivity`, and `priority` fields, plus a short contributor-friendly `summary`.
- HTML and CSS share the `.dashboard` and `.project-card` hooks. The markup renders the project fields, including summary, and references `app/styles.css`.
- Status and priority are understandable from visible text or labels, not color alone. Markup is semantic and keyboard-friendly; text remains readable and wraps when long.
- The preview serves from `app/`, uses `${workspaceFolder}/app` as its launch working directory, and opens `index.html` explicitly. Select a simple local serving approach compatible with the available VS Code/browser tooling; avoid unnecessary dependencies and ensure launch does not show a directory listing.

## Ordered phases and file assignments

### 1. Establish the contract

**Owner:** Orchestrator, coordinating Planner input.

Agree on the data keys and required fields, rendered content, `.dashboard` and `.project-card` hooks, accessibility expectations, and the local preview behavior before either implementation owner starts. No app files are assigned for implementation in this phase.

**Exit condition:** Designer and Coder have the same contract and understand the boundaries of their file scopes.

### 2. Implement in parallel after the contract

These tasks may run in parallel because their file scopes do not overlap. Their shared contract is the dependency that makes parallel work safe.

- **Designer — only `app/styles.css`:** Create the responsive visual system for the dashboard, project cards, status badges, and priority/risk treatment. Style the agreed `.dashboard` and `.project-card` hooks; preserve readable spacing, contrast, text wrapping, and non-color status cues across desktop and narrow screens.
- **Coder — only `app/index.html`, `app/project-data.json`, `.vscode/launch.json`:** Build semantic, keyboard-friendly markup that references the stylesheet and renders the agreed project fields from valid JSON. Include representative sample projects with all required fields. Configure the named VS Code launch preview to use `${workspaceFolder}/app` and open `index.html` through a simple compatible local-serving approach without adding unnecessary dependencies.

**Dependencies:** The contract comes first. HTML must use the CSS hooks and data schema in that contract; CSS must style those same hooks. The launch configuration must serve from the app directory and target `index.html`.

### 3. Integrate and validate sequentially

**Owner:** Orchestrator.

After both parallel tasks finish, integrate and validate the complete app. Assign any required fixes to the original owner and their existing file scope: Designer for `app/styles.css`, Coder for `app/index.html`, `app/project-data.json`, or `.vscode/launch.json`. Revalidate after fixes; do not expand or overlap ownership without agreeing a revised contract.

## Edge cases and risks

- Keep both JSON files valid strict JSON; include every required project field and avoid missing or empty values.
- Ensure long project names, summaries, and activity descriptions wrap without clipping or breaking the card layout.
- Do not communicate status or priority through color alone; use readable text labels and adequate contrast.
- Use semantic structure, keyboard-accessible content and controls, and responsive layout that remains usable on narrow screens.
- Ensure the launch URL names `index.html`; serving the directory root must not result in a directory listing.
- Keep the local preview approach compatible with the repository's available tools and do not add dependencies unless the chosen approach genuinely requires them.

## Validation expectations

The Orchestrator validates the integrated result and reports what was actually checked, along with any remaining limitations:

1. Parse `app/project-data.json`; confirm `projects` is an array and every item has `name`, `owner`, `status`, `recentActivity`, `priority`, and a short `summary`.
2. Inspect `app/index.html`: confirm it references the stylesheet, uses the shared hooks, renders the required data fields, and uses semantic, keyboard-friendly markup.
3. Inspect `app/styles.css`: confirm both hooks are styled and responsive behavior, readable contrast, spacing, text wrapping, and non-color status/priority cues are addressed.
4. Strictly parse `.vscode/launch.json` as JSON; confirm a configuration named `Run Project Pulse Dashboard`, `cwd` equal to `${workspaceFolder}/app`, and a URL that opens `index.html`.
5. Run the configured preview and inspect the dashboard at desktop and narrow viewport widths. Confirm the page displays the UI rather than a directory listing.
6. In the completion report, distinguish checks actually performed from checks not performed, and state any limitations (for example, if a browser preview or narrow-layout inspection was unavailable).

## Parallel and sequential decisions

Designer and Coder can work in parallel only after the shared contract is agreed: their implementation file scopes are disjoint, and the contract aligns HTML, CSS hooks, and JSON expectations. Contract definition is a prerequisite. Integration and validation happen sequentially after both implementations; any fixes return to the original file owner and must be revalidated.

## Open questions

None blocking. Choose a simple local serving and preview approach compatible with the available tooling, without unnecessary dependencies.
