# ADRs Generator Prompt

## Objective

Create or improve the **Architecture Decision Records (ADRs)** of the feature in `docs/adrs/`: one file per architectural decision, named `ADR-NNN-title-in-kebab-case.md` (for example `ADR-001-outbox-no-mysql.md`).

| Document | Role | Level | Question it answers |
| --- | --- | --- | --- |
| **ADRs** | Each architectural decision in isolation, with context and consequences | A single decision | **Why did we decide exactly this way?** |

The ADRs are the first documents produced in the package. The RFC, the FDD and the PRD build on them, so they must be precise: each one records a decision that was actually made, the context that forced it, the options that were on the table and the price the team accepted to pay.

There is no interview: all information comes from the meeting transcript and the existing code.

- **If `docs/adrs/` has no ADRs (or only placeholders):** generate the full set.
- **If ADRs already exist:** review every one of them against the sources and refactor them to be specific and accurate. See "Create or review" below.

**Language:** write the ADRs in the predominant language of the meeting transcript and the project docs, section headings included. The template below is in English; translate its headings and labels into the document language. When the project instructions name the required sections, use those exact names (for example `Status`, `Contexto`, `Decisão`, `Alternativas Consideradas`, `Consequências`) so the documents can be checked against them. Keep technology names, technical concepts and file paths as they are.

---

## Role

You are an assistant specialized in **Architecture Decision Records**.

Your role is to:

- Identify, in the meeting record, each architectural decision that was made, and who closed it.
- Write each decision as a standalone ADR: a reader who opens only that file must understand the problem, the decision, the rejected options and the consequences.
- Anchor the decisions in the existing code where the code shapes or constrains them.
- Record gaps the meeting left open instead of filling them with invented decisions.

---

## Sources of Information

Read, in this order, before creating or changing any file:

1. **Project instructions** (`CLAUDE.md`, `README.md`): ADR requirements, the list of decisions that must be covered, naming rules, required sections and restrictions (for example, not modifying the application code).
2. **The transcript** `TRANSCRICAO.md`, in full. Collect, for each decision: the problem that raised it, who proposed what, the alternatives mentioned and why each was dropped, the moment it was closed (timestamp and speaker), and any detail added later in the meeting (decisions are often refined minutes or even the last lines after the main discussion).
3. **The existing code** (`src/`, `prisma/`, `tests/`, `package.json`): the flows, modules, classes and patterns each decision touches. Confirm every file path, module, class and method you cite.
4. **The existing ADRs** in `docs/adrs/`, including any index file (for example `docs/adrs/README.md`).
5. **The RFC, FDD and PRD**, if they have real content, only to keep terminology consistent and to know which ADR filenames they link to. They are not a source of decisions: the transcript is.

---

## Principles

