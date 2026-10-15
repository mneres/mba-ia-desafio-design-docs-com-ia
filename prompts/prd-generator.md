# PRD Generator Prompt

## Objective

Create or improve `docs/PRD.md`: the **PRD (Product Requirements Document)** of the feature.

| Document | Role | Level | Question it answers |
| --- | --- | --- | --- |
| **PRD** | Problem, audience, scope and success metrics | Product / business | **Why and what?** |

The PRD is produced **last** among the major documents. Because it sits at the highest level, with the RFC, the FDD and the ADRs in hand it is essentially a **consolidation**: the decisions are already made and recorded, and the PRD restates them from the product and business point of view. There is no interview: all information comes from the repository.

The PRD does not explain **how** to build the feature. It does not repeat the architecture argumentation of the RFC (alternatives, open technical questions) or the implementation detail of the FDD (flows, contracts, schemas, error codes). When a reader needs that, the PRD links to the right document.

The final PRD must be rendered in the format defined in **"PRD skeleton (output template)"**.

**Language:** write the PRD in the predominant language of the existing ADRs, RFC and FDD, section headings included. The skeleton below is in English; translate its headings and labels into the document language. When the project instructions name the required sections, use those exact names so the document can be checked against them.

---

## Role

You are an assistant specialized in **PRDs**.

Your role is to:

- Read the repository sources before writing anything.
- Consolidate what the meeting and the design docs established into a product view: who needs this, why, what must be delivered, how success is measured and what is left out.
- Translate technical decisions into product terms (what they mean for the customer and the business), without re-deciding them.
- Produce a standardized document that lets product, business and engineering agree on what will be delivered and how it will be accepted.

---

## Sources of Information

Read, in this order, before creating or changing any file:

1. **Project instructions** (`CLAUDE.md`, `README.md`): PRD requirements, acceptance criteria, traceability conventions and restrictions (for example, not modifying the application code).
2. **The transcript** `TRANSCRICAO.md`, in full: the primary source for the product view. Who asked, why, the deadline, the latency the customers consider acceptable, the requirements listed by the PM, and every item ruled out or deferred.
3. **All ADRs** in `docs/adrs/`: the closed decisions and their trade-offs, which feed "Main decisions and trade-offs".
4. **The RFC** in `docs/RFC.md`: problem framing, proposal summary, impact and risks.
5. **The FDD** in `docs/FDD.md`: the technical objectives, acceptance criteria and risks, to keep the PRD consistent with them and to point to it for detail.
6. **The existing code** (`src/`, `prisma/`), only as far as needed to describe the current state accurately (what exists today, what is absent).
7. **The current `docs/PRD.md`**, if it already has real content.

If the RFC or the FDD is still a placeholder, generate the PRD from the transcript and the ADRs, and flag in the final report that it must be reconciled once those documents exist.

Precedence in case of conflict: the transcript and the code are the ground truth; no statement in the PRD may contradict them. Among the derived documents, an accepted ADR wins over the RFC, and the RFC wins over the FDD. If a derived document contradicts the transcript or the code, do not copy the error: follow the transcript or the code and flag the divergence in the final report so the derived document can be fixed. If the conflict cannot be resolved safely, flag it instead of silently picking a side.

---

## Principles

- **Nothing invented.** Every requirement, metric, decision, risk and out-of-scope item must be traceable to the transcript (timestamp and speaker, for example `[09:02] Marcos`), an ADR, the RFC, the FDD or a real file path. Cite the source inline, using the convention the other docs already use.
- **Labeled hypotheses.** Some product details may not have been defined (a baseline for a metric, a success target after launch). When the detail is not in the sources, mark it as **(hypothesis)**, in the document language. Do not present a hypothesis as a decision.
- **Metrics come from the sources.** At least one objective needs a metric with a quantitative target. Prefer numbers the meeting actually stated (for example, a latency threshold the customers accept) over targets you would choose yourself.
- **Out of scope means discussed.** Each out-of-scope item must be something the meeting explicitly discarded or deferred, with the reason and the source. Do not pad the list with items nobody mentioned.
- **Requirements are the ones discussed.** Functional requirements are the capabilities the meeting asked for, each numbered and verifiable. Reach any minimum count the project requires by covering what was discussed, never by splitting one requirement artificially or inventing new ones.
- **Consistency with the ADRs, RFC and FDD.** Use the same names for the feature, components, events and parameters. Do not contradict an accepted decision, and keep numbers (timeouts, retries, limits, deadlines) identical across documents.
- **Product level.** A sentence belongs in the PRD if product or business needs it to agree on *what* is delivered and *why*. If it is only needed to *build* it, it belongs in the FDD; link there instead.
- **Do not modify** application code, ADRs, the RFC, the FDD or other documents. The only output is `docs/PRD.md`.
- **Repeatable execution.** Running this prompt again on unchanged sources must not duplicate sections or reword what is already correct. See "Create or review" below.
- **Verified file paths.** Only cite as existing the files that exist. A file the feature will create must be marked explicitly as new (for example "(novo)" or "a criar").
- Use simple, direct language, readable by non-engineers.
- **Do not use em dashes ("—").**

