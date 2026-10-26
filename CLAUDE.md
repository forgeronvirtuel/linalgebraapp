# CLAUDE.md

Interactive linear algebra learning platform: lessons, auto-graded exercises, step-by-step explanations, visualizations, and an AI tutor grounded in an exact computation engine. See `README.md` for the vision, the concept graph, the roadmap and the architecture.

## Repository layout

- `frontend/` — React + TypeScript + Vite app, including the TS computation engine (`frontend/src/engine/`)
- `backend/` — FastAPI app, NumPy / SymPy engine, tutor orchestration
- `content/` — lessons and exercises in MDX, one folder per concept
- `infra/` — Ansible playbooks, Nginx configuration, docker compose files
- `docs/` — architecture notes and decision records (`docs/adr/`)
- `.gitlab/issue_templates/` — template for new tickets (see Workflow)

## Non-negotiable rules

- **The engine computes, the LLM explains.** No numerical result, grading decision or exercise solution may come from a language model. The tutor gets numbers only through tool calls to the engine.
- **Solutions are never written by hand.** Every exercise solution in `content/` is computed by the engine; CI recomputes and checks them.
- **Every concept declares its prerequisites** in its MDX front matter (`requires: [vectors, matrices]`). Identifiers must match existing concept folders, and the graph must stay acyclic.
- **The TS and Python engines must agree.** Any change to one engine needs a matching change and cross-tests in the other (until the open decision in the README is settled).
- **No secrets in the repository.** Use `.env` (never committed) locally and protected GitLab CI variables.

## Workflow

Work is organized as Kanban tickets in GitLab issues, shown on the project's issue board. The GitLab CLI `glab` is installed and authenticated: use it to read and update tickets. If `glab` fails (not installed, not authenticated, host unreachable), stop and say so; never guess a ticket's content.

**Board columns**, driven by labels:

| Column      | Issue state                                          |
| ----------- | ---------------------------------------------------- |
| Backlog     | Open, no status label                                |
| Ready       | Open, label `ready`                                  |
| In progress | Open, label `in-progress` (only one issue at a time) |
| Review      | Open, label `review`                                 |
| Done        | Closed                                               |

**Rules**

- **Each ticket describes the target state, not the steps.** It states what must be true once it is done, and ends with a Definition of Done: a checklist of verifiable criteria (`- [ ]`). New tickets use the `Ticket` template (`.gitlab/issue_templates/Ticket.md`).
- **Work on one ticket at a time.** Before starting, read the issue with its comments, move it to `in-progress` and restate its Definition of Done. If a criterion is ambiguous or not verifiable, ask before writing code.
- **Stay within the ticket's scope.** Anything outside it (an improvement, a bug spotted elsewhere, a refactoring idea) is proposed as a new ticket; create it in the backlog only after the developer agrees.
- **One branch per ticket**, named `<issue-number>-<short-slug>` (e.g. `12-dot-product-lesson`). Commits reference the issue (`feat: add dot product lesson (#12)`).
- **One merge request per ticket**, whose description contains `Closes #<issue-number>` so the issue closes when it is merged into `main`.
- **Keep the issue up to date**: check DoD items in the issue description as they are met, post a short comment on notable decisions or blockers, and move the issue to `review` when the merge request is ready.
- **A ticket is done only when every DoD item is checked**, CI is green and the change is deployed to staging. When finishing, report each DoD item with how it was verified.
- **To know what has already been done**, look at closed issues and merged merge requests before assuming something does not exist.

**Useful `glab` commands**

```bash
glab issue list --label ready                 # tickets ready to start
glab issue view 12 --comments                 # read a ticket and its discussion
glab issue update 12 --label in-progress --unlabel ready
glab issue note 12 -m "Decision: ..."         # comment on a ticket
glab mr create --fill --description "Closes #12"
glab issue list --closed                      # what has been done
glab mr list --merged                         # merged changes
```

When updating an issue description (to check DoD items), read the current description first and rewrite it in full, changing only the checkboxes.

## Conventions

- Everything is in English: code, comments, commits, content, documentation.
- Commits follow Conventional Commits (`feat:`, `fix:`, `docs:`, `chore:`…).
- Concept folder and file names use kebab-case (`content/dot-product/`).
- Numerical comparisons always use the shared tolerance helpers; never compare floats with `===` or `==`.
- New code in the engines and the grader comes with tests.

## Commands

To be filled in during iteration 0.

- Front end: install, dev server, tests, lint — TBD
- Back end: install, dev server, tests, lint — TBD
- Content validation (graph and solutions) — TBD
- Full stack locally: `docker compose up` — TBD

## Ask before

- Changing anything in `infra/` or `.gitlab-ci.yml`
- Adding a dependency (state why it is needed)
- Changing the concept graph structure beyond the concept being worked on
- Changing the database schema
