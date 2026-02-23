# Code Simplifier Agent — Implementation Plan

> **For Claude:** REQUIRED SUB-SKILL: Use superpowers:executing-plans to implement this plan task-by-task.

**Goal:** Create a new `/code-simplifier` audit agent that analyzes code for unnecessary complexity, inconsistent conventions, and AI-generated anti-patterns.

**Architecture:** A single markdown file (`code-simplifier.md`) following the same structure as existing agents (Mission, Audit Scope, Audit Process, Output Format, Rules, $ARGUMENTS). The README.md is updated to include the new agent in all relevant sections.

**Tech Stack:** Markdown (Claude Code slash command prompt files)

**Design doc:** `docs/plans/2026-02-23-code-simplifier-design.md`

---

### Task 1: Create the Code Simplifier agent file

**Files:**
- Create: `code-simplifier.md` (project root, same location as other agents)
- Reference: `code-cleaner.md` (for structure/format consistency)
- Reference: `docs/plans/2026-02-23-code-simplifier-design.md` (approved design)

**Step 1: Create `code-simplifier.md`**

Write the full agent file with these sections, mirroring `code-cleaner.md` structure exactly:

1. **Title line**: `# Code Simplifier — Code Clarity & Simplification Agent`
2. **Persona**: Language-agnostic (NOT .NET-specific like Code Cleaner). Focused on reducing cognitive load in working code.
3. **Mission**: Analyze code for unnecessary complexity, inconsistent conventions, and AI-generated patterns. Report-only with mandatory before/after examples.
4. **Audit Scope** — 5 categories with `- [ ]` checklist items:
   - `### 1. Complexity Reduction` — 8 items (nested code, boolean expressions, conditional chains, redundant conditions, ternary nesting, over-engineered abstractions, unnecessary intermediates, complex chains)
   - `### 2. Standards Enforcement (Configurable)` — 7 items (naming, imports, syntax patterns, error handling style, signatures, whitespace, language features)
   - `### 3. AI Output Cleanup` — 9 items (over-commenting, verbose error handling, unnecessary null checks, over-defensive coding, unnecessary abstractions, repetitive patterns, verbose names, unnecessary async, framework boilerplate)
   - `### 4. Readability Improvements` — 8 items (long methods, poor naming, guard clauses, implicit logic, magic values, complex expressions, inconsistent patterns, code ordering)
   - `### 5. Performance Micro-Optimizations` — 5 items (allocations in loops, redundant computations, wrong data structures, unnecessary copies, lazy evaluation)
5. **Audit Process** — 7 numbered steps from design doc
6. **Output Format** — markdown template inside a code block, using:
   - Header with Cognitive Load Rating (not Technical Debt Rating)
   - Finding IDs: CR-xxx, STD-xxx, AI-xxx, READ-xxx, PERF-xxx
   - Every finding has: Location, Issue, Impact, Before code block, After code block, Rationale
   - Quick Wins section
   - Simplification Roadmap table
7. **Rules** — 9 rules from design doc
8. **`$ARGUMENTS`** — at the very end, just like Code Cleaner

**Step 2: Verify file exists and structure matches Code Cleaner**

Run: `ls -la /path/to/code-simplifier.md`
Expected: File exists with similar size to `code-cleaner.md`

Verify sections present:
Run: `grep "^##" code-simplifier.md`
Expected output should show: Mission, Audit Scope, Audit Process, Output Format, Rules — same top-level sections as Code Cleaner.

**Step 3: Commit**

```bash
git add code-simplifier.md
git commit -m "feat: add Code Simplifier audit agent

Language-agnostic agent for code clarity and simplification.
Covers complexity reduction, standards enforcement, AI output
cleanup, readability improvements, and performance micro-optimizations."
```

---

### Task 2: Update README.md with new agent

**Files:**
- Modify: `README.md`

**Step 1: Add Code Simplifier to the agents table**

In the `## Agents` table (line ~7), add a new row after Code Cleaner:

```markdown
| ✨ Code Simplifier | `/code-simplifier` | Code clarity, complexity reduction, AI output cleanup, standards enforcement |
```

**Step 2: Update the project structure section**

In the `## Project Structure` tree (line ~249), add:

```
    ├── code-simplifier.md        # Code clarity & simplification
```

Place it after `code-cleaner.md` alphabetically.

**Step 3: Add Code Simplifier to workflows table**

In the `### Common Workflows` table (line ~109), add a row:

```markdown
| Code simplification | Code Cleaner → Code Simplifier | `/code-cleaner` then `/code-simplifier` |
```

**Step 4: Update the combo audit example**

In the `### Combo Audits` section (line ~89), add after the code-cleaner step:

```bash
# Then: simplify the remaining code
/code-simplifier
```

**Step 5: Add usage examples for Code Simplifier**

In the `### Targeted Audit` section (line ~77), add examples:

```
/code-simplifier focus: ai-cleanup

/code-simplifier enforce: camelCase, arrow-functions, ES modules
```

**Step 6: Update agent count**

Change "7 specialized audit agents" to "8 specialized audit agents" in the opening line (line 3).

**Step 7: Remove Code Simplifier from future ideas (if listed)**

Code Simplifier is not in the future ideas table, so no removal needed. Skip this step.

**Step 8: Verify README changes**

Run: `grep -c "code-simplifier" README.md`
Expected: At least 5 mentions (table, structure, workflows, combo, targeted)

**Step 9: Commit**

```bash
git add README.md
git commit -m "docs: add Code Simplifier agent to README

Update agents table, project structure, workflows, combo audit
example, and targeted audit examples. Bump agent count to 8."
```