---

## Create or review

Decide first what state `docs/PRD.md` is in:

- **Missing, empty or a placeholder stub** (a heading and a TODO comment): generate the whole document from the sources.
- **Has real content:** review the entire document against the sources and refactor it in place to be specific to this feature. Do not just append. Look for:
    - **Generic statements.** For each sentence, ask: could it appear unchanged in the PRD of a different feature? If yes ("improve customer experience", "increase efficiency", "the system must be secure and scalable", "risk of delays"), rewrite it with the concrete customer, number, deadline, requirement or citation the sources support. Typical rewrites: "customers want faster updates" becomes the named customers, what they do today and the latency they accept; "improve satisfaction" becomes an objective with a metric and a target; "risk of delays" becomes the specific deadline at stake and its documented mitigation.
    - **Objectives without a metric or target**, and targets that are not in the sources.
    - **Out-of-scope items** without reason or source, or that the meeting never mentioned.
    - **Requirements** that are vague, not verifiable, split artificially, or missing compared with what the meeting asked for.
    - **Contradictions** with an accepted ADR, the RFC or the FDD, and numbers that differ between documents.
    - **Implementation detail** (schemas, error codes, flows) that belongs in the FDD, and architecture debate that belongs in the RFC.
    - **Duplicated** content and unlabeled hypotheses.

  Preserve valid feature-specific content, and do not change the meaning of a decision just to shorten the text.
- **Already accurate, specific and complete:** make no changes and report "no changes needed".

---

## Required Sections

The PRD must contain, at minimum, the sections below:

- **Summary and context of the feature**
- **Problem and motivation**
- **Target audience and usage scenarios**
- **Objectives and success metrics**
- **Scope (included and out of scope)**
- **Functional requirements**
- **Non-functional requirements**
- **Main decisions and trade-offs**
- **Dependencies**
- **Risks and mitigation**
- **Acceptance criteria**
- **Testing and validation strategy**

---

## Elaboration Process

1. **Summary and context**
    - What the feature is, in one or two sentences, and where it fits in the existing product
    - What exists today and what is missing
2. **Problem and motivation**
    - The pain, who asked for it and the business consequence of not delivering (deadline, churn risk)
    - Why now
3. **Target audience and usage scenarios**
    - Who uses or benefits from the feature (customers, internal roles such as operators and admins)
    - 2 to 4 concrete scenarios, written from the user's point of view
4. **Objectives and success metrics**
    - Each objective with metric, baseline (if known) and target
    - At least one objective with a quantitative target taken from the sources
5. **Scope**
    - What is included in this delivery
    - **Out of scope:** at least 2 items explicitly discarded or deferred in the meeting, each with whether it was discarded or deferred, the reason and the source
6. **Functional requirements**
    - Numbered (FR-01, FR-02... or the convention of the document language), one capability per item, each verifiable
    - Include permissions and business rules discussed (who can do what)
    - Each with its source
7. **Non-functional requirements**
    - Latency, reliability and delivery guarantee, security, limits, auditability, compatibility
    - Measurable target whenever the sources define one
8. **Main decisions and trade-offs**
    - The key decisions in product terms: what the customer gains and what is accepted in exchange
    - Link each to its ADR; do not repeat the ADR's full argumentation
9. **Dependencies**
    - Systems, teams, people and activities the delivery depends on (security review, documentation in the developer portal, existing infrastructure)
10. **Risks and mitigation**
    - At least 2 risks, each with **probability**, **impact** and **mitigation**
    - Product and delivery risks first (deadline, customer adoption, customer-side duplicates), technical risks only as far as they affect the product
    - Keep consistency with the risks in the RFC and FDD
11. **Acceptance criteria**
    - Objective, product-level checklist of what must be true for the feature to be accepted
    - Traceable to the functional and non-functional requirements; point to the FDD for the technical criteria
