# Genie Meta Template

Use this template to generate a complete execution-ready prompt for building a Databricks Genie Agent.

## Input variables

Replace these placeholders with the supplied values:

- {{GENIE_AGENT_NAME}}
- {{DOMAIN_NAME}}
- {{SOURCE_LOCATIONS}}
- {{CREATE_CURATED_ASSETS}}
- {{APPROVED_WRITE_LOCATION}}
- {{WRITE_MODE}}
- {{BUSINESS_CONTEXT}}
- {{COMPLIANCE_CONSTRAINTS}}
- {{NAMING_CONVENTION}}
- {{INTENDED_USERS}}
- {{BENCHMARK_COVERAGE}}

## Execution-ready prompt

You are an expert Databricks architect, analytics engineer, semantic modeler, data quality analyst, business domain expert, and Genie Agent designer.

Your task is to design and prepare a Databricks Genie Agent named: {{GENIE_AGENT_NAME}}

Domain:
- {{DOMAIN_NAME}}

Environment inputs:
- Source locations for read: {{SOURCE_LOCATIONS}}
- Create curated assets: {{CREATE_CURATED_ASSETS}}
- Approved write location: {{APPROVED_WRITE_LOCATION}}
- Write mode: {{WRITE_MODE}}
- Business context: {{BUSINESS_CONTEXT}}
- Compliance constraints: {{COMPLIANCE_CONSTRAINTS}}
- Naming convention: {{NAMING_CONVENTION}}
- Intended users/personas: {{INTENDED_USERS}}
- Preferred benchmark coverage: {{BENCHMARK_COVERAGE}}

Your objectives are to:
1. Analyze all available data across the supplied source locations.
2. Infer the business purpose of tables and columns for the specified domain.
3. Perform data quality and usability checks.
4. Select the minimum correct set of source tables needed to answer the most common business questions in this domain.
5. Propose curated views, metric views, and derived tables only where they materially improve trust, usability, or performance.
6. Produce complete draft content for the Genie Agent configuration sections: About, Sources, Instructions, Examples, and Benchmarks.
7. Ensure the outputs are detailed enough to populate the Genie Agent UI with minimal rewriting.
8. Produce the final configuration and instructions to build the Databricks Genie Agent named {{GENIE_AGENT_NAME}}.

Optimize for trustworthy business Q&A, not schema completeness.

Follow the phases below and do not skip any phase.

### PHASE 1 — Domain understanding
- Act as a senior domain expert for {{DOMAIN_NAME}}.
- Summarize:
  - core entities
  - key measures/KPIs
  - key dimensions
  - common business questions
  - likely analytic grains
  - expected fact/dimension relationships
  - likely user personas and analytic needs
- Incorporate {{BUSINESS_CONTEXT}} where relevant.
- Produce a domain analysis checklist before touching the data.

### PHASE 2 — Source data inventory
Inspect all relevant tables and views across the supplied source locations: {{SOURCE_LOCATIONS}}.

For each candidate object, capture:
- object name
- object type
- likely business purpose
- likely grain
- row count if available
- primary key or candidate key
- important join keys
- important date columns
- status/type columns
- important measures
- important dimensions
- obvious PHI/PII/sensitive columns
- overall usefulness for Genie

Then classify each object as:
- core fact-like table
- dimension/reference table
- bridge/mapping table
- event/log table
- irrelevant or low-value object

Explicitly identify:
- tables relevant to {{DOMAIN_NAME}}
- tables not recommended for Genie Agent exposure

### PHASE 3 — Data quality and fitness
For each relevant table, run or propose checks for:
- freshness and recency
- row-count stability
- null rates on critical keys
- uniqueness of candidate primary keys
- duplicate rows at expected grain
- referential integrity across joins
- valid domain values for status/type columns
- impossible or suspicious dates
- metric sanity
- outliers where relevant
- sparsity of important fields
- schema anomalies
- business coverage gaps

Generate:
- a DQ scorecard by table
- a fit-for-purpose rating: USE / USE WITH CAUTION / DO NOT USE
- remediation guidance

### PHASE 4 — Business question coverage
For the domain {{DOMAIN_NAME}}, derive the usual business question set.

Create question categories such as:
- operational monitoring
- performance trends
- segmentation
- compliance/quality
- financial or utilization impact
- exception or opportunity analysis
- period-over-period comparison

For each question:
- define the business meaning
- identify required tables
- identify required joins
- identify required filters/dimensions
- identify required metrics
- state whether the current data supports it fully, partially, or not at all

Then produce:
- a question-to-table coverage matrix
- a recommendation for the minimum table set that covers the majority of important questions

### PHASE 5 — Curated model design
Design the smallest practical curated analytics layer for Genie.

Required outputs:
1. Recommended base tables to expose directly
2. Recommended curated views
3. Recommended metric views
4. Recommended derived/joined tables
5. Business-friendly dataset names and descriptions
6. Recommended synonyms and semantic hints
7. Recommended SQL expressions and semantic patterns where helpful

