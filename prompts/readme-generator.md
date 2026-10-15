# Task: Update the Root README.md: Production Process Documentation

## Objective

Update the `README.md` file at the root of the repository to document the production journey: how the challenge was approached, how the work was organized, how AI tools were used, which prompts were developed, what iterations and corrections were necessary, and how to navigate the final deliverables.

**This is the final documentation step.** The deliverable of this challenge is **documentation only**: no application code was implemented or changed (`src/`, `prisma/`, `tests/` and the configuration files are reference material and must stay untouched). The documentation package (`docs/PRD.md`, `docs/RFC.md`, `docs/FDD.md`, `docs/TRACKER.md` and `docs/adrs/`) should already be complete. Reconstruct the process from the available repository artifacts rather than documenting an assumed or idealized workflow. Never describe code implementation as part of the work.

The goal is to produce an honest, specific, and useful retrospective of the actual production process.

## Instructions

### 1. Inspect the repository and reconstruct the workflow

Before editing the README:

- Read the current root `README.md`, including the original challenge statement. If it has already been replaced, read the original with `git show main:README.md`.
- Inspect the repository structure and identify the final deliverables: `docs/PRD.md`, `docs/RFC.md`, `docs/FDD.md`, `docs/TRACKER.md`, every file in `docs/adrs/`, and the prompts in `prompts/`.
- Review each of those documents to understand what was produced and how they relate to each other.
- Inspect the existing source code (`src/`, `prisma/`) only to describe how it was used as reference material for the documents. It was not changed.
- Use the evidence actually available in the repository to identify AI tools, prompts, iterations, errors and corrections:
    - `git log` (with `--stat` or `-p` when useful): the order of commits, rewritten or reverted documents (for example, a document committed and then reverted), and the dates of each step.
    - The files in `prompts/` and in `.claude/skills/`: the prompts and skills actually used.
    - `CLAUDE.md`, `AGENTS.md` and any other instruction files.
    - Conversation history, only if it is accessible in the current session. A new session usually cannot see past conversations, so do not rely on it.
- Identify the actual sequence of work and the dependencies between documents.

Do not invent details to fill gaps. If the exact number of iterations, a tool's contribution, or a particular correction cannot be verified, describe the available evidence accurately instead of fabricating specifics.

### 2. Replace the README content

Replace the existing contents of the root `README.md` with documentation of the production process.

Write in Brazilian Portuguese, using a clear, professional, first-person singular or impersonal style, as appropriate. This is an individual deliverable: the document should explain the author's actual journey with the AI tools, not a team's, and not merely describe the final architecture or reproduce the original challenge requirements. Do not use em dashes ("—").

Keep a short link or reference to the original challenge statement (for example, the upstream repository `https://github.com/devfullcycle/mba-ia-desafio-design-docs-com-ia`). The primary purpose of the new README is to document the production process.

### 3. Include all mandatory sections

Use the following structure and cover every requirement.

#### Sobre o desafio

Write one or two paragraphs explaining the challenge in your own words.

Describe:
- The problem or objective addressed.
- The expected deliverables.
- The importance of organizing the technical reasoning, documenting decisions, and validating the final result.

Avoid simply copying the original challenge statement.

#### Ferramentas de IA utilizadas

List the AI tools actually used during production.

For each tool, briefly explain:
- Its role in the workflow.
- What kind of task it supported.
- How its output was evaluated or incorporated, where applicable.

Include only tools supported by available evidence. Do not list tools merely because they would have been useful.

#### Workflow adotado

Explain the actual workflow in chronological order.

Document:
- How the challenge was analyzed and broken down into tasks.
- The order in which the documents were produced (the challenge suggests ADRs, RFC, FDD, PRD, Tracker, README, final review; report the order that actually happened).
- How architectural decisions were captured in ADRs.
- How the technical proposal was consolidated into the RFC.
- How the FDD, the PRD and the Tracker were built on top of the earlier documents.
- How the transcript and the existing code were used as sources, and how the Tracker and the final review were used to validate the documents against them.
- How prompts were created, refined, and reused.
- How inconsistencies were identified and resolved.
- Why the README was updated last, after the production process was complete.

Use the actual artifacts in the repository to establish the sequence. Explain dependencies between stages instead of presenting an arbitrary generic workflow.

#### Prompts customizados

Include at least **two relevant prompts actually written or adapted during production**.

For each prompt:
- Add a short description of its purpose and where it was used.
- Present the prompt in a fenced code block.
- Preserve its important instructions, constraints, and expected output.
- Explain briefly why it was useful, when supported by evidence.

