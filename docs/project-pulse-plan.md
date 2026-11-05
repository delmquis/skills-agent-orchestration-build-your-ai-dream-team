# Project Pulse implementation plan

## Summary and scope

Build Mona's lightweight, static Project Pulse dashboard so contributors can quickly understand project names, owners, statuses, recent activity, priorities or risks, and short contributor-friendly summaries. The result should be a polished, accessible, responsive card-based frontend, not a bare page or server directory listing.

This document persists the Planner's implementation plan; it does not implement the dashboard. Only `docs/project-pulse-plan.md` is changed for this planning task. Do not stage, commit, or push; the learner controls Git operations.

## Repository grounding

- `.github/project-pulse-brief.md` defines the dashboard goal, static app files, and required project data fields.
- `.github/steps/2-step.md` and `.github/workflows/2-step.yml` require a saved plan with file assignments, responsibilities, dependencies, parallel decisions, and validation.
- `.github/steps/3-step.md` specifies the exact title, CSS hooks, JSON structure, and launch command and URL.
- `.github/agents/` and `docs/agent-team.md` define the Planner, Designer, Coder, and Orchestrator roles and nonoverlapping delegation rules.
- `scripts/validate-exercise.sh` checks the course template, not dashboard functionality. Course workflow text checks and JSON parsing are not substitutes for browser validation.

At review time, no existing `docs/project-pulse-plan.md` or dashboard implementation files were present. Preserve the repository's small static-app layout; no framework, build system, or additional tooling files are planned.

## Roles and explicit file assignments

| File | Exclusive implementation owner | Responsibility |
| --- | --- | --- |
| `app/styles.css` | Designer | Implement accessible, responsive dashboard styling, `.dashboard` and `.project-card` hooks, project cards, status badges, clear priority treatment, readable typography and spacing, contrast, rounded corners, and shadows. |
| `app/index.html` | Coder | Implement semantic markup and rendering logic under the approved design contract; use the exact title `Project Pulse`, link `styles.css`, load `project-data.json`, render visible project cards and fields, and handle loading, empty, and error states. |
| `app/project-data.json` | Coder | Provide agreed deterministic project samples in strict JSON with a top-level `projects` array and the required fields; implement the agreed summary representation. |
| `.vscode/launch.json` | Coder | Create strict JSON without comments for **Run Project Pulse Dashboard**, serving the app directory and opening its `index.html`. |

The Designer owns visual and accessibility decisions, but requests any necessary HTML changes from the Coder rather than editing the Coder's files. The Coder implements logic and markup without editing the Designer's CSS. The Planner identifies dependencies and risks; the Orchestrator approves contracts, delegates explicit scopes, summarizes each phase, coordinates integration, and reports blockers rather than implementing files itself.

## Ordered phases and dependencies

### Phase 1 — Agree shared contracts before implementation

1. Designer defines accessible layout, semantic structure guidance, information hierarchy, responsive behavior, and CSS hooks. Required hooks are `.dashboard` for the dashboard container and `.project-card` for each project card. Agree badge, priority, summary, loading, empty, and error-state hooks before either owner relies on them.
2. Coder defines the projects data contract: a top-level `projects` array, with `name`, `owner`, `status`, `recentActivity`, and `priority` on every project. Agree field types and validation behavior explicitly; readable strings are a proposed baseline, not an existing repository requirement.
3. Designer and Coder resolve the open questions below, including sample projects, summary representation, supported statuses and priorities, and unknown-value presentation.
4. Orchestrator approves both the design/markup contract and data contract before dependent work begins.

**Exit gate:** Both specialists can implement against the same approved structure, data shape, and presentation rules without waiting for evolving outputs from the other. Contract discussions may proceed independently, but final approval is sequential and mandatory.

### Phase 2 — Parallel implementation in nonoverlapping scopes

After Phase 1 approval, the Orchestrator may run these two workstreams in parallel:

- **Designer:** Create `app/styles.css` against the fixed markup and state hooks. Deliver responsive cards, status badges, priority styling, and accessible presentation.
- **Coder:** Create `app/project-data.json`, then implement data loading and rendering in `app/index.html` against that contract; create `.vscode/launch.json` for the approved runnable app.

Parallel work is safe only because file ownership does not overlap and shared contracts are fixed. Within the Coder's workstream, data-contract/sample decisions precede renderer verification. If either specialist needs a contract change, pause affected dependent work, obtain Orchestrator approval, and communicate it before continuing. Do not silently change another owner's assumptions.

**Exit gate:** Each specialist reports files changed, checks performed, and remaining risks. Both workstreams must finish before integrated validation.

