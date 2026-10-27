# Project Pulse Dashboard Implementation Plan

## Summary

Build a small, static Project Pulse dashboard that helps contributors see active projects, owners, status, recent activity, priority or risk, and a short contributor-friendly summary. The page should present this information in a polished, responsive, accessible card layout and run from the VS Code **Run Project Pulse Dashboard** launch configuration.

Use the repository brief and conventions as the source of truth. No framework or additional dependency is assumed. The app must be served over HTTP so it can load `app/project-data.json`; opening the HTML directly as a `file://` URL is not the expected runtime.

## Responsibilities and file ownership

| Agent | Responsibility | Assigned files |
|---|---|---|
| **Orchestrator** | Coordinate the agents, confirm the shared data and markup contract, enforce non-overlapping file scopes, sequence work, review integration, and report validation and blockers. Does not implement the app. | No implementation files. Coordinates `docs/project-pulse-plan.md` and reviews all outputs. |
| **Planner** | Research the repository, surface requirements and uncertainties, document phases, dependencies, file ownership, parallel work, edge cases, and validation expectations. | `docs/project-pulse-plan.md` |
| **Designer** | Set the visual hierarchy, card layout, status and priority treatments, spacing, responsive behavior, and accessibility details. Implement the agreed visual treatment without changing the app markup. | `app/styles.css` |
| **Coder** | Implement the static page and data, connect the agreed CSS hooks and data fields, create the launch configuration, and validate the runnable experience. Do not edit the Designer-owned stylesheet. | `app/index.html`, `app/project-data.json`, `.vscode/launch.json` |

The Orchestrator should give the Designer and Coder the same agreed interface before parallel implementation: the required data keys, card class name (`project-card`), dashboard class name (`dashboard`), and any additional selectors needed to style the page.

## Ordered implementation steps

### 1. Confirm requirements and shared contract

**Owner:** Orchestrator, with Planner input  
**Files:** No implementation files; plan saved at `docs/project-pulse-plan.md`

Confirm these requirements before implementation:

- The page title is **Project Pulse**.
- `app/project-data.json` has a top-level `projects` array.
- Every project has `name`, `owner`, `status`, `recentActivity`, and `priority`.
- The dashboard also provides a concise contributor-friendly summary. Unless the team decides otherwise, use an additional `summary` property rather than overloading `recentActivity`.
- The page renders data-backed project cards using the `project-card` class.
- The CSS includes `.dashboard` and `.project-card` selectors.
- The launch target serves from `app/` and opens `index.html`, not the directory root.

### 2. Create the stylesheet and project data

**Owners and files:**

- **Designer:** `app/styles.css`
- **Coder:** `app/project-data.json`

After Step 1 establishes the shared contract, these tasks can run in parallel. The Designer creates the responsive, accessible visual treatment. The Coder creates representative, valid project data using the required fields and the agreed summary approach.

The stylesheet should provide visible cards, readable spacing and typography, status badges, and clear priority or risk treatment. It should include rounded corners and shadows, while maintaining adequate contrast and usable layouts on narrow screens.

The data should include multiple representative projects. Keep values readable and useful to contributors; do not assume that status or priority values have a specific fixed vocabulary unless the team agrees to one.

### 3. Create the page and launch configuration

**Owner:** Coder  
**Files:** `app/index.html`, `.vscode/launch.json`

This step depends on the shared contract and the availability of the stylesheet and data file. Build the page to reference `styles.css` and load `project-data.json`. Render each project’s name, owner, status, recent activity, priority, and summary in a `project-card`. Ensure the page structure and class hooks match the Designer’s CSS.

Create `.vscode/launch.json` as strict JSON with no comments. Add a configuration named **Run Project Pulse Dashboard** that serves from `${workspaceFolder}/app` using `python3 -m http.server 5500` and opens `http://localhost:%s/index.html` when the server is ready. Use a VS Code launch approach supported by the available environment; verify that its server-ready detection matches the Python server output.

### 4. Integrate and validate

**Owner:** Orchestrator coordinates; Coder validates implementation; Designer advises on visual issues  
**Files:** Review all four implementation files. Fixes remain with the file’s assigned owner.

Review the data-to-markup-to-style connection, run the app using the launch configuration, and resolve failures before handoff. If changes are needed, keep file ownership unchanged: Coder fixes HTML, JSON, or launch issues; Designer fixes CSS issues.

## Dependencies and parallel work decisions

- **Step 1 precedes implementation.** The shared contract prevents mismatched markup hooks, data fields, and styling.
- **After the contract is agreed, `app/styles.css` and `app/project-data.json` can be created in parallel.** They have separate owners and do not depend on one another.
- **`app/index.html` depends on the contract, data shape, and CSS hooks.** Implement it after those are known so its fetch logic and selectors match.
- **`.vscode/launch.json` is functionally independent of the page and data.** It may be created in parallel with the stylesheet and data, but its runtime validation must wait until the app exists.
- **Integration and runtime validation are sequential.** Validate after all four files are present.
- **Do not assign the same file to multiple agents.** The Designer owns only `app/styles.css`; the Coder owns the other three implementation files.

## Edge cases and risks

- A page opened as `file://` may fail to fetch JSON. Serve the app over HTTP as planned.
- The selected launch mechanism’s server-ready pattern may not match the installed Python version’s output. Check that VS Code actually opens the intended URL.
- Port `5500` may already be in use. If so, free the port or agree on another port and update both the configuration and expected URL consistently.
- Empty, missing, or malformed project data should not leave users with a blank dashboard; show a useful empty or load-error message if practical.
- Status, priority, activity, and summary text should remain readable when values are long or unexpected. Avoid relying on color alone to convey status or risk.
- Ensure responsive behavior and accessible structure, including readable contrast, meaningful headings, and clear text labels for status and priority.
- The brief requires a contributor-friendly summary but does not specify a `summary` key. The plan recommends adding it; confirm that choice before implementation.
- VS Code launch properties can vary by debugger or extension. Confirm the configuration is supported in the Codespace rather than assuming a browser debugger is installed.

## Validation expectations

1. Confirm `app/index.html`, `app/styles.css`, `app/project-data.json`, and `.vscode/launch.json` exist.
2. Parse the data and launch files as JSON, for example:
   - `python3 -m json.tool app/project-data.json`
   - `python3 -m json.tool .vscode/launch.json`
3. Inspect the data to confirm a top-level `projects` array and the required fields on every project.
4. Inspect the page to confirm it has the exact **Project Pulse** title, references `styles.css` and `project-data.json`, and renders data-backed cards with class `project-card` and the required visible fields.
5. Inspect the stylesheet for `.dashboard`, `.project-card`, responsive layout, readable spacing, `border-radius`, and `box-shadow`.
6. Run **Run Project Pulse Dashboard** in VS Code. Confirm that it serves the `app/` directory, opens `index.html` rather than a directory listing, loads the project data, and displays the dashboard without console or network errors.
7. Check the rendered result at a wide and narrow viewport and verify status and priority are understandable without color alone.
8. Stop the preview server after the runtime check. Report any remaining limitations or environment-dependent behavior in the Orchestrator handoff.
9. When running repository checks, distinguish the exercise-template validator and workflow keyword checks from the app’s runtime validation; passing textual or JSON checks alone does not prove that the dashboard launches correctly.

## Open questions

- Should the contributor-friendly summary be a separate `summary` property on each project, or should the data schema remain limited to the five required fields?
- Which status and priority values should be used consistently in the sample data?
- Is the preferred VS Code launch/debugger type available in the Codespace, or should the Coder select and verify a compatible option during implementation?
