---
name: genie-agent-builder
description: Use this skill when the user wants to build, assess, or design a Databricks Genie Agent. Triggers on short phrases like 'create genie', 'build genie', 'new genie agent', 'genie agent for [domain]', or longer requests specifying source schemas, curated views, metric views, examples, benchmarks, and configuration.
---

## CRITICAL EXECUTION RULE

When Genie Agent configuration tools are available (createAsset, addTablesToSpace, updateSpaceConfig, addStarterQuestions, addInstructionsToSpace, addKnowledgeSnippetsToSpace, updateTableDescription, configureEntityMatching, updateColumnSynonyms, addBenchmarks), you MUST execute the full build end-to-end using those tools. Do NOT present configuration as text recommendations. Do NOT stop after creating the Genie Agent. Do NOT pause to summarize and ask permission to continue.

"Build a Genie Agent" = execute all of these in one continuous pass:
1. executeCode — create schema (if needed) and curated SQL objects (views, metric views)
2. createAsset (genie) — create the Genie Agent WITH tableIdentifiers pointing to the curated objects
3. updateSpaceConfig — set description with business rules, glossary, scope boundaries
4. addStarterQuestions — add 6-10 clickable prompts
5. addInstructionsToSpace — glossary (text) + 5-8 SQL examples (sql_example)
6. addKnowledgeSnippetsToSpace — joins, filters (sql_filter), derived columns (sql_derived), measures (sql_measure)
7. updateTableDescription — business description for each dataset
8. configureEntityMatching — enable on categorical columns (status, type, category, market, priority, measure_code)
9. updateColumnSynonyms — alternative search terms for key columns
10. addBenchmarks — 10-15 questions with expected SQL

If ANY step is skipped, the Genie Agent is incomplete and the user will have to fix it manually. This defeats the purpose of the skill.

## Quick Start

When the user provides a short trigger phrase without full details (e.g. "create genie", "build genie", "new genie agent"), respond with EXACTLY the following template — do NOT paraphrase, shorten, or reword it:

---
**To build a Genie Agent, I need:**

1. **Domain** — what business area? (e.g. HEDIS quality, claims analytics, member engagement)
2. **Source schema** — where is the raw data? (e.g. `catalog.schema`)
3. **Target schema** — where should curated views go? (e.g. `catalog.curated_schema`, or "same schema" if writing back is OK)

**Copy and fill in this prompt:**

```
Build a Genie Agent for [DOMAIN]. Source schema: `[catalog.schema]`. Create curated views and a metric view in `[catalog.curated_schema]` (create the schema if needed). Execute the full build — schema, views, Genie Agent with tables attached, space config, starter questions, glossary, SQL examples, knowledge snippets (joins, filters, measures, derived columns), table descriptions, entity matching, column synonyms, and benchmarks. Do not stop or summarize mid-build.
```

---

The example prompt MUST be shown inside a code block exactly as written above, with [DOMAIN], [catalog.schema], and [catalog.curated_schema] as the only placeholders for the user to fill in. Do NOT shorten it. Do NOT omit any of the listed deliverables. The full list of deliverables in the prompt acts as a contract — if any are missing from the prompt, the agent may skip them.

If the user provides all three inputs in their initial message (even briefly), skip the template and proceed directly to Step 1.

# Genie Agent Builder

Use this skill when the user wants to:
- build a new Databricks Genie Agent
- assess whether one or more source schemas or tables are ready for a Genie Agent
- choose the right datasets for Genie Agent exposure
- create curated views, metric views, or derived tables for a Genie Agent
- generate a reusable prompt for building Genie Agents
- create business glossary terms, dataset descriptions, instructions, examples, or benchmarks for a Genie Agent
- produce and execute a complete Genie Agent configuration using available tools (preferred) or paste-ready content (fallback only when tools are unavailable)

Do not use this skill for:
- generic SQL help with no Genie goal
- simple schema documentation with no semantic modeling task
- dashboard-only requests unrelated to Genie
- non-Databricks coding tasks

## Canonical references

Always use these files from this skill:
- [Genie meta template](./references/genie-meta-template.md)

This skill uses the canonical template above as the base structure for all Genie Agent build prompts and design outputs.

## How to use the canonical template

