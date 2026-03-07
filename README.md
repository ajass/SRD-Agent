# sa-agent

GitHub Copilot agents for VS Code — Solution Architecture and Enhancement Request workflows.

## Agents

### `sa` — Solution Architect

Full-lifecycle solution architecture agent. Converts source documents, builds artifact templates, maps content, and generates a completeness report.

**Quick Start:**
1. Copy `.github\agents\sa.agent.md` to your project's `.github\agents\` folder
2. Open VS Code → Copilot Chat → select `sa` from the agent dropdown
3. Answer the gate configuration prompt (`auto` or `manual`)
4. Follow Phase 1 setup, then the agent proceeds through all phases

**Phases:**
- **Phase 1** — Create folder structure, generate `scripts\convert_artifacts.py`, prompt to copy artifacts
- **Phase 2** — Scan and collect source documents from `artifacts\` and `documents\source\`
- **Phase 3** — Pre-flight checks, venv setup, install dependencies, convert documents to Markdown
- **Phase 4** — Verify and create artifact templates
- **Phase 5** — Map content to templates, generate completeness report
- **Phase 6** — Update README.md and CHANGELOG.md
- **Phase 7** — Summary and next steps

**Supported file types:** `.txt`, `.csv`, `.xlsx`, `.docx`, `.pptx`, `.pdf`, `.md`, `.vsdx` (Visio — conditional)

**Dependencies installed automatically:**
- `markitdown[docx,xlsx,pptx,pdf]` — core document conversion
- `vsdx` — only installed if `.vsdx` files are detected in `documents\source\`

---

### `er` — Enhancement Request

Streamlined workflow for enhancement requests. Captures business requirements, defines a solution path, assesses governance, and produces an actionable implementation plan.

**Quick Start:**
1. Copy `.github\agents\er.agent.md` to your project's `.github\agents\` folder
2. Open VS Code → Copilot Chat → select `er` from the agent dropdown
3. Answer the gate configuration prompt (`auto` or `manual`)
4. Provide your enhancement description or place source documents in the generated folder

**Phases:**
- **Phase 1** — Auto-generate Enhancement ID (`ER-YYYYMMDD-HHMM`), create folder structure, prompt for input
- **Phase 2** — Collect and structure business requirements, cross-reference SA artifacts if present
- **Phase 3** — Define high-level solution, assess complexity, create ADR if needed
- **Phase 4** — Intelligent governance assessment (risk, impact, security, privacy, regulatory, accessibility)
- **Phase 5** — Generate implementation plan, task files with hour estimates, runbook, update index
- **Phase 6** — Final summary

**Outputs per enhancement (`enhancements\ER-YYYYMMDD-HHMM\`):**

| File | Description |
|------|-------------|
| `requirements.md` | Business need, user stories, success criteria |
| `solution.md` | High-level approach, decisions, dependencies |
| `governance.md` | Risk, impact, and compliance assessment |
| `implementation-plan.md` | Phases, effort estimates, timeline |
| `runbook.md` | Higher-level implementation guidance |
| `tasks\task-NNN.md` | Individual task files with hour estimates |
| `SUMMARY.md` | Auto-generated summary of all artifacts |

**`enhancements\README.md`** is created and maintained as a status dashboard across all enhancement requests.

**SA Agent Integration:** If `artifacts\` is present from an SA agent run, the ER agent will automatically cross-reference architecture docs, requirements, and ADRs. It never modifies SA agent files — read-only.

---

## Decision Gates

Both agents share the same gate system. At startup, you are asked once:
- **auto** — only pause in Phase 1, proceed automatically through remaining phases
- **manual** — pause after each phase with `yes / no / skip-gates` options

Configuration is per-session and does not persist.

---

## Requirements

- Windows (PowerShell)
- VS Code with GitHub Copilot
- Python 3.8+

---

## Copy Agents to a New Project

```powershell
# SA Agent
mkdir .github\agents
curl -o .github\agents\sa.agent.md https://raw.githubusercontent.com/ajass/sa-agent/master/.github/agents/sa.agent.md

# ER Agent
curl -o .github\agents\er.agent.md https://raw.githubusercontent.com/ajass/sa-agent/master/.github/agents/er.agent.md
```

Or download directly from [GitHub](https://github.com/ajass/sa-agent).

---

## Folder Structure (Both Agents)

```
project-root\
├── .github\
│   └── agents\
│       ├── sa.agent.md          # Solution Architect agent
│       └── er.agent.md          # Enhancement Request agent
├── artifacts\                   # SA agent — strategic architecture artifacts
│   ├── requirements\
│   ├── architecture\
│   ├── diagrams\
│   ├── adr\
│   └── discovered\
├── documents\                   # SA agent — source and converted documents
│   ├── source\
│   └── processed\
├── enhancements\                # ER agent — enhancement requests
│   ├── README.md                # Status dashboard
│   └── ER-YYYYMMDD-HHMM\
│       ├── requirements.md
│       ├── solution.md
│       ├── governance.md
│       ├── implementation-plan.md
│       ├── runbook.md
│       ├── SUMMARY.md
│       ├── tasks\
│       └── source\
└── scripts\                     # Shared — Python venv and conversion script
    ├── venv\
    └── convert_artifacts.py
```

---

## Roadmap

- **ER agent — Requirements Discrepancy Report:** Automatically compare enhancement request requirements against SA agent artifacts and templates, identifying gaps, conflicts, and misalignments
