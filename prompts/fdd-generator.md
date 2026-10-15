# FDD Generator Prompt

## Objective

Create or improve `docs/FDD.md`: a technical, clear and actionable **FDD (Feature Design Doc)** that details **how to implement** the feature.

The FDD is the most technical document in the package. It must be actionable enough for a developer to pick it up and start coding, without going back to the meeting to clear up doubts.

The FDD is derived from the **ADRs and the RFC already generated**, complemented by the meeting transcript and the existing code. There is no interview: all information comes from the repository.

The FDD does not repeat the business narrative of the PRD or the argumentation of the RFC (rejected alternatives, open questions). It starts from the decisions already closed and focuses on the verifiable technical behavior of the feature.

The final FDD must be rendered in the format defined in **"FDD skeleton (output template)"**.

**Language:** write the FDD in the predominant language of the existing ADRs and RFC, section headings included. The skeleton below is in English; translate its headings and labels into the document language. When the project instructions name the required sections, use those exact names so the document can be checked against them.

---

## Role

You are an assistant specialized in **FDDs**.

Your role is to:

- Read the repository sources before writing anything.
- Turn the decisions recorded in the ADRs and the proposal in the RFC into a concrete implementation design.
- Anchor the design in the existing code, pointing to real files and how each one will be extended or reused.
- Consolidate everything into a standardized technical document that allows implementation without ambiguity and objective validation.

---

## Sources of Information

Read, in this order, before creating or changing any file:

1. **Project instructions** (`CLAUDE.md`, `README.md`): FDD requirements, acceptance criteria, traceability conventions and restrictions (for example, not modifying the application code).
2. **All ADRs** in `docs/adrs/`: status, decision, rationale, consequences and cross-references. An accepted ADR is a closed decision and the FDD must implement it as is.
3. **The RFC** in `docs/RFC.md`: solution overview, components, risks and open questions.
4. **The transcript** `TRANSCRICAO.md`, in full: implementation details agreed in the meeting that did not reach the ADRs or the RFC (numeric values, field names, formats, edge-case behavior).
5. **The existing code** (`src/`, `prisma/`, `tests/`): the transactional flow where the feature fits, module patterns, errors, authentication, validation, logger, configuration and database schema.
6. **The current `docs/FDD.md`**, if it already has real content.

Precedence in case of conflict: the transcript and the code are the ground truth; no statement in the FDD may contradict them. Among the derived documents, an accepted ADR wins over the RFC. If an ADR or the RFC contradicts the transcript or the code, do not copy the error: follow the transcript or the code and flag the divergence in the final report so the derived document can be fixed. If the conflict cannot be resolved safely, flag it instead of silently picking a side.

---

## Principles

- **Nothing invented.** Every statement must be traceable to an ADR, the RFC, the transcript (timestamp and speaker, for example `[09:17] Diego`) or a real file path in the code.
- **Labeled hypotheses.** An FDD needs details the meeting may not have defined (the exact name of a metric, the format of a field, the status code of an edge case). When the detail is not in the sources, derive it from the pattern already present in the code, mark it as **(hypothesis)** in the document language, and cite the file whose pattern it follows (for example "(hypothesis, following `src/shared/errors/http-errors.ts`)") so the Tracker can map it to `CODIGO`. Do not present a hypothesis as a decision. Keep pure hypotheses, with no code pattern behind them, to the minimum the required sections need.
- **Consistency with the ADRs and the RFC.** Use the same names for components, tables, events and parameters. Do not contradict an accepted decision.
- **Open questions in the RFC stay open.** Do not resolve them in the FDD. Indicate where they affect the design and which behavior applies until they are decided.
- **Project patterns first.** New endpoints, errors, validations and configuration follow the conventions already present in the code (module layers, `AppError` subclasses, Zod schemas, pagination envelope, authentication and roles).
- **Verified file paths.** Only cite as existing the files that exist. Check each path before writing it. Files the feature will create (for example under `src/modules/webhooks/`) must be marked explicitly as new wherever they are cited (for example "(novo)" or "a criar"), and listed under "New files and tables". A reader must never mistake a new path for an existing one.
- **Do not modify** application code, ADRs, the RFC or other documents. The only output is `docs/FDD.md`.
- **Repeatable execution.** Running this prompt again on unchanged sources must not duplicate sections or reword what is already correct. See "Create or review" below.
- Use simple, direct technical language.
- **Do not use em dashes ("—").**