12. **Testing and validation strategy**
    - How the requirements will be verified (automated tests, end-to-end, security review, validation with the requesting customers)
    - How and when the success metrics will be measured after launch

---

## PRD skeleton (output template)

The final output must follow this Markdown, with headings and labels translated into the document language:

```markdown
# PRD: [feature name]

Version: [version]
Date: [date]
Owner: [product owner, or "not specified"]
Related documents: [links to the RFC, the FDD and the ADRs]

---

## 1. Summary and context
[what the feature is, where it fits, what exists today]

## 2. Problem and motivation
[pain, who asked, business consequence, why now]

## 3. Target audience and usage scenarios

**Target audience**
- [audience 1]

**Usage scenarios**
1. [scenario 1]
2. [scenario 2]

## 4. Objectives and success metrics

| Objective | Metric | Baseline | Target | Source |
| --- | --- | --- | --- | --- |
| [objective] | [metric] | [current] | [numeric value] | [source] |

## 5. Scope

**Included**
- [item 1]

**Out of scope**

| Item | Status | Reason | Source |
| --- | --- | --- | --- |
| [item A] | [discarded/deferred] | [why] | [source] |
| [item B] | [discarded/deferred] | [why] | [source] |

## 6. Functional requirements

| ID | Requirement | Source |
| --- | --- | --- |
| FR-01 | [requirement] | [source] |

## 7. Non-functional requirements

| ID | Category | Requirement | Target | Source |
| --- | --- | --- | --- | --- |
| NFR-01 | [performance/security/...] | [requirement] | [value] | [source] |

## 8. Main decisions and trade-offs
- **[decision]:** [what is gained and what is accepted in exchange]. See [ADR-NNN](adrs/ADR-NNN-....md).

## 9. Dependencies
- [dependency 1]

## 10. Risks and mitigation

| Risk | Probability | Impact | Mitigation |
| --- | --- | --- | --- |
| [risk 1] | [low/medium/high] | [impact] | [mitigation] |
| [risk 2] | [low/medium/high] | [impact] | [mitigation] |

## 11. Acceptance criteria
- [ ] [criterion 1]
- [ ] [criterion 2]

## 12. Testing and validation strategy
- **Tests:** [approach per type of requirement]
- **Metric validation:** [how and when to measure]
```

---

## Acceptance Criteria

Before finishing, validate every item below. All are mandatory.

- [ ] The file `docs/PRD.md` exists and is Markdown
- [ ] It contains all the required sections
- [ ] It identifies at least the minimum number of functional requirements the project requires (8 in this repository), all discussed in the meeting
- [ ] At least 1 objective has a metric and a quantitative target
- [ ] The "Out of scope" section lists at least 2 items explicitly discarded or deferred in the meeting
- [ ] The "Risks" section includes at least 2 risks with probability, impact and mitigation
- [ ] No statement contradicts an accepted ADR, the RFC or the FDD
- [ ] Every hypothesis is labeled as a hypothesis
- [ ] No file other than `docs/PRD.md` was changed

If a criterion cannot be met for lack of evidence in the sources, do not invent the content or declare the criterion as met.

---

## Validation and Final Report

After creating or updating the PRD:

1. Re-read `docs/PRD.md` in full.
2. Check that the links to the RFC, the FDD and the ADRs resolve from `docs/`.
3. Check numbers, names and deadlines against the transcript, the ADRs, the RFC and the FDD.
4. Spot-check the cited sources: re-open the transcript at the cited timestamps for the out-of-scope items and a sample of requirements.
5. Check the diff to make sure only `docs/PRD.md` changed.
6. Mechanical checks, and fix whatever fails: links resolve; at least 8 distinct functional requirement IDs (`FR-NN` or `RF-NN`); at least 2 risk rows that state a probability; no duplicated headings. If the repository has `.claude/skills/prd-generator/scripts/check_prd.py`, run `python3 .claude/skills/prd-generator/scripts/check_prd.py docs/PRD.md` from the repository root to do these checks; otherwise do them by hand.

Deliver a short report with:

- **Action:** PRD created, updated or no changes needed.
- **Sources consulted:** transcript, ADRs, RFC, FDD and code files used.
- **Hypotheses adopted:** list of the points marked as hypothesis.
- **Acceptance criteria:** which passed and which are blocked.
- **Pending items:** conflicts between sources, documents still missing and information that is lacking.
