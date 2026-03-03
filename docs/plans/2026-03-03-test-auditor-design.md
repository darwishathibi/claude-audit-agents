# Design: Test Auditor — Integration Test Quality & Coverage Auditor

**Date:** 2026-03-03
**Status:** Approved

## Summary

A new audit agent that replaces the previously planned Test Auditor (from the 2026-02-27 three-new-agents design). Instead of covering general test quality and unit testing patterns, this agent focuses exclusively on **integration testing** — reliability, coverage gaps, infrastructure quality, and test architecture.

## Key Decisions

1. **Replaces planned Test Auditor**: The original Test Auditor covered unit test quality, mocking, and integration tests as a subsection. This redesign drops unit test concerns and goes deep on integration testing.
2. **Primarily .NET, but flexible**: Default patterns target xUnit, WebApplicationFactory, and EF Core. When a non-.NET stack is detected, the agent adapts checklist items to equivalent patterns.
3. **Balanced scope**: Reliability, coverage gaps, and infrastructure quality are equally prioritized — no single concern dominates.
4. **Integration tests only**: No E2E/browser testing (Playwright, Selenium). Strictly service-level integration tests (API tests, DB tests, middleware tests).
5. **Approach**: Broad auditor organized by concern area (6 categories), consistent with all other agents in the collection.

## Agent Spec

**File:** `test-auditor.md`
**Stack:** .NET Core 8 (xUnit, WebApplicationFactory, EF Core) — adaptable to other stacks
**Arguments:** Optional test file/folder to focus on. Full audit if omitted.
**Finding IDs:** `TA-001`, `TA-002`, etc.

### Mission

Audit integration tests for reliability, coverage gaps, infrastructure quality, and architectural soundness. Produce a structured report identifying flaky tests, missing test scenarios, poor test isolation, and infrastructure anti-patterns — with before/after examples for every finding.

### Audit Scope

#### 1. Test Reliability & Isolation
- Shared mutable state between tests (static fields, singleton leaks)
- Database state leaking between tests (missing cleanup/rollback)
- Test ordering dependencies (tests that fail when run individually)
- Non-deterministic behavior (DateTime.Now, Guid.NewGuid, random values)
- Timing-dependent assertions (Thread.Sleep, Task.Delay in tests)
- Port/resource conflicts between parallel test runs
- File system side effects without cleanup

#### 2. Test Infrastructure & Setup
- WebApplicationFactory configuration quality
- Test database strategy (InMemory vs SQLite vs real PostgreSQL via Testcontainers)
- DI service override patterns (replacing real services with fakes)
- Test fixture lifecycle (per-test vs per-class vs per-collection)
- Custom test server configuration vs production parity
- Missing or misconfigured test `appsettings.Test.json`
- Test startup performance (unnecessary service initialization)

#### 3. Coverage Gaps
- Untested API endpoints (controllers without corresponding integration tests)
- Missing error/failure path tests (4xx, 5xx scenarios)
- Missing auth/authorization flow tests (role-based access, token expiry)
- Untested middleware behavior (exception handling, CORS, rate limiting)
- Missing database transaction/rollback tests
- Untested background job/worker integrations
- Critical business workflows without integration tests

#### 4. Test Data Management
- Hardcoded test data (magic values, brittle assertions)
- Missing test data builders/factories
- Test data coupling (tests depending on data from other tests)
- Database seeding strategy quality
- Missing edge case data (nulls, empty strings, boundary values, unicode)
- Sensitive data in test fixtures (real emails, API keys)

#### 5. Assertion Quality
- Weak assertions (only checking status code, not response body)
- Missing response body validation
- Over-assertion (brittle tests that break on irrelevant changes)
- Missing header/content-type assertions
- Missing side-effect verification (DB state after API call, events published)
- Missing timing/performance assertions for critical paths

#### 6. Test Architecture
- Test project structure mirrors source project
- Consistent test naming convention
- Test helper/utility organization
- Base test class design (useful abstraction vs unnecessary inheritance)
- Test categories/traits for selective execution
- CI/CD integration considerations (parallel execution, test grouping)

### Audit Process

1. **Discover Test Projects** — Identify all test projects, frameworks, and test types
2. **Map Test-to-Source Coverage** — Match integration tests to source code, identify untested areas
3. **Infrastructure Assessment** — Evaluate WebApplicationFactory, DB strategy, fixtures, DI overrides
4. **Reliability Analysis** — Scan for shared state, ordering dependencies, flakiness indicators
5. **Test Data Review** — Check data builders, seeding, hardcoded values, data isolation
6. **Assertion Depth Check** — Evaluate assertion quality across response validation and side effects
7. **Architecture Review** — Assess structure, naming, organization, CI readiness
8. **Report Generation** — Structured findings with severity, before/after examples, roadmap

### Output Format

```markdown
# Test Auditor Report
**Project**: [name] | **Date**: [date]
**Test Projects**: [count] | **Integration Tests**: [count]

## Executive Summary
[2-3 sentence overview]
**Integration Test Health**: [Excellent / Good / Needs Work / Critical]

## Test Infrastructure
### [TA-001] [Title]
- **Severity**: [Critical / High / Medium / Low]
- **Location**: `path/to/test.cs`
- **Issue**: [description]
- **Before**: [code snippet]
- **After**: [improved code]

## Coverage Gaps
[same finding structure]

## Reliability Issues
[same finding structure]

## Test Data Issues
[same finding structure]

## Assertion Quality
[same finding structure]

## Quick Wins
[low-effort improvements]

## Roadmap
| Phase | Focus | Effort | Risk | Impact |
|-------|-------|--------|------|--------|
```

### Rules

- NEVER modify test code — report only
- Provide before/after examples for every finding
- Respect the existing test framework (don't suggest switching frameworks)
- Prioritize reliability issues over style issues
- Flag tests that pass but test nothing meaningful (false confidence)
- Distinguish "missing tests" from "inadequate tests"
- When detecting non-.NET stacks, adapt checklist items to equivalent patterns
- Use .NET 8 patterns when suggesting improvements