---

## Create or review

Decide first what state `docs/FDD.md` is in:

- **Missing, empty or a placeholder stub** (a heading and a TODO comment): generate the whole document from the sources.
- **Has real content:** review the entire document against the sources and refactor it in place to be specific and actionable. Do not just append. Look for:
    - **Generic statements.** For each sentence, ask: could it appear unchanged in the FDD of a different feature? If yes ("errors are handled gracefully", "the system logs relevant events", "retries are used on failure", "a monitoring dashboard will be created"), rewrite it with the concrete value, component, file, field or citation the sources support. Typical rewrites: "retries with backoff" becomes the exact schedule and attempt limit from the ADR; "relevant logs" becomes the log events with their fields; "integrates with the orders module" becomes the file, method and transaction boundary.
    - **Steps a developer could not code from:** flows without numbered steps, transaction boundaries or state changes; endpoints without example payloads or status codes; error codes without trigger and handling.
    - **Contradictions** with an accepted ADR or the RFC, and numbers that differ between documents.
    - **Unlabeled hypotheses** and file paths that do not exist.
    - **Missing required sections**, endpoints, error codes or integration points.
    - **Duplicated** content and RFC-level argumentation (rejected alternatives, debate) that belongs in the RFC.

  Preserve valid feature-specific content, and do not change the meaning of a decision just to shorten the text.
- **Already accurate, specific and complete:** make no changes and report "no changes needed".

---

## Required Sections

The FDD must contain, at minimum, the sections below:

- **Technical context and motivation**
- **Technical objectives**
- **Scope and exclusions**
- **Detailed flows** (event creation in the outbox, processing by the worker, retry, DLQ)
- **Public contracts** (HTTP endpoints with example payloads, headers, status codes, semantics)
- **Expected error matrix** with codes in the `WEBHOOK_*` pattern
- **Resilience strategies** (timeouts, retries, backoff, fallback)
- **Observability** (metrics, logs, tracing)
- **Integration with the existing system**
- **Dependencies and compatibility**
- **Technical acceptance criteria**
- **Risks and mitigation**

---

## Elaboration Process

1. **Technical context and motivation**
    - Which technical problem the feature solves and which gap exists in the code today
    - How it fits the architecture proposed in the RFC and the existing systems
    - Actors, assumptions and explicit constraints
    - Summarize in a few lines and point to the RFC and the ADRs instead of repeating the argumentation
2. **Technical objectives**
    - Measurable technical results, with the numbers defined in the sources
    - Guarantees and invariants that must hold (consistency, delivery semantics, ordering, security)
3. **Scope and exclusions**
    - What is included in this delivery
    - What was explicitly discarded or deferred in the meeting
4. **Detailed flows**
    - One flow for each of these, with numbered steps: **event creation in the outbox**, **processing by the worker**, **retry**, **DLQ**
    - In each step, indicate where validation, persistence, transaction boundaries and external calls happen
    - Include the event states and the transitions between them
    - Use Mermaid diagrams (sequence, state) when they help readability
5. **Public contracts**
    - **At least 4 HTTP endpoints**, each with: method and route, authentication and required role, headers, **request example**, **response example**, success and error **status codes** with the semantics of each
    - The contract of the webhook delivered to the client: event payload, headers sent, signature, what the client must respond and how it should handle duplicates
    - Field validation rules, limits and pagination when applicable
6. **Expected error matrix**
    - Table with `WEBHOOK_*` code, HTTP status, triggering condition and handling
    - Each error follows the project's `AppError` subclass pattern, with the same response format already used by the error middleware
    - Cover errors of the management API and delivery failures handled by the worker
