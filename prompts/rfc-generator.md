# RFC Generator Prompt

Produce `docs/RFC.md`: the technical proposal submitted to the team for review. It answers four things — **what we propose, why this approach, which alternatives were rejected, and what is still undecided** — at the architecture level, in roughly 2–4 pages.

Three ideas drive everything below:

1. **Evidence, not invention.** The RFC is only useful to a reviewer if every decision, alternative, and open question in it really happened. A plausible-sounding alternative that nobody discussed is worse than a missing one, because it misrepresents the team's reasoning. When evidence is missing, say so in the final report instead of filling the gap.
2. **The RFC proposes; ADRs record; the FDD builds.** ADRs hold each closed decision in full. The FDD holds the implementation detail. The RFC is the connective narrative between them, so it links rather than copies.
3. **Convergence.** Running this prompt twice in a row on unchanged sources should leave the file essentially unchanged the second time. Treat the existing RFC as a document to maintain.

## Workflow

### 1. Gather sources (before touching any file)

Read, in this order, whatever exists:

- **Project instructions** (`CLAUDE.md`, `README.md`) — they may define required sections, acceptance criteria, traceability conventions, or files that must not be edited. Where they are stricter than this prompt, they win.
- **Every ADR** for the feature (commonly `docs/adrs/`, `docs/adr/`, `docs/decisions/`). Note each one's exact filename, title, status, rationale, consequences, rejected options, and cross-references. An *accepted* ADR is a closed decision; *proposed*, *superseded*, or *deprecated* ones are not — don't present them as settled.
- **Meeting records** (transcripts, notes, minutes). Read them in full, not just the parts the ADRs cite: alternatives that were dismissed quickly and questions that were deferred often never made it into an ADR, and those are exactly what the RFC needs. While reading, collect:
  - the problem and who asked for it;
  - the approach agreed on;
  - each alternative raised and discarded, with the stated reason;
  - each question raised and left undecided or pushed to later, with any named owner;
  - risks, constraints, dependencies;
  - the participants (they become the reviewers).
- **The code the feature integrates with**, enough to state the current state accurately (what exists today, what is absent, which flow the feature hooks into).
- **The FDD and PRD**, if they have real content, to align terminology and to know what detail already lives elsewhere.
- **The current `docs/RFC.md`**, if any.

Keep a locator for each fact as you collect it (timestamp + speaker for a transcript, file path for code, ADR number for a decision). You'll need them for inline citations and they make the final verification cheap.

If sources conflict, prefer explicit statuses, later statements, and recorded decisions. If the conflict can't be resolved confidently, it becomes an open question — don't silently pick a side.

### 2. Decide: create, improve, or leave alone

- **Missing, empty, or a placeholder stub** (a heading and a TODO comment) → write the whole document.
- **Has real content** → review the entire document against the sources and refactor it in place to be specific to this feature. Look for statements that are generic, vague, outdated, unsupported, contradictory with an ADR, duplicated, or at FDD-level detail; alternatives and open questions that exist in the meeting record but not in the RFC; broken or mistitled ADR links. Preserve valid feature-specific content even if no ADR repeats it, and don't change the meaning of a decision just to shorten the text. Restructure freely when it improves clarity.

  **Genericness test.** For each sentence, ask: could it appear unchanged in the RFC of a different feature? If yes ("the system must be scalable", "we will follow best practices", "there is a risk of failures"), rewrite it with the concrete component, number, constraint or citation the sources support, or remove it if the sources support nothing more specific. Typical rewrites: "improves reliability" becomes the actual guarantee and the ADR that grants it; "risk of downtime" becomes the specific failure mode of this architecture and its documented mitigation; "several alternatives were evaluated" becomes the named alternatives with the reason each was dropped.
- **Already accurate and complete** → make no changes. Don't reword for the sake of it, and don't bump the date; report "no changes required".

Never append a second copy of a section, alternative, risk, question, or link. If an item is already there, improve it where it stands.

### 3. Write the document

**Language:** follow the predominant language of the existing ADRs and project docs (section headings included). Use the same terms the ADRs use for components and concepts, so a reader moving between documents never wonders whether two names mean the same thing.

**Traceability:** cite sources inline using whatever convention the ADRs already use (for example `` `[09:04] Bruno` `` for a transcript, a file path for code, a link for an ADR). Alternatives and open questions especially need a citation each, since they are the items most likely to be doubted.

**Style:** do not use em dashes ("—") in the RFC, even though this prompt uses them.

**File paths:** only cite as existing the files that exist. A file or module the feature will create (for example `src/modules/webhooks/`) must be marked explicitly as new (for example "(novo)" or "a criar").

Use these sections, in this order (translate headings to the document language). When the project instructions name the required sections, use those exact names so the document can be checked against them. In this repository they are: `Metadados`, `Resumo executivo (TL;DR)`, `Contexto e problema`, `Proposta técnica`, `Alternativas consideradas`, `Questões em aberto`, `Impacto e riscos`, `Decisões relacionadas`.

#### Metadata
Title (feature-specific), author, status, date, reviewers.
- **Author:** the person writing the RFC, as identified in the repository docs; otherwise the name configured in `git config user.name` (the person producing the document now). Don't infer it from the authors of past commits, and don't credit a meeting participant as author unless the sources say so. If neither is available, write that it is not specified.
- **Status:** match the document's real lifecycle (`Draft`, `In Review`, `Accepted`…). An RFC being written now for review is not "Accepted" merely because its ADRs are.
- **Date:** today when creating or materially changing the RFC; otherwise leave it. If the meeting's calendar date isn't recorded, don't make one up.
- **Reviewers:** the meeting participants, with names and roles exactly as documented. If none can be found, say so.