### Phase 3 — Sequential integration and owner-scoped fixes

1. Orchestrator reviews the assembled HTML, CSS, JSON, and launch configuration together for contract consistency.
2. Verify JSON parsing, resource references, visible rendering, styling, data loading, and launch behavior as one runnable dashboard.
3. Perform browser checks at narrow and wide viewport sizes, including accessibility and agreed failure states.
4. Route fixes back to each file's exclusive owner. If fixes affect shared contracts or depend on each other, approve the revised contract first and apply dependent changes sequentially.
5. Repeat affected checks after fixes, then report the integrated outcome, validation evidence, and unresolved environment limitations.

Integration must be sequential after both implementation workstreams complete; independent file completion does not prove the dashboard works together.

## Validation expectations for later implementation

- **Content and rendering:** `app/index.html` uses the exact title `Project Pulse` in the document title and visible main heading, links `styles.css`, and loads `project-data.json`. Cards are generated from the `projects` data and visibly use the `project-card` class. Every project displays `name`, `owner`, `status`, `recentActivity`, and `priority`, plus the agreed contributor-friendly summary.
- **Design:** `app/styles.css` includes `.dashboard`, `.project-card`, `border-radius`, and `box-shadow`. The initial view is a polished dashboard with readable cards, badges, and priority treatment.
- **Accessibility and responsiveness:** Check semantic headings, readable contrast, meaningful text labels rather than color alone, keyboard access and visible focus for any controls, understandable state messages, and wrapping of long content without horizontal overflow at narrow widths.
- **Data:** Parse `app/project-data.json` as strict JSON; confirm a top-level `projects` array and all required fields on each entry. Verify agreed types, supported values, and summary representation. Suggested later parsing check: `python3 -m json.tool app/project-data.json`.
- **Launch:** Parse `.vscode/launch.json` as strict JSON without comments. It must name the configuration `Run Project Pulse Dashboard`, set `cwd` to `${workspaceFolder}/app`, run `python3 -m http.server 5500`, and use `serverReadyAction` to open `http://localhost:%s/index.html`. These command and URL details are explicitly supported by the existing Step 3 requirements. Confirm the readiness pattern captures the server port and the chosen launch mechanism is available in the learner's VS Code environment. Suggested later parsing check: `python3 -m json.tool .vscode/launch.json`.
- **Browser integration:** Run **Run Project Pulse Dashboard** and verify that the browser opens the dashboard frontend, not a directory listing; CSS and JSON load successfully; cards and all fields are visible; and no unexpected console or network errors occur.
- **Evidence:** Record what was actually checked, failures and fixes, and remaining limitations. Do not claim browser or launch success solely from static checks or `scripts/validate-exercise.sh`.

These are future implementation checks. For this documentation-only task, verify the saved plan's coverage and file scope; do not execute dashboard validations.

## Edge cases, assumptions, and open questions

| Topic | Required agreement or handling |
| --- | --- |
| Loading and request failures | Provide clear loading and error presentation for missing data, HTTP errors, or failed requests; never leave an unexplained blank dashboard. Coder owns logic; Designer owns state styling. |
| Malformed data | Agree validation and whether invalid entries reject the dataset or are skipped with an explicit message. Invalid JSON, a missing/non-array `projects` value, and missing required fields must have deterministic outcomes. |
| Empty data | An empty `projects` array should produce an understandable empty state, not fabricated cards. |
| Unknown statuses or priorities | The repository does not enumerate supported values. Agree allowed values and a neutral, text-labeled fallback for unknown values; do not assume badge mappings silently. |
| Samples and summaries | Agree sample count/content and how active projects are represented. The brief asks for short summaries but does not specify a `summary` field; explicitly approve an additional field or another representation before coding. |
| Long or unexpected text | Wrap long project names, owners, activity, and summaries. Render data as text rather than executable HTML; do not rely on color alone to convey meaning. |
| Occupied port | Keep the required default port 5500 deterministic. Report a conflict and provide a clear retry path; do not terminate unrelated processes or silently select another port. |
| Launch environment | Confirm Python 3 and the selected VS Code launch mechanism are available. Surface missing support as a blocker; obtain approval before adding tooling beyond the four assigned implementation files. |
| Codespaces forwarding | Check port 5500 forwarding and browser access in Codespaces. Distinguish environment forwarding limitations from application failures; document an appropriate forwarded `index.html` access path if automatic opening is unavailable. |

Unspecified decisions are Phase 1 approval items, not implementation facts. The baseline scope is a static dashboard; filtering, sorting, backend services, persistence, and extra tooling are not required.