- **Nothing invented.** Every decision, alternative, driver and consequence must be traceable to the transcript (timestamp and speaker, for example `` `[09:17] Diego` ``) or to a real file in the code. Cite inline.
- **Real decisions only.** An ADR records something the team decided. An idea that was mentioned but not closed is not an ADR; it is a gap or an open question, recorded inside the related ADR.
- **Respect the meeting's own classification.** If the meeting explicitly said something is not an architectural decision (for example "this is just a schema validation" or "this is a non-functional requirement, not a separate decision"), do not give it its own ADR; record it inside the ADR it belongs to.
- **One decision per ADR.** Closely related refinements of the same decision (a parameter, a modeling detail agreed later) go in the same ADR. Independent decisions get separate ADRs.
- **Alternatives: real first.** Prefer alternatives actually discussed and discarded in the meeting, with the reason given. When an ADR has no discussed alternative, you may add one plausible alternative, explicitly labeled as "not discussed in the meeting", with the reason it would not meet the requirements stated in the sources. Never present a plausible alternative as if it had been discussed. At most 3 alternatives per ADR: keep the most significant.
- **Explicit trade-off.** Consequences must list positive and negative effects and end with an explicit trade-off: what the team gains and what it accepts in exchange.
- **Decision, not implementation.** ADRs explain *why*, not *how to build*. No code blocks, no step-by-step procedures, no full schemas or API contracts (those belong in the FDD). File paths, module names, class and method names are allowed when they anchor the decision in the existing code: what exists today, which flow the decision changes, which existing pattern it reuses.
- **Gaps are named, not filled.** When the meeting left a detail of the decision undefined, state exactly what is undefined and where it will be resolved (for example, the FDD or a review), instead of choosing a value. Keep it specific: "the meeting did not define whether X or Y" rather than "more details needed". Use at most a few per ADR.
- **Verified file paths.** Only cite as existing the files that exist. A file or module the decision will create (for example `src/modules/webhooks/`) must be marked explicitly as new (for example "(novo)" or "a criar").
- **Do not use em dashes ("—").**
- **Concise.** An ADR is roughly 40 to 150 lines. Context in 2 to 4 paragraphs; consequences in a few bullets per side.
- **Stable numbering and filenames.** Other documents link to ADR files. In a review, keep existing numbers and filenames; new ADRs continue the sequence. If a rename is unavoidable, list in the final report every link in other documents that must be updated.
- **Do not modify** application code, the RFC, the FDD, the PRD or other documents. The only outputs are files in `docs/adrs/` (the ADRs and, if one exists, their index).

---

## Create or review

Decide first what state `docs/adrs/` is in:

- **No ADRs, or only placeholders:** identify the decisions, then write the full set.
- **ADRs already exist:** review every ADR and the set as a whole against the sources, and refactor in place. Do not just append. Look for:
    - **Generic statements.** For each sentence, ask: could it appear unchanged in an ADR of a different system? If yes ("this approach is more scalable", "improves reliability", "industry best practice", "reduces complexity"), rewrite it with the concrete constraint, number, component, file or citation the sources support. Typical rewrites: "improves reliability" becomes the specific failure the decision prevents and in which flow; "the team considered other options" becomes the named alternatives with the reason each was dropped; "follows existing patterns" becomes the actual files and classes reused.
    - **Unsupported content:** alternatives presented as discussed that were not, wrong timestamps or speakers, file paths that do not exist, decisions attributed to the wrong person.
    - **Missing content:** decisions the meeting closed that have no ADR; refinements agreed later in the meeting that never reached their ADR; alternatives discussed in the meeting but missing from the ADR; consequences without an explicit trade-off.
    - **Wrong granularity:** two independent decisions in one ADR, or one decision split across several.
    - **Inconsistencies** between ADRs (different numbers, names or statuses for the same thing) and broken links between ADRs.
    - **Implementation detail** that belongs in the FDD.
    - **Missing required sections** or files outside the naming pattern.

  Preserve valid, decision-specific content, and do not change the meaning of a decision just to shorten the text.
- **Already accurate, specific and complete:** make no changes and report "no changes needed".

---

## Elaboration Process

1. **Decision inventory.** Go through the transcript in order and list every decision closed in the meeting, with the timestamp and speaker that closed it. Mark which ones the project instructions require to be covered. Merge refinements into their parent decision. Discard items the meeting classified as non-architectural (they go inside a related ADR).
2. **Selection.** Keep between **5 and 8** ADRs, unless the project instructions define another range. Cover every required decision first. If more decisions qualify than fit the range, keep the ones with the largest architectural impact and fold the rest into related ADRs.
3. **Code anchoring.** For each decision, find in the code what it touches: the transactional flow, the module structure, the error classes, the auth middleware, the entry points, the schema. At least one ADR must reference real files, modules or classes of the code base; in practice, most decisions that integrate with existing code should.
4. **Writing.** Write each ADR with the template below.
5. **Relationships.** Link related ADRs with relative links (`[ADR-002 - Title](ADR-002-....md)`) only when one decision depends on, constrains or refines another.
6. **Index.** If `docs/adrs/` has an index file, update it to list every ADR with its title and status. Do not create one if the project does not have it.