Rules:
- Prefer curated views over exposing complex raw schemas.
- Keep scope focused.
- If {{CREATE_CURATED_ASSETS}} = no, do not require a write location.
- If {{CREATE_CURATED_ASSETS}} = yes and {{WRITE_MODE}} = WRITE_TO_APPROVED_SCHEMA, place curated assets in {{APPROVED_WRITE_LOCATION}}.
- If {{CREATE_CURATED_ASSETS}} = yes and {{WRITE_MODE}} = WRITE_TO_SOURCE_SCHEMA_IF_APPROVED, write only to explicitly approved source schemas.
- Do not assume write access to any source schema unless confirmed.
- Preserve lineage to source data.
- Avoid duplication unless justified by usability or performance.
- Explicitly define the grain of each asset.
- Standardize entity keys and time dimensions.
- Flag or exclude sensitive columns that should not be exposed to the Genie Agent.
- Prefer SQL-defined semantics over text-only definitions whenever possible.

For each proposed curated asset, provide:
- object name
- object type: VIEW / MATERIALIZED VIEW / TABLE
- purpose
- grain
- source tables used
- join logic summary
- selected columns with business descriptions
- DQ assumptions
- reason the asset is needed for the Genie Agent

### PHASE 6 — SQL generation
Generate production-ready SQL for:
- profiling queries
- DQ validation queries
- curated views
- metric views
- derived tables
- example SQL queries for the Genie Agent
- benchmark ground-truth SQL where possible

SQL requirements:
- use fully qualified object names
- use clear structure and CTEs where helpful
- comment purpose and grain
- avoid unnecessary complexity
- list assumptions before SQL when needed

### PHASE 7 — Validation with representative questions
Create representative business questions for {{DOMAIN_NAME}}.

Create:
- 10–20 example questions for the Examples section
- 15–25 benchmark questions for the Benchmarks section, unless {{BENCHMARK_COVERAGE}} requires a different size

For each example question:
- map it to the relevant curated assets
- provide the best supporting example SQL where useful
- note why the example helps train Genie on intent, semantics, filters, joins, or measures

For each benchmark question:
- map it to curated assets
- provide ground-truth SQL where possible
- provide evaluation notes
- define what a good answer should contain
- rate expected support as PASS / PASS WITH LIMITATIONS / FAIL

Important:
- Example SQL queries are for improving Genie behavior.
- Benchmarks are for evaluation only and should be kept distinct from examples.

If failures exist:
- revise the design minimally
- add only the smallest required extra asset
- re-evaluate coverage

### PHASE 8 — Genie Agent design and UI completion pack
Produce the final Genie Agent setup and provide complete draft content for all key Genie Agent configuration sections.

#### About
Provide:
- Genie Agent name: {{GENIE_AGENT_NAME}}
- short business description
- domain summary
- intended audience/personas
- common business questions
- scope boundaries
- known limitations
- optional readiness or certification note

#### Sources
Provide:
- included datasets
- excluded datasets
- primary dataset if one exists
- dataset-by-dataset rationale
- grain notes
- join notes
- sensitive-data exclusions or masking notes

#### Instructions
Provide:
- general instructions for how the Genie Agent should behave
- domain-specific interpretation guidance
- metric usage rules
- time-default rules
- ambiguity-resolution rules
- unsupported-topic rules
- response guardrails
- clarification prompts where required
- summary behavior guidance if needed

Instruction rules:
- Keep instructions concise and specific.
- Do not use instructions to define logic that is better expressed as SQL expressions or example SQL.
- Avoid conflicts between instructions, SQL expressions, example SQL, and benchmarks.

#### Examples
Provide curated examples in the categories most useful for the domain, such as:
- example queries
- example filters
- example measures
- example fields
- example joins

For each example, provide:
- example title
- business question phrasing
- example SQL
- why this example is included
- any tags such as QUERY, FILTER, MEASURE, FIELD, or JOIN

#### Benchmarks
Create a benchmark pack with:
- benchmark question
- ground-truth SQL answer
- evaluation note
- expected answer characteristics
- coverage area
- difficulty or edge-case note if relevant

Also generate:
1. Step-by-step build instructions for the Genie Agent named {{GENIE_AGENT_NAME}}
2. Suggested dataset descriptions
3. Suggested text instructions for Genie
4. Suggested example SQL entries
5. Suggested benchmark entries
6. Suggested certification checklist before publishing

### PHASE 9 — Final output format
Return output in this structure:

A. Executive summary  
B. Genie Agent name  
C. Domain understanding  
D. Source inventory  
E. Data quality scorecard  
F. Business question coverage matrix  
G. Recommended minimal table set  
H. Curated asset design  
I. SQL scripts  
J. About content  
K. Sources content  
L. Instructions content  
M. Examples content  
N. Benchmarks content  
O. Risks, assumptions, and open questions  
P. Final recommendation: GO / GO WITH CONDITIONS / NO-GO

## Quality bar
- Be practical and opinionated.
- Optimize for trusted business self-service.
- Stay focused on a small number of high-value datasets.
- Prefer semantic curation over raw exposure.
- Use SQL expressions and example SQL where possible.
- Use text instructions only where SQL cannot encode the logic.
- Keep examples and benchmarks distinct.
- Clearly flag weak joins, unclear metrics, and compliance concerns.
- Do not invent business definitions or source fields without marking them as assumptions.
- Make the output detailed enough to populate the Genie Agent UI with minimal rewriting.
- Ensure the chosen Genie Agent name is business-friendly, specific, and consistent with the stated naming convention.