When the user asks to build or design a Genie Agent:
1. Read [Genie meta template](./references/genie-meta-template.md).
2. Preserve its phase structure and final deliverable sections unless the user explicitly asks for a shorter format.
3. Replace the placeholders with the user’s supplied values.
4. Add domain-specific expectations only where they materially improve trust, usability, business coverage, or guardrails.
5. When Genie configuration tools are available, execute the build directly (see CRITICAL EXECUTION RULE above). When tools are not available, return either:
   - a filled execution-ready prompt, or
   - a practical Genie Agent design/build plan, depending on the user request.

## Required inputs

Extract these when available:
- domain name
- source locations for read access
- whether curated assets need to be created
- approved write location, if any
- whether writing to one of the source schemas is allowed
- business context
- compliance constraints
- naming convention
- intended users or personas, if stated
- preferred benchmark coverage, if stated
- preferred Genie Agent name, if stated

Accept source locations for read in these forms:
- schema-level scope using `catalog_name.schema_name`
- table-level scope using `catalog_name.schema_name.table_name`
- a mix of schema-level and table-level scope when the user wants narrower control

Examples:
- `catalog_name.schema_a`
- `catalog_name.schema_b`
- `catalog_name.schema_c`
- `catalog_name.schema_a.table_x`
- `catalog_name.schema_b.table_y`

Interpretation rules:
- A schema-level source location means that schema is in scope for discovery and assessment.
- A table-level source location means scope should be limited to the specified objects.
- Source locations may span multiple schemas.
- Read scope and write location are separate concepts.

Approved write location is optional for read-only Genie Agent design.
It is required only if the solution needs new curated views, metric views, or derived tables.

If new assets are needed, they may be created in:
- a dedicated curated schema, or
- one of the source schemas, if writing there is explicitly allowed

Do not assume write access to any source schema unless confirmed.

## Operating procedure

### Step 1 — Identify the request
Classify the user request as one or more of:
- build a Genie Agent
- assess Genie readiness
- generate a reusable Genie Agent build prompt
- generate implementation assets such as SQL, dataset descriptions, instructions, glossary, examples, or benchmarks
- produce a UI-ready configuration pack for the Genie Agent

### Step 2 — Normalize the inputs
Convert user input into this structure:

- Genie Agent name: `<preferred_agent_name or generated_name>`
- Domain: `<domain>`
- Source locations for read: `<list>`
- Create curated assets: `<yes/no>`
- Approved write location: `<catalog_name.schema_name or null>`
- Write mode: `<NO_WRITES | WRITE_TO_APPROVED_SCHEMA | WRITE_TO_SOURCE_SCHEMA_IF_APPROVED>`
- Business context: `<business_context>`
- Compliance constraints: `<compliance_constraints>`
- Naming convention: `<naming_convention>`
- Intended users/personas: `<personas or null>`
- Preferred benchmark coverage: `<coverage expectations or null>`

Normalization rules:
- If the user provides multiple schemas, preserve them as a list.
- If the user wants only read-only Genie Agent configuration, set `Create curated assets = no`.
- If curated assets are needed and an approved writable schema is provided, use it.
- If curated assets are needed and no writable schema is provided, ask for one or recommend one.
- Only use `WRITE_TO_SOURCE_SCHEMA_IF_APPROVED` when the user clearly confirms that one of the source schemas is writable for new assets.
- If the user does not provide a Genie Agent name, generate one using the naming convention and domain.
- If the user asks for a UI-ready result, ensure all Genie Agent configuration sections are explicitly drafted.

### Step 3 — Load the canonical template
Read and use [Genie meta template](./references/genie-meta-template.md).

Do not invent a different structure unless the user explicitly asks for a shorter or simplified version.

### Step 4 — Apply domain reasoning
Act like a domain SME before recommending datasets.

For the stated domain:
- identify likely core entities
- identify likely facts and dimensions
- identify likely grains
- identify likely business KPIs and measures
- identify common business questions
- identify likely semantic traps, governance issues, or compliance concerns
- identify likely user personas and what they will expect from Genie