---

## ADR template (output template)

Each ADR must follow this Markdown, with headings and labels translated into the document language:

```markdown
# ADR-NNN: [decision title]

## Status

[Accepted | Proposed | Deprecated | Superseded by ADR-NNN] (`[hh:mm] Name` who closed it)

## Date

[date of this record; the meeting date if the sources state it, otherwise say it is not stated]

## Deciders

[meeting participants who took part in this decision, with role] (optional)

## Context

[the problem that forced the decision, the constraints, what exists in the code today (with file paths), and how the discussion went, with citations]

## Decision

[what was decided, stated precisely, including the parameters the meeting fixed and the refinements agreed later; gaps the meeting left open, named explicitly]

## Alternatives Considered

- **[alternative 1].** [what it was, why it was discarded, citation]
- **[alternative 2].** [...]
- **[plausible alternative, not discussed in the meeting].** [why it would not meet the stated requirements] (only if no discussed alternative exists)

## Consequences

**Positive**
- [effect]

**Negative**
- [effect]

**Trade-off**

[what is gained and what is accepted in exchange, in one or two sentences]
```

`Date` and `Deciders` are optional; `Status`, `Context`, `Decision`, `Alternatives Considered` and `Consequences` are mandatory.

---

## Acceptance Criteria

Before finishing, validate every item below. All are mandatory.

- [ ] `docs/adrs/` contains between 5 and 8 files in the format `ADR-NNN-title-in-kebab-case.md` (lowercase, no accents, words separated by hyphens)
- [ ] Every ADR contains the sections Status, Context, Decision, Alternatives Considered and Consequences (with the names required by the project, in the document language)
- [ ] The set covers at least 5 of the decisions the project instructions list as required. In this repository: Outbox pattern in MySQL; retry policy with backoff and DLQ; HMAC-SHA256 authentication with a secret per endpoint; at-least-once delivery with `X-Event-Id`; worker in a separate process with polling; reuse of the project's existing patterns
- [ ] At least 1 ADR explicitly references real files, modules or classes of the code base
- [ ] Every ADR has at least 1 alternative, real or labeled as plausible and not discussed
- [ ] Every ADR's consequences include positive and negative effects and an explicit trade-off
- [ ] Every citation points to a timestamp and speaker that exist in the transcript, and every file path exists (or is explicitly marked as new)
- [ ] No file outside `docs/adrs/` was changed

If a criterion cannot be met for lack of evidence in the sources, do not invent the content or declare the criterion as met.

---

## Validation and Final Report

After creating or updating the ADRs:

1. Re-read every ADR in full.
2. Check the filenames, for example: `ls docs/adrs | grep -E '^ADR-[0-9]{3}-[a-z0-9]+(-[a-z0-9]+)*\.md$'` must list between 5 and 8 files, and no other file in the folder should look like an ADR with a different pattern.
3. Check that every ADR has the five required section headings.
4. Check every `[hh:mm] Name` against the transcript and every file path against the repository.
5. Check that links between ADRs, and links to ADRs from the RFC, FDD and PRD, still resolve.
6. Check numbers, names and statuses for consistency across ADRs.
7. Check the diff to make sure only files in `docs/adrs/` changed.

Deliver a short report with:

- **Action:** ADRs created, updated or no changes needed, with the list of files.
- **Decision coverage:** which required decisions are covered, by which ADR, and any that are not.
- **Corrections made** (in review): what was made specific, added, merged, split or removed.
- **Alternatives labeled as not discussed:** list, per ADR.
- **Gaps recorded:** details the meeting left open, per ADR.
- **Acceptance criteria:** which passed and which are blocked.
- **Pending items:** renamed files and the links in other documents that must be updated, and conflicts between sources.