#### Executive summary (TL;DR)
A few sentences or bullets: the problem, the recommended architecture, the main reason for choosing it, the most important trade-off, the biggest open question. A reviewer who reads only this should be able to say what is being proposed.

#### Context and problem
The actual situation: who needs what, what the system does today, what it lacks, and the constraints that shape the solution (team size, existing infrastructure, existing transactional flows, deadlines). Prefer concrete facts from the sources over generic qualities — "the status-change transaction already updates three tables, so a synchronous HTTP call inside it would let one slow client block others" rather than "the system must be scalable".

#### Technical proposal
The selected solution at architecture level: main components and their responsibilities, the high-level flow of data/events between them, integration points with the existing system, and the guarantees it provides (consistency, delivery semantics, security, failure handling). For each significant choice, give the one-or-two-sentence *why* and link the ADR that records it.

A useful test for the right level of detail: a sentence belongs here if a reviewer needs it to judge *whether this is the right approach*; it belongs in the FDD if it's only needed to *build* it. So: component names, responsibilities, and key parameters that were themselves debated (e.g. a retry schedule the team argued about) are in; table schemas, class/function signatures, full API contracts, migration scripts, and step-by-step plans are out.

A small diagram (Mermaid or ASCII) is welcome when the flow has more than three hops.

#### Alternatives considered
At least two alternatives that were **actually discussed and discarded** in the meeting record. For each:
- **Alternative** — what was proposed;
- **Trade-offs** — the concrete benefits and drawbacks that were raised;
- **Reason for rejection** — the specific concern that settled it;
- **Source** — citation (and the ADR, if one records the rejection).

Good hunting grounds: the "alternatives"/"context" sections of each ADR, and moments in the transcript where someone suggests something and another person pushes back. Don't pad the list with hypothetical options. If you genuinely find fewer than two, write what you have and flag the gap in the report.

#### Open questions
At least two questions that were **raised in the meeting and left undecided or deferred**. For each:
- **Question**;
- **Why it matters** — what it affects architecturally, operationally, or for delivery;
- **Current position** — what is known and what isn't;
- **Owner / next step** — only if the source names one.

Distinguish carefully between *deferred* ("we'll look at that after v1", "need to check with X") and *decided* (in an accepted ADR). An item that was explicitly ruled out of scope is a scope decision, not an open question — though a "not now, maybe later" item with an unanswered dependency can legitimately be one. Same rule as above: don't manufacture questions; report the gap instead.

#### Impact and risks
What changes in the existing system (components touched, new processes or tables, operational load) and the risks specific to *this* architecture — its failure modes, the consequences of its trade-offs, and what the open questions put at risk. For each significant risk give the impact and the mitigation or follow-up **that the sources support**. Skip risks that would apply to any software project.

#### Related decisions
A list of Markdown links to the feature's ADRs (at least two), each with one line on what it decides and how it bears on this proposal. Use paths relative to `docs/RFC.md` and the ADR's real filename and title — list the directory rather than guessing.

### 4. Verify

Re-read the finished `docs/RFC.md` top to bottom, then check:

1. Mechanical checks: every relative link resolves from `docs/`, at least two distinct ADR files are linked, no heading appears twice (a sign of content appended on a re-run), and the length fits roughly 2–4 pages (about 700 to 2,600 words). If the repository has `.claude/skills/rfc-generator/scripts/check_rfc.py`, run `python3 .claude/skills/rfc-generator/scripts/check_rfc.py docs/RFC.md` from the repository root to do these checks; otherwise do them by hand.
2. Every decision stated matches its ADR (status, parameters, rationale). No contradictions.
3. Every alternative and open question has a citation, and the cited passage really says that. Spot-check by re-opening the source at the cited location.
4. No reviewer, author, date, component, technology, or guarantee appears that the sources don't support. Every cited file path exists, or is explicitly marked as new.
5. Nothing reads like the FDD (schemas, signatures, step lists).
6. `git status` / `git diff` shows changes only to `docs/RFC.md` (plus `docs/` creation if it was missing). Don't modify ADRs, the FDD, application code, or other files unless the user asks. If the project keeps a traceability tracker, mention in the report that new RFC items may need rows there rather than editing it yourself.

### 5. Report

Finish with a short report:

- **Action:** created / updated / no changes required.
- **Sources reviewed:** the ADRs, meeting records, code, and other docs you used.
- **Improvements made:** the most important ones (skip on a no-change run).
- **Acceptance criteria:** which pass and which are blocked, using the list below.
- **Outstanding information:** the exact facts or records that would unblock anything missing.

Don't describe the RFC as fully compliant if any criterion is unmet.

## Acceptance criteria

- `docs/RFC.md` exists and is valid Markdown.
- The section headings use the names required by the project instructions.
- Metadata has author, status, date, reviewers, with missing information explicitly marked as such.
- The executive summary conveys the proposal on its own.
- Context and problem describe the real feature and its constraints.
- The technical proposal explains the chosen architecture without duplicating the FDD.
- ≥ 2 real alternatives discussed and rejected, each with trade-offs and reason for rejection.
- ≥ 2 real questions raised and left unresolved or deferred.
- Impact and risks are specific to this architecture.
- ≥ 2 ADRs referenced through links that resolve.
- All claims are consistent with the sources; nothing fabricated.
- Re-running adds no duplicate content and makes no unnecessary changes.
- Length is appropriate for an architecture-level review (about 2–4 pages).