If the domain is healthcare, payer, provider, HEDIS, claims, enrollment, risk, quality, or utilization:
- use appropriate business terminology
- prefer surrogate keys over direct identifiers
- explicitly flag PHI/PII concerns
- pay attention to denominator, numerator, exclusions, attribution, time windows, and measurement period logic where relevant
- define scope boundaries clearly so the Genie Agent does not answer outside its data coverage

### Step 5 — Assess source-data fitness
Assess all supplied source locations for Genie Agent readiness, including cross-schema read scenarios.

For relevant tables, evaluate:
- business relevance
- grain clarity
- join reliability across and within schemas
- key integrity
- duplicate risk
- nulls in critical columns
- freshness and recency
- valid domain values
- sensitive data exposure
- metric interpretability
- whether the data can be safely exposed directly or needs curation first
- whether the object is suitable for end-user self-service

For each relevant table, assign:
- `USE`
- `USE WITH CAUTION`
- `DO NOT USE`

### Step 6 — Design the smallest useful curated layer
Recommend the minimum set of datasets needed for trustworthy business Q&A.

Prefer this order:
1. curated business-friendly views
2. metric views
3. derived tables when justified
4. direct raw-table exposure only if already business-ready

When proposing new assets:
- if read-only is sufficient, do not require a write location
- if new assets are required, use the approved write location if one is provided
- the approved write location may be a dedicated curated schema or one of the source schemas
- prefer a dedicated curated schema when available for clarity and governance
- write to a source schema only if the user explicitly confirms it is allowed

For each proposed asset, define:
- object name
- object type
- purpose
- grain
- source lineage
- join logic summary
- business-friendly columns
- exclusions or masking
- reason the asset belongs in the Genie Agent

Do not expose every table just because it exists.

### Step 7 — Validate using representative questions
Generate representative business questions for the domain.

For each question:
- map it to the proposed assets
- identify needed joins, filters, and metrics
- mark coverage as full, partial, or unsupported
- note ambiguity or caveats

If important questions are unsupported:
- revise the curated design minimally
- add only the smallest required extra asset

### Step 8 — Produce the Genie Agent completion pack
The final deliverable must include complete draft content for the Genie Agent UI sections:
- About
- Sources
- Instructions
- Examples
- Benchmarks

At minimum, provide:

#### About
- Genie Agent name
- short description
- domain summary
- common business questions
- intended audience/personas
- scope boundaries
- known limitations
- optional readiness or certification notes

#### Sources
- included datasets
- excluded datasets
- primary dataset if one exists
- rationale for each included dataset
- notes on grain, joins, and sensitive-field exclusions where relevant

#### Instructions
- general behavior instructions
- scope and boundary rules
- metric usage rules
- time-default rules
- dimension usage guidance
- ambiguity handling rules
- unsupported-topic behavior
- summary behavior guidance if needed

#### Examples
- curated example queries
- example filters if relevant
- example measures if relevant
- example fields if relevant
- example joins if relevant

Examples should reinforce intended semantics and expected answer patterns.

#### Benchmarks
- representative benchmark questions
- ground-truth SQL answers where possible
- evaluation notes
- expected answer characteristics
- a mix of common, edge-case, and boundary questions

Do not stop at a conceptual plan only.
The output should be detailed enough that a user could paste or enter it into the Genie Agent UI with minimal rewriting.

### Step 9 — Produce the final deliverable
Depending on the user’s request, return one or more of:
- a completed execution-ready Genie Agent build prompt
- a Genie Agent design/build plan
- recommended included and excluded datasets
- SQL for curated views or metric views
- validation or DQ query ideas
- dataset descriptions
- glossary terms and synonyms
- starter prompts
- example SQL
- benchmark packs
- a certification checklist

### Step 10 — Execute configuration with tools

When operating inside a Genie Agent authoring context with access to Genie
configuration tools, DO NOT present the configuration as recommendations or
paste-ready text. Execute it directly using the available tools in this order:

1. Create curated SQL objects (views, metric views) via executeCode
2. Create the Genie Agent via createAsset (type: genie) with curated table identifiers
3. updateSpaceConfig — set display name, description with business rules, glossary, scope
4. addStarterQuestions — add 6-10 clickable home-screen prompts
5. addInstructionsToSpace — add glossary (text) + 5-8 example SQL queries (sql_example)
6. addKnowledgeSnippetsToSpace — add joins, filters, derived columns, measures
7. updateTableDescription — set business-friendly description for each dataset
8. configureEntityMatching — enable on all low-cardinality categorical columns
9. updateColumnSynonyms — add alternative search terms for key columns
10. addBenchmarks — add 10-15 benchmark questions with expected SQL answers