7. **Resilience strategies**
    - Timeouts, retry policy, backoff table, criterion for sending to the DLQ and reprocessing
    - Fallback policy and behavior when the worker itself fails (crash, restart, concurrency between instances)
    - Invariants that can never be violated
    - Configurable parameters with their default values
8. **Observability**
    - **Metrics**: name, type, labels and what each one validates
    - **Logs**: structured format, essential fields and data that must not be logged
    - **Tracing**: main spans, correlation propagation between the request that generates the event and the delivery, sampling
    - Minimum alerts and dashboards
    - The three subsections (metrics, logs and tracing) are mandatory. If the sources do not cover one of them, propose the minimum coherent with the project and mark it as a hypothesis
9. **Integration with the existing system**
    - **At least 4 real file paths** from the code base
    - For each one: what exists today and how the webhooks module integrates (what is extended, what is reused, what is created alongside)
    - Cover at least: the status-change transactional flow, the error classes, authentication and roles, validation, route and controller wiring, logger, environment configuration and database schema
    - List the new files and tables following the module structure already adopted
10. **Dependencies and compatibility**
    - Required components, libraries and infrastructure, with version when relevant
    - Impact on existing interfaces, database migrations and compatibility guarantees
11. **Technical acceptance criteria**
    - Objective, testable checklist (functional, performance, resilience, security, observability)
    - Numeric targets when the sources define them
12. **Risks and mitigation**
    - Technical implementation and operation risks, with probability and impact
    - **Mitigations may have multiple sub-items**
    - Contingency plan when applicable
    - Stay coherent with the risks in the RFC, going down to implementation level

---

## FDD skeleton (output template)

The final output must follow this Markdown, with headings and labels translated into the document language:

````markdown
# FDD: [feature name]

Version: [version]
Date: [date]
Owner: [technical owner, or "not specified"]
Related documents: [links to the RFC and the ADRs]

---

## 1. Technical context and motivation
[technical problem, gap in the current code, fit with the RFC architecture, actors, assumptions and constraints]

---

## 2. Technical objectives
- [objective 1 with measure/invariant]
- [objective 2 with measure/invariant]

---

## 3. Scope and exclusions

**Included**
- [item 1]
- [item 2]

**Excluded**
- [item A, with the source of the rejection or deferral]
- [item B, with the source of the rejection or deferral]

---

## 4. Detailed flows

### 4.1 Event creation in the outbox
1. [step 1]
2. [step 2]

### 4.2 Processing by the worker
1. [step 1]
2. [step 2]

### 4.3 Retry
1. [step 1]
2. [step 2]

### 4.4 DLQ
1. [step 1]
2. [step 2]

### 4.5 Event states
[state diagram and transitions]

---

## 5. Public contracts

### 5.1 [METHOD /route]
- Description: [what it does]
- Authentication: [mechanism and required role]
- Headers: [header: meaning]
- Validations: [field rules]

**Request example**
```json
{}
```

**Response example**
```json
{}
```

**Status codes**

| Status | Meaning |
| --- | --- |
| [2xx] | [success] |
| [4xx] | [error, with WEBHOOK_* code] |

[repeat for each endpoint, at least 4]

### 5.N Webhook delivered to the client
- Method and destination: [how the call is made]
- Headers sent: [header: meaning]
- Signature: [how it is computed and how the client validates it]
- Expected client response: [what counts as success and as failure]
- Idempotency: [how the client identifies duplicates]

**Payload example**
```json
{}
```

---

## 6. Expected error matrix

| Code | HTTP status | Condition | Handling |
| --- | --- | --- | --- |
| WEBHOOK_[...] | [status] | [when it happens] | [what the system does] |

---

## 7. Resilience strategies

- **Timeouts:** [values]
- **Retries and backoff:** [policy and interval table]
- **DLQ and reprocessing:** [entry criterion and how to reprocess]
- **Fallback:** [policy]
- **Worker failures:** [crash, restart, concurrency]
- **Invariants:** [list of critical invariants]

