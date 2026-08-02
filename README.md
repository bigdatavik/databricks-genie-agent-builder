# Databricks Genie Agent Builder

A [Databricks Assistant](https://docs.databricks.com/aws/en/notebooks/databricks-assistant-faq) **User Skill** that builds a fully-configured AI/BI Genie Agent from a single prompt — curated schema, business-friendly views, a governed metric view, instructions, knowledge snippets, entity matching, synonyms, and benchmarks.

> **"Build a Genie Agent for HEDIS from `humana_payer.hedis_raw`."**
>
> …and ~90 seconds later you have a production-ready Genie Agent instead of a 10+ step manual checklist.

> **Note on naming:** Databricks renamed **Genie spaces** to **Genie Agents** (June 2026). Some Assistant tool names still use the older `...Space` convention (e.g. `updateSpaceConfig`) — those are real API identifiers and are intentionally left unchanged in the skill.

## What's in this repo

```text
databricks-genie-agent-builder/
├── README.md
└── skills/
    └── genie-agent-builder/
        ├── SKILL.md                       # operating procedure + domain reasoning rules
        └── references/
            └── genie-meta-template.md      # 9-phase canonical template for agent design
```

Everything the Assistant needs is the two files under `skills/genie-agent-builder/`. That directory maps 1:1 onto where the skill lives in your Databricks workspace.

## Installation

The skill must live at this exact path in your workspace (the Assistant auto-discovers it):

```text
/Workspace/Users/<your-email>/.assistant/skills/genie-agent-builder/
```

### Option A — Databricks CLI (recommended)

Requires the [Databricks CLI](https://docs.databricks.com/dev-tools/cli/install.html), authenticated to your workspace.

```bash
git clone https://github.com/bigdatavik/databricks-genie-agent-builder.git
cd databricks-genie-agent-builder

EMAIL="you@company.com"
DEST="/Workspace/Users/$EMAIL/.assistant/skills/genie-agent-builder"

databricks workspace import-dir skills/genie-agent-builder "$DEST" --overwrite
```

### Option B — Databricks UI

1. Navigate to your workspace home folder (`/Workspace/Users/<your-email>/`).
2. Create the directory path `.assistant/skills/genie-agent-builder/`.
3. Upload `SKILL.md` into that folder.
4. Create a `references/` subfolder and upload `genie-meta-template.md`.

### Option C — Repos (Git integration)

1. Add this repo as a Databricks **Git folder** (Repos).
2. From a notebook, copy the skill into your `.assistant/` path:

```python
import shutil

email = "you@company.com"
src  = f"/Workspace/Repos/{email}/databricks-genie-agent-builder/skills/genie-agent-builder"
dest = f"/Workspace/Users/{email}/.assistant/skills/genie-agent-builder"

shutil.copytree(src, dest, dirs_exist_ok=True)
print("Skill installed at:", dest)
```

### Verify

Open Databricks Assistant and type:

```text
Build a Genie Agent
```

If the skill loaded, the Assistant replies with a structured template asking for **domain**, **source schema**, and **target schema**.

## Usage

**Full invocation:**

```text
Build a Genie Agent for HEDIS quality analytics.
Source schema: humana_payer.hedis_raw
Target schema: humana_payer.hedis_curated
Focus: member compliance, care gaps, provider performance, Star Ratings.
```

The skill will inspect the source tables, design a curated layer, create the Genie Agent, and configure instructions, knowledge snippets, entity matching, synonyms, and benchmarks — end to end.

## Customizing

| Customization | Where to edit |
|---------------|---------------|
| Domain-specific rules | `SKILL.md` → domain reasoning section |
| Naming conventions | `SKILL.md` → input normalization |
| Compliance constraints (PHI/PII) | `SKILL.md` → operating procedure |
| Output structure | `references/genie-meta-template.md` → Phases 8–9 |
| Benchmark count | `references/genie-meta-template.md` → Phase 7 |

## License

See [`LICENSE`](LICENSE).