Execution rules:
- Do not stop after creating the Genie Agent. Complete all 10 steps in one continuous pass.
- Do not pause to present a summary and ask if the user wants to continue.
- "Build" means build — not plan, not recommend, not list.
- If a tool is unavailable, fall back to producing paste-ready content as in Step 8.
- If any step fails, note the failure and continue with remaining steps.

## Response rules

Always:
- optimize for trustworthy business self-service
- when Genie configuration tools are available, execute the full configuration end-to-end rather than listing recommendations — "build" means build, not plan
- prefer curated semantic assets over raw schema exposure
- use business-friendly naming
- state assumptions clearly
- flag missing information explicitly
- flag sensitive data explicitly
- mark weak joins and weak metrics clearly
- prefer SQL-defined semantics and example SQL over text-heavy guidance
- produce UI-ready draft content when the user is trying to configure a real Genie Agent

Never:
- expose sensitive fields without warning
- recommend every table by default
- invent business definitions silently
- assume weak data is fit for Genie
- choose completeness over trust
- assume source-schema write access unless explicitly confirmed
- stop with architecture commentary if the user needs paste-ready Genie Agent content
- present configuration as a summary to review when tools are available to apply it directly
- skip any of the 10 execution steps listed in the CRITICAL EXECUTION RULE

## Example

User prompt:
`@genie-agent-builder build a Genie Agent for a domain using source schemas catalog_name.schema_a, catalog_name.schema_b, catalog_name.schema_c and use catalog_name.analytics_curated for any derived assets`

Expected behavior:
1. Read [Genie meta template](./references/genie-meta-template.md).
2. Preserve the phase structure.
3. Treat the three schemas as read sources.
4. Use `catalog_name.analytics_curated` as the approved write location for any new assets.
5. Return the completed execution-ready prompt or design plan.
6. Include UI-ready content for About, Sources, Instructions, Examples, and Benchmarks.

## Edge cases

### Multiple source schemas
If the user provides multiple source schemas:
- allow them as read inputs
- evaluate cross-schema relevance and joinability
- recommend only the minimum set of tables needed across those schemas
- do not force schema consolidation unless required for trust, performance, or usability

### Missing write location
If the user provides only read sources and wants a read-only Genie Agent:
- continue without requiring a write location

If the user wants curated views, metric views, or derived tables and no writable schema is provided:
- ask for an approved write location, or
- recommend one
- do not assume source-schema write access unless explicitly approved

### Source schema is writable
If the user explicitly confirms that one of the source schemas is allowed for new assets:
- it may be used as the approved write location
- still note that a dedicated curated schema is often cleaner for governance and discoverability

### Noisy operational schemas
If the schema set contains many raw, logging, or low-value objects:
- exclude them by default
- recommend a smaller curated layer for Genie Agent use

### Weak joins
If joins are unreliable or keys are weak:
- downgrade trust
- mark tables as `USE WITH CAUTION` or `DO NOT USE`
- avoid presenting uncertain data as business-ready

### Sensitive data
If PHI, PII, or confidential fields appear:
- recommend masking, exclusion, or surrogate keys
- do not expose them in Genie-facing assets unless explicitly justified

### Metric ambiguity
If measures or business definitions are unclear:
- create an assumptions section
- separate confirmed metrics from provisional ones
- recommend domain review before publishing

### Too many datasets
If the likely design would exceed the practical scope of a focused Genie Agent:
- reduce to the smallest high-value dataset set
- recommend pre-joined views or metric views
- avoid sprawling, low-trust configurations

## Quality bar

A strong outcome from this skill should:
- expose a small number of high-trust datasets
- answer the most important business questions for the domain
- surface major DQ and semantic risks early
- include validation logic, not just architecture ideas
- include complete draft content for About, Sources, Instructions, Examples, and Benchmarks
- produce output a data team can actually use to build and certify a Genie Agent