# 🤖 Claude Code Audit Agents

A collection of 7 specialized audit agents for Claude Code, designed for **.NET Core 8 + PostgreSQL** projects. Each agent is a focused expert that performs deep analysis on a specific domain.

## Agents

| Agent | Command | Focus |
|-------|---------|-------|
| 🛡️ Auth Guardian | `/auth-guardian` | Session hijacking, privilege escalation, auth middleware |
| 💰 Payment Sentinel | `/payment-sentinel` | Payment gateway security, double payments, callback validation, race conditions |
| 🔌 Integration Inspector | `/integration-inspector` | 3rd-party API audit (Billplz, Resend, DelyvaNowX, Didit, etc.), error handling, idempotency |
| ⚡ DB Perf Optimizer | `/db-perf-optimizer` | Missing indexes, N+1 queries, slow JOINs, FK integrity |
| 🔒 Security Auditor | `/security-auditor` | SQL injection, XSS, CSRF, file upload, IDOR, sensitive data exposure |
| 🧹 Code Cleaner | `/code-cleaner` | Dead code removal, SOLID principles, code smells |
| 📖 Code Explain | `/code-explain` | Code structure explanation, data flow, architecture walkthrough |

---

## Setup

### Option A: Add to a Single Project

```bash
# Navigate to your project root
cd /path/to/your-project

# Create the commands directory
mkdir -p .claude/commands

# Copy all agent files
cp /path/to/claude-audit-agents/.claude/commands/*.md .claude/commands/
```

### Option B: Install Globally (Available in All Projects)

```bash
# Create the global commands directory
mkdir -p ~/.claude/commands

# Copy all agent files
cp /path/to/claude-audit-agents/.claude/commands/*.md ~/.claude/commands/
```

### Option C: Clone & Link

```bash
# Clone the repo (or copy the folder)
git clone <your-repo-url> ~/claude-audit-agents

# Symlink into your project
mkdir -p .claude/commands
ln -s ~/claude-audit-agents/.claude/commands/*.md .claude/commands/
```

### Verify Installation

Open Claude Code in your project directory and type `/`. You should see all 7 agents listed as slash commands.

---

## Usage

### Basic Usage

In Claude Code, type the slash command:

```
/auth-guardian
```

Claude will adopt the Auth Guardian persona and audit your entire codebase's auth layer, producing a structured report.

### Targeted Audit

Pass arguments to focus the audit on specific files or areas:

```
/security-auditor Audit only the Controllers/ and Middleware/ folders

/db-perf-optimizer Focus on the Orders and Subscriptions entities

/payment-sentinel Audit the Billplz integration specifically

/code-explain Please explain this code: src/Services/PaymentService.cs

/integration-inspector Audit the Didit eKYC integration only
```

### Combo Audits

Run multiple agents sequentially for comprehensive coverage:

```
# First: find security issues
/security-auditor

# Then: check the auth layer specifically
/auth-guardian

# Then: optimize the database
/db-perf-optimizer

# Finally: clean up the code
/code-cleaner
```

### Common Workflows

| Goal | Agent(s) | Command |
|------|----------|---------|
| Pre-deployment security check | Security Auditor → Auth Guardian | `/security-auditor` then `/auth-guardian` |
| Payment integration launch | Payment Sentinel → Integration Inspector | `/payment-sentinel` then `/integration-inspector` |
| Performance optimization | DB Perf Optimizer | `/db-perf-optimizer` |
| New team member onboarding | Code Explain | `/code-explain Explain the overall architecture` |
| Code review prep | Code Cleaner → Security Auditor | `/code-cleaner` then `/security-auditor` |
| New 3rd-party integration | Integration Inspector | `/integration-inspector Audit the [new service] integration` |

---

## Customization

### Modify an Existing Agent

Edit the `.md` file in `.claude/commands/`. The structure is:

```markdown
# Agent Name — Description

You are **Agent Name**, a specialized...

## Mission
[What the agent does]

## Audit Scope
[Checklists organized by category]

## Audit Process
[Step-by-step methodology]

## Output Format
[Report template]

## Rules
[Hard constraints for the agent]

$ARGUMENTS
```

Key sections to customize:
- **Audit Scope**: Add/remove checklist items specific to your project
- **Rules**: Add project-specific constraints
- **Output Format**: Modify report structure to match your team's preferences

### Add a New Agent

1. Create a new `.md` file in `.claude/commands/`:

```bash
touch .claude/commands/my-new-agent.md
```

2. Use this template:

```markdown
# [Agent Name] — [One-line Description]

You are **[Agent Name]**, a specialized [role] focused on [domain] in .NET Core applications with PostgreSQL backends.

## Mission
[2-3 sentences: what this agent does and why]

## Audit Scope

### 1. [Category Name]
- [ ] [Check item 1]
- [ ] [Check item 2]
- [ ] [Check item 3]

### 2. [Category Name]
- [ ] [Check item 1]
- [ ] [Check item 2]

## Audit Process
1. **[Phase]**: [What to do]
2. **[Phase]**: [What to do]
3. **Report Generation**: Produce structured findings

## Output Format
[Define the report markdown template]

## Rules
- [Hard rule 1]
- [Hard rule 2]
- Always provide .NET 8-specific code examples
- [Project-specific rule]

$ARGUMENTS
```

3. The `$ARGUMENTS` placeholder at the end captures any text the user types after the slash command.

4. Test it: `/my-new-agent`

### Agent Ideas for Future Addition

| Agent | Focus |
|-------|-------|
| 🧪 Test Auditor | Test coverage gaps, flaky tests, missing edge cases |
| 📦 Docker Inspector | Dockerfile best practices, image size, security |
| 🔄 CI/CD Auditor | Pipeline security, secret handling, deployment safety |
| 📊 API Design Reviewer | REST conventions, versioning, pagination, error format |
| 🌐 Localization Auditor | i18n completeness, Malay/English coverage, missing translations |
| 📝 Documentation Auditor | Missing XML docs, API docs, README completeness |
| ♻️ Migration Auditor | EF Core migration safety, rollback ability, data loss risk |

---

## Upgrading Agents

### Update Existing Agents

```bash
# Pull latest from repo (if using git)
cd ~/claude-audit-agents
git pull

# Re-copy to your project
cp .claude/commands/*.md /path/to/your-project/.claude/commands/
```

### Version Control Best Practice

Include the `.claude/commands/` directory in your project's git repo:

```bash
# .gitignore — do NOT ignore these
# .claude/commands/  ← Keep this tracked

git add .claude/commands/*.md
git commit -m "chore: add/update audit agents"
```

This way your entire team gets the same agents when they clone the repo.

---

## Project Structure

```
.claude/
└── commands/
    ├── auth-guardian.md          # Auth & session security
    ├── payment-sentinel.md       # Payment flow security
    ├── integration-inspector.md  # 3rd-party API audit
    ├── db-perf-optimizer.md      # Database performance
    ├── security-auditor.md       # Application security (OWASP)
    ├── code-cleaner.md           # Code quality & SOLID
    └── code-explain.md           # Code explanation
```

---

## Tips

1. **Start broad, then go deep**: Run `/security-auditor` first for a full sweep, then `/auth-guardian` for auth-specific depth
2. **Use arguments**: Always pass context like "focus on the payment module" for targeted results
3. **Chain agents**: After `/db-perf-optimizer` identifies N+1 queries, use `/code-cleaner` to refactor the affected services
4. **Explain before cleaning**: Use `/code-explain` on complex code before running `/code-cleaner` so you understand what you're refactoring
5. **Agents are project-agnostic**: While optimized for .NET 8 + PostgreSQL, the patterns apply to any C# backend project
