# Design: Error Handler, Test Auditor, API Auditor Agents

**Date:** 2026-02-27
**Status:** Approved

## Summary

Three new audit agents to expand the Claude Code Audit Agents collection:

1. **Error Handler** — Language-agnostic error trace & handling auditor
2. **Test Auditor** — .NET-specific test quality & coverage auditor
3. **API Auditor** — .NET-specific API design & standards auditor

All three follow the established slash command pattern: standalone `.md` files with Mission, Audit Scope, Audit Process, Output Format, and Rules sections.

---

## Agent 1: Error Handler — Error Trace & Handling Auditor

**File:** `error-handler.md`
**Stack:** Language-agnostic
**Arguments:** Error message to trace (required)
**Finding IDs:** `EH-001`, `EH-002`, etc.

### Behavior

Given a specific error message, the agent traces the error through the codebase and audits every point where it is thrown, caught, logged, or surfaced to the user.

### Audit Scope

#### 1. Error Origin Identification
- Locate all throw/raise points matching the error message
- Identify triggering conditions
- Check if error message is descriptive and actionable
- Verify error context preservation (stack trace, correlation IDs)

#### 2. Error Propagation Analysis
- Trace error bubble through call stack layers
- Detect swallowed exceptions (silent catch blocks)
- Identify catch-and-rethrow patterns that lose context
- Check error wrapping quality (inner exception preserved)
- Verify error type specificity (generic vs specific types)

#### 3. Error Handling Quality
- Evaluate catch block appropriateness at each handling point
- Check for empty catch blocks
- Verify cleanup/disposal in error paths (using/finally/defer)
- Check error handling in async code paths
- Identify retry logic without backoff or limits

#### 4. Error Reporting & Logging
- Check logging with sufficient context
- Verify appropriate log levels
- Check for sensitive data leakage in error messages/logs
- Verify structured logging fields
- Check user-facing error messages are safe

#### 5. Error Recovery
- Check recovery strategies (fallback, circuit breaker, retry)
- Verify error state cleanup (partial transactions, temp files, locks)
- Check error path leaves system in consistent state
- Identify cascading failure risks

---

## Agent 2: Test Auditor — Test Quality & Coverage Agent

**File:** `test-auditor.md`
**Stack:** .NET Core 8 (xUnit, NUnit, MSTest, EF Core)
**Arguments:** Optional test spec file/folder. Full audit if omitted.
**Finding IDs:** `TA-001`, `TA-002`, etc.

### Behavior

Audits test quality and identifies coverage gaps. When given a specific test file, focuses on that file's quality and what it tests. When no argument is provided, audits all test projects.

### Audit Scope

#### 1. Test Quality
- Assertion quality: single concern, meaningful assertions
- Test naming: consistent convention (Method_Scenario_Expected)
- Arrange-Act-Assert structure
- Test isolation: no shared mutable state
- Flakiness indicators: timing, network, file system dependencies
- Magic values: unexplained test data

#### 2. Mocking & Dependencies
- Mock overuse: mocking implementation details vs behavior
- Missing mocks: real dependencies in unit tests
- Mock verification: over-verification (brittle tests)
- EF Core test DB strategy (InMemory vs SQLite vs real DB)
- HttpClient mocking patterns
- Test DI container setup vs production

#### 3. Coverage Gaps
- Untested public methods/endpoints
- Missing edge cases: null, empty, boundary values
- Missing error path tests
- Missing integration tests for critical workflows
- Missing negative tests
- Untested configuration-dependent paths

#### 4. Integration & E2E Test Patterns
- WebApplicationFactory usage
- Database test isolation
- Test data builders/factories
- Authentication in tests
- Middleware testing

#### 5. Test Architecture
- Test project structure mirrors source
- Shared fixtures usage
- Test categories/traits
- Test execution order independence
- Unnecessary test abstractions

---

## Agent 3: API Auditor — API Design & Standards Agent

**File:** `api-auditor.md`
**Stack:** .NET Core 8 (ASP.NET Core Web API)
**Arguments:** Optional focus area (e.g., `focus: endpoints`, `focus: documentation`, `focus: security-headers`). Full audit if omitted.
**Finding IDs:** `API-001`, `API-002`, etc.

### Audit Scope

#### 1. RESTful Design Principles
- HTTP method semantics correctness
- Resource naming: plural nouns, lowercase, kebab-case
- URL structure: consistent nesting, logical hierarchy
- Idempotency: PUT/DELETE idempotent, POST not
- Status codes: correct usage (201, 204, 409, etc.)
- Versioning strategy consistency

#### 2. Response Consistency
- Consistent response envelope structure
- Standard error response format (code, message, details, traceId)
- Pagination: consistent pattern, total count, navigation
- Serialization: camelCase, null handling, ISO 8601 dates
- Content negotiation
- Empty collection handling ([] not null/404)

#### 3. API Documentation (OpenAPI/Swagger)
- Swagger/OpenAPI spec present and auto-generated
- All endpoints documented with summary/description
- Request/response examples
- Parameter descriptions and constraints
- Authentication requirements per endpoint
- Response codes for all scenarios

#### 4. Rate Limiting & Throttling
- Rate limiting middleware configured
- Rate limit headers returned
- Per-client vs per-endpoint strategy
- 429 response with proper body

#### 5. CORS & Security Headers
- CORS: specific origins, not wildcard in production
- Restrictive methods/headers whitelist
- Security headers: X-Content-Type-Options, X-Frame-Options, HSTS
- Content-Security-Policy
- Server/X-Powered-By header removal

#### 6. API Architecture Patterns
- Controller organization and single responsibility
- Input validation consistency
- Model binding correctness
- Async patterns for I/O operations
- Global exception handling middleware
- Action filters for cross-cutting concerns

---

## Design Decisions

1. **Approach:** Three standalone agents, no cross-references or coupling
2. **Output format:** Standard audit report matching all existing agents
3. **Error Handler is language-agnostic:** Error tracing is universal across languages
4. **Test and API Auditors are .NET-specific:** Target xUnit/EF Core and ASP.NET Core patterns respectively
5. **Error Handler requires argument:** The error message is the entry point for tracing
6. **Test and API Auditors have optional arguments:** Can focus on specific files/areas or run full audit
