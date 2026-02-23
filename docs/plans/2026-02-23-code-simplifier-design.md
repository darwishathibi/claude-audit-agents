# Code Simplifier Agent — Design Document

**Date**: 2026-02-23
**Status**: Approved

## Overview

A new audit agent (`/code-simplifier`) that analyzes code for unnecessary complexity, inconsistent conventions, and AI-generated anti-patterns. Produces a structured report with mandatory before/after code examples that reduce cognitive load, enforce project standards, and make code production-ready — without changing behavior.

## Key Decisions

- **Separate agent**: New `code-simplifier.md` file, distinct from Code Cleaner
- **Language-agnostic**: Works with any codebase, not limited to .NET 8
- **Report-only**: Identifies simplification opportunities with before/after examples; does not modify files
- **Conventions via arguments**: User passes enforcement rules as arguments (no auto-detection)
- **AI output cleanup**: Gets a dedicated deep-dive section alongside other equally important categories
- **Mirrors Code Cleaner structure**: Same section layout (Mission, Audit Scope, Audit Process, Output Format, Rules, $ARGUMENTS)

## Differentiator from Code Cleaner

Code Cleaner finds dead code and SOLID violations. Code Simplifier focuses on making *working* code easier to read, understand, and maintain. They are complementary — use Code Cleaner to remove waste, then Code Simplifier to clarify what remains.

## Audit Scope (5 Categories)

### 1. Complexity Reduction
- Deeply nested code (>3 levels): flatten with guard clauses, early returns, extraction
- Complex boolean expressions: simplify with named variables or helper methods
- Long conditional chains (if/else if/switch): simplify, extract, or use lookup patterns
- Redundant conditions: remove tautological or contradictory checks
- Unnecessary ternary nesting: simplify to readable conditional logic
- Over-engineered abstractions: factories, strategies, wrappers where simple code suffices
- Unnecessary intermediate variables that add noise without clarity
- Complex LINQ/stream chains: break into readable steps or simplify

### 2. Standards Enforcement (Configurable)
- Naming consistency: variables, methods, classes following project conventions
- Import/using organization: consistent ordering, grouping, unused removal
- Preferred syntax patterns (user-specified via arguments)
- Consistent error handling style across the codebase
- Consistent function/method signature patterns
- Whitespace and formatting consistency
- Consistent use of language features (var vs explicit types, arrow functions vs declarations, etc.)

### 3. AI Output Cleanup (Dedicated Deep-Dive)
- Over-commenting: obvious comments that restate what the code does
- Verbose error handling: try-catch blocks wrapping code that can't throw
- Unnecessary null checks on values that are never null
- Over-defensive coding: validating inputs already validated upstream
- Unnecessary abstractions: interfaces/classes created for single implementations
- Repetitive patterns: AI tends to repeat similar structures instead of extracting shared logic
- Overly verbose variable names that reduce readability instead of helping
- Unnecessary async/await wrapping of synchronous operations
- Boilerplate that frameworks handle automatically

### 4. Readability Improvements
- Long methods: extract well-named sub-methods
- Poor naming: rename for clarity (variables, methods, classes)
- Missing guard clauses: convert deep nesting to early returns
- Implicit logic: make hidden assumptions explicit with clear code or named constants
- Magic numbers/strings: extract to meaningful constants
- Complex expressions: decompose into named steps
- Inconsistent patterns within the same file or module
- Code ordering: related methods grouped together, public before private

### 5. Performance Micro-Optimizations
- Unnecessary allocations in loops (string concatenation, object creation)
- Redundant computations: same value calculated multiple times
- Inefficient collection usage (wrong data structure for the access pattern)
- Unnecessary copies or conversions
- Lazy evaluation opportunities where eager evaluation wastes resources

## Audit Process

1. **Codebase Scan**: Map project structure, identify languages, and detect frameworks
2. **Convention Detection**: Note existing patterns to distinguish intentional style from inconsistency
3. **Complexity Analysis**: Identify nested, convoluted, or over-engineered code
4. **AI Pattern Detection**: Flag common AI-generated anti-patterns
5. **Standards Check**: Evaluate naming, formatting, and convention consistency (+ user-supplied rules)
6. **Readability Assessment**: Review method length, naming clarity, and code organization
7. **Report Generation**: Structured findings with mandatory before/after examples

## Output Format

Report header includes a **Cognitive Load Rating** (Low / Moderate / High / Critical) instead of Code Cleaner's Technical Debt Rating.

Every finding uses this structure:
- Finding ID (e.g., CR-001, AI-001)
- Location (file path + line)
- Issue description
- Impact
- Before code block
- After code block
- Rationale (why the simplification is safe and better)

Report sections: Executive Summary, Complexity Reduction, Standards Violations, AI Output Issues, Readability Improvements, Performance Micro-Optimizations, Quick Wins, Simplification Roadmap.

## Rules

- NEVER change behavior: all simplifications must preserve identical inputs, outputs, and side effects
- Every finding MUST include before/after code examples
- Distinguish between "should simplify" (high cognitive load) and "could simplify" (marginal improvement)
- Respect intentional complexity: some code is complex because the domain is complex
- Only flag inconsistencies against user-provided conventions or clear majority patterns
- Don't impose personal style preferences — focus on measurable clarity improvements
- AI output cleanup must be evidence-based — explain why a pattern is AI-typical
- Performance suggestions must not sacrifice readability unless the gain is significant
- Consider the audience: weight readability higher for teams with junior developers

## Arguments

```
/code-simplifier                          → Full scan, all categories
/code-simplifier src/services/            → Scope to specific directory
/code-simplifier enforce: camelCase, arrow-functions, ES modules
/code-simplifier focus: ai-cleanup        → Only AI output issues
/code-simplifier focus: complexity         → Only complexity reduction
```