**Configurable parameters**

| Parameter | Default | Description |
| --- | --- | --- |
| [name] | [value] | [effect] |

---

## 8. Observability

**Metrics**

| Metric | Type | Labels | What it validates |
| --- | --- | --- | --- |
| [name] | [counter/gauge/histogram] | [labels] | [behavior] |

**Logs**
- Format and essential fields
- Data that must not be logged

**Tracing**
- Main spans, correlation propagation and sampling

**Dashboards and alerts**
- [minimum dashboard/alert]

---

## 9. Integration with the existing system

| File | What exists today | How the feature integrates |
| --- | --- | --- |
| `[real path]` | [current behavior] | [extension or reuse] |

[at least 4 real paths]

**New files and tables**
- `[new path]` (new): [responsibility]

---

## 10. Dependencies and compatibility

| Component | Minimum version | Notes |
| --- | --- | --- |
| [comp 1] | [vX.Y] | [notes] |

**Compatibility guarantees**
- [impact on existing interfaces, migrations]

---

## 11. Technical acceptance criteria

- [ ] [objective criterion 1]
- [ ] [objective criterion 2]
- [ ] [objective criterion 3]

---

## 12. Risks and mitigation

### [Risk 1]

- **Probability:** [low|medium|high]
- **Impact:** [expected impact]
- **Mitigation:**
    - [action 1]
    - [action 2]
- **Contingency plan:** [plan B]

### [Risk 2]

- **Probability:** [low|medium|high]
- **Impact:** [expected impact]
- **Mitigation:**
    - [action 1]
- **Contingency plan:** [plan B]
````

---

## Acceptance Criteria

Before finishing, validate every item below. All are mandatory.

- [ ] The file `docs/FDD.md` exists and is Markdown
- [ ] It contains all the required sections
- [ ] The "Public contracts" section includes at least 4 HTTP endpoints with example payload (request and response) and status codes
- [ ] The error matrix uses codes with the `WEBHOOK_` prefix
- [ ] The "Integration with the existing system" section references at least 4 real file paths from the code base
- [ ] The "Observability" section cites metrics, logs and tracing
- [ ] No statement contradicts an accepted ADR or the RFC
- [ ] Every hypothesis is labeled as a hypothesis, and cites the code file it follows when it derives from a code pattern
- [ ] Every path to a file that does not exist yet is marked as new
- [ ] No file other than `docs/FDD.md` was changed

If a criterion cannot be met for lack of evidence in the sources, do not invent the content or declare the criterion as met.

---

## Validation and Final Report

After creating or updating the FDD:

1. Re-read `docs/FDD.md` in full.
2. Check that every cited file path exists in the repository.
3. Check that the links to the RFC and the ADRs resolve from `docs/`.
4. Check numeric values, names and states against the ADRs and the transcript.
5. Check the diff to make sure only `docs/FDD.md` changed.
6. Mechanical checks, and fix whatever fails: links resolve; at least 4 endpoint headings in the form `### 5.1 POST /route`; `WEBHOOK_` codes present; at least 4 cited code paths exist; metrics, logs and tracing all mentioned; no duplicated headings; no em dashes. If the repository has `.claude/skills/fdd-generator/scripts/check_fdd.py`, run `python3 .claude/skills/fdd-generator/scripts/check_fdd.py docs/FDD.md` from the repository root to do these checks; otherwise do them by hand. Cited paths that do not exist are allowed only for new files explicitly marked as new; a missing path cited as existing, or a new path without the marker, is an error.

Deliver a short report with:

- **Action:** FDD created, updated or no changes needed.
- **Sources consulted:** ADRs, RFC, transcript excerpts and code files used.
- **Hypotheses adopted:** list of the points marked as hypothesis.
- **Acceptance criteria:** which passed and which are blocked.
- **Pending items:** conflicts between sources and missing information.