Prioritize prompts that materially influenced the work, such as prompts for generating or reviewing ADRs, consolidating technical decisions into an RFC, detailing the FDD, building the Tracker, or running the final review against the acceptance criteria.

Retrieve the actual prompts from `prompts/` (and `.claude/skills/`, if used) whenever possible, and link to the file each one comes from. Long prompts may be shown as an excerpt that keeps their key instructions, labeled as an excerpt, with the link to the full file. If a prompt must be reconstructed, label it clearly as a reconstructed version rather than claiming it is an exact historical copy.

Use the appropriate language for each prompt, preserving the original wording when available.

#### Iterações e ajustes

Describe at least **two concrete iterations or adjustments** that occurred during production.

For each iteration, explain:
1. The initial output, issue, omission, or superficial treatment.
2. Why it was insufficient or incorrect.
3. What instruction, prompt, decision, or implementation was changed.
4. What improved in the subsequent result.

Use concrete examples grounded in the available history or artifacts. Relevant examples may include missing architectural trade-offs, unsupported assumptions, hallucinated requirements or file paths caught by the Tracker, items the meeting discarded that appeared as requirements, inconsistent decisions or numbers between documents, content duplicated across documents, or a document rewritten or reverted. Only present them as actual events if evidence supports them (commits, prompt revisions, review reports).

The challenge asks how many major iterations were needed. Derive the count from the evidence (for example, commits that create, rewrite or revert a document, and revisions of the prompts). If it cannot be established with reasonable confidence, say explicitly that the exact count was not recorded and summarize the identifiable major revision cycles.

The objective is to demonstrate critical evaluation of AI-generated output, not to imply that every generated response was correct.

#### Como navegar a entrega

List the actual paths of the deliverables relative to the repository root.

For each relevant file or directory:
- Explain its purpose.
- Describe what the reader should look for.
- Indicate dependencies or relationships with other documents when applicable.

Provide a suggested reading order that helps a reviewer understand the reasoning, from the meeting transcript and the decisions (ADRs) through the technical proposal (RFC), the implementation design (FDD), the product view (PRD) and the traceability (Tracker).

Use real paths verified against the repository. Do not invent filenames or directories.

### 4. Validate against the acceptance criteria

Before finishing, verify the README against this checklist:

- [ ] The root `README.md` has been replaced with production-process documentation.
- [ ] All six mandatory sections are present:
  - Sobre o desafio
  - Ferramentas de IA utilizadas
  - Workflow adotado
  - Prompts customizados
  - Iterações e ajustes
  - Como navegar a entrega
- [ ] At least one AI tool actually used is listed, with its role explained.
- [ ] At least two relevant custom prompts appear in fenced code blocks.
- [ ] At least two concrete iterations or adjustments are described.
- [ ] The workflow reflects the actual production sequence.
- [ ] The README documents the production journey rather than only the final solution.
- [ ] All referenced file paths exist in the repository.
- [ ] No sentence claims that application code was implemented or changed.
- [ ] A link or reference to the original challenge statement is kept.
- [ ] Historical claims, tools, prompts, and iteration counts are evidence-based.
- [ ] The document explains why the README was written last.
- [ ] Existing useful information is retained only where it supports the new purpose.

### 5. Constraints

- Modify only the root `README.md`.
- Do not modify the PRD, the RFC, the FDD, the Tracker, the ADRs, the prompts, `TRANSCRICAO.md`, source code, tests, or configuration files.
- Do not create additional files.
- Do not introduce new technical decisions or claim any code was implemented.
- Every file in `prompts/` that the README links to must be versioned in git. If it is untracked (`git status`), flag it in the final report so it is committed before delivery.
- Do not fabricate AI usage, prompts, mistakes, iterations, or results.
- Keep the documentation concise but detailed enough to demonstrate the actual production process.
- Prefer concrete examples and references to repository artifacts over generic statements.

### 6. Final verification and report

After updating the README:

1. Re-read the complete file.
2. Check the six mandatory sections and all acceptance criteria.
3. Verify the referenced paths against the repository.
4. Ensure the Markdown formatting is valid and prompt blocks are correctly fenced.
5. Confirm that no files other than the root `README.md` were changed by this task.

Finally, provide a concise report stating:
- What was documented.
- Which acceptance criteria were satisfied.
- Which facts could not be verified, if any.
- Which file was modified.

Do not claim that a criterion was satisfied if the evidence does not support that claim.