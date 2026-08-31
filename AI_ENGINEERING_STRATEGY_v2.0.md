# AI Engineering Strategy

## VS Code + Cline + OpenRouter + Web AI + Repomix + GitHub

> **Version:** 2.0  
> **Status:** Active  
> **Purpose:** стандартизировать AI-assisted разработку, архитектурный анализ, code review и evolution проекта.
>
> **Главный принцип:** AI выполняет значительную часть инженерной работы, но Developer сохраняет контроль над архитектурой, требованиями, безопасностью и финальными решениями.

---

# 1. Core Philosophy

AI используется не как один универсальный инструмент, а как набор специализированных контуров.

```text
                         ┌─────────────────────┐
                         │      DEVELOPER      │
                         │ Requirements        │
                         │ Architecture        │
                         │ Decisions           │
                         │ Final Review        │
                         └──────────┬──────────┘
                                    │
              ┌─────────────────────┼─────────────────────┐
              │                     │                     │
              ▼                     ▼                     ▼
       ┌──────────────┐      ┌──────────────┐      ┌──────────────┐
       │    WEB AI    │      │    CLINE     │      │   REPOMIX    │
       │ Consultation │      │ Implement    │      │ Snapshot     │
       │ Research     │      │ Edit         │      │ External     │
       │ Analysis     │      │ Test         │      │ Review       │
       └──────┬───────┘      └──────┬───────┘      └──────┬───────┘
              │                     │                     │
              └─────────────────────┼─────────────────────┘
                                    ▼
                              ┌──────────────┐
                              │     CI/CD    │
                              │ Lint         │
                              │ Typecheck    │
                              │ Test         │
                              │ Build        │
                              │ Deploy       │
                              └──────────────┘
```

# 2. Fundamental Rules

## Rule 1 — Developer decides

AI может анализировать, предлагать, реализовывать, тестировать и критиковать.

Но AI не должен самостоятельно принимать существенные архитектурные решения.

```text
AI Proposal
     ↓
Developer Decision
     ↓
Implementation
```

## Rule 2 — Git is Source of Truth

```text
Git Repository
      =
Current Project State
```

Все AI snapshots, чаты и отчёты являются производными материалами.

## Rule 3 — Cline работает с live repository

Cline используется там, где AI должен непосредственно взаимодействовать с проектом:

- читать файлы;
- искать код;
- создавать файлы;
- изменять код;
- запускать команды;
- запускать тесты;
- анализировать ошибки;
- выполнять refactoring;
- работать с Docker;
- создавать CI/CD.

## Rule 4 — Web AI является консультантом

Web AI используется для:

- research;
- архитектурных обсуждений;
- сравнения технологий;
- анализа документации;
- debugging;
- brainstorming;
- подготовки ADR;
- независимого code review;
- анализа Repomix snapshots.

## Rule 5 — Repomix является snapshot-инструментом

Repomix не является заменой Git или Cline.

```text
Git
 ↓
Repomix
 ↓
Snapshot
 ↓
External AI
```

Repomix применяется преимущественно для:

- architecture review;
- security review;
- scalability review;
- dependency review;
- technical debt review;
- independent second opinion.

# 3. AI Context Strategy

Контекст должен предоставляться AI на минимально необходимом уровне.

## Level 1 — Local Context

```text
function
 ↓
file
 ↓
related tests/types
```

Используется для bug fix, небольшого refactoring, локальной feature и тестов.

## Level 2 — Repository Context

```text
Repository
 ↓
Cline
 ↓
Search
 ↓
Relevant files
 ↓
Implementation
```

Используется для feature, API changes, database changes, cross-package changes и integration.

## Level 3 — External Snapshot Context

```text
Git Repository
 ↓
Repomix
 ↓
Snapshot
 ↓
Web AI
```

Используется для architecture review, security review, scalability review, monorepo review и technical debt analysis.

# 4. Tool Responsibility Matrix

| Responsibility | Web AI | Cline | Repomix | Git | CI |
|---|---:|---:|---:|---:|---:|
| Research | ✅ | | | | |
| Architecture discussion | ✅ | | | | |
| Documentation analysis | ✅ | | | | |
| Repository implementation | | ✅ | | | |
| File modification | | ✅ | | | |
| Terminal | | ✅ | | | |
| Tests | | ✅ | | | ✅ |
| Debugging | ✅ | ✅ | | | |
| Git history | | | | ✅ | |
| Architecture review | ✅ | | ✅ | | |
| Security review | ✅ | | ✅ | | |
| Scalability review | ✅ | | ✅ | | |
| Independent second opinion | ✅ | | ✅ | | |
| Lint | | ✅ | | | ✅ |
| Typecheck | | ✅ | | | ✅ |
| Build | | ✅ | | | ✅ |
| Deployment | | | | | ✅ |

# 5. Development Lifecycle

Каждая существенная задача проходит:

```text
1. Define
2. Research
3. Decide
4. Document
5. Plan
6. Implement
7. Test
8. Review
9. Commit
10. CI
11. Deploy
12. Periodic Architecture Review
```

Не каждая маленькая задача обязана проходить все этапы.

# 6. Define

Задача должна иметь понятный и проверяемый результат.

Плохо:

```text
Fix authentication.
```

Хорошо:

```text
Implement refresh token rotation.

Requirements:

- access token lifetime: 15 minutes
- refresh token rotation
- revoked tokens cannot be reused
- persistent token state
- unit tests
- integration tests
```

# 7. Research

Если решение не очевидно:

```text
Developer
    ↓
Web AI
    ↓
Research
    ↓
Alternatives
    ↓
Trade-offs
    ↓
Developer
```

AI помогает понять варианты, преимущества, недостатки, operational complexity, security и scalability implications.

# 8. Decision

Developer выбирает решение.

Для архитектурных решений используется ADR:

```text
docs/decisions/
├── ADR-001.md
├── ADR-002.md
└── ...
```

## ADR Standard

```markdown
# ADR-XXX: Decision Title

## Status

Proposed | Accepted | Rejected | Superseded

## Context

Why is this decision necessary?

## Decision

What are we choosing?

## Alternatives

What alternatives were considered?

## Consequences

Benefits and disadvantages.

## Constraints

What limitations does this decision introduce?
```

# 9. Cline Planning

Перед изменением repository Cline должен сначала исследовать проект.

```text
You are working inside an existing software repository.

Before making any changes:

1. Inspect the repository structure.
2. Read relevant documentation.
3. Identify the current architecture.
4. Identify package boundaries.
5. Identify dependency direction.
6. Inspect workspace configuration.
7. Identify lint, typecheck, test and build commands.
8. Find all relevant consumers and dependencies.

Do not modify files yet.

Provide:

1. Repository overview
2. Relevant files
3. Existing conventions
4. Dependencies
5. Risks
6. Implementation plan
7. Required tests

Do not change architecture without approval.
Do not add dependencies without justification.
Do not modify unrelated files.

Wait for approval before implementation.
```

# 10. Implementation

После approval:

```text
Implement the approved plan.

Rules:

- Follow existing architecture.
- Follow repository conventions.
- Do not modify unrelated files.
- Do not introduce unnecessary dependencies.
- Preserve existing APIs unless explicitly required.
- Add/update tests.
- Do not change architectural boundaries without approval.
- Do not expose secrets.
```

# 11. Testing

Минимальный quality gate:

```text
Code
 ↓
Lint
 ↓
Typecheck
 ↓
Test
 ↓
Build
```

Для PNPM:

```bash
pnpm lint
pnpm typecheck
pnpm test
pnpm build
```

Для monorepo:

```bash
pnpm -r lint
pnpm -r typecheck
pnpm -r test
pnpm -r build
```

Команды должны соответствовать конкретному проекту.

# 12. Definition of Done

```markdown
## Definition of Done

- [ ] Requirements implemented
- [ ] Existing functionality preserved
- [ ] Tests added/updated
- [ ] Tests pass
- [ ] Lint passes
- [ ] Typecheck passes
- [ ] Build passes
- [ ] No secrets added
- [ ] No unrelated changes
- [ ] Git diff reviewed
- [ ] Architecture remains consistent
```

# 13. Agent Debugging Loop

```text
Read
 ↓
Analyze
 ↓
Edit
 ↓
Test
 ↓
Read error
 ↓
Fix
 ↓
Test
```

## Stop Rule

Если одна и та же проблема не решается после 2–3 попыток:

```text
STOP
 ↓
Collect logs
 ↓
Analyze root cause
 ↓
Consult Web AI if necessary
 ↓
Choose solution
 ↓
Return to Cline
```

Не допускается бесконечный цикл:

```text
fix → test → same error → fix → test
```

# 14. Context Management

Не следует автоматически передавать AI весь repository.

Плохой подход:

```text
500 000 lines
       ↓
LLM
```

Предпочтительный:

```text
Task
 ↓
Search
 ↓
Relevant files
 ↓
Relevant symbols
 ↓
Dependencies
 ↓
LLM
```

Принцип:

> **Minimum sufficient context.**

Но:

> **Never sacrifice correctness merely to reduce tokens.**

# 15. Repository Structure

Для PNPM monorepo:

```text
repository/
│
├── apps/
│   ├── api/
│   ├── web/
│   └── worker/
│
├── packages/
│   ├── auth/
│   ├── database/
│   ├── config/
│   └── shared/
│
├── docs/
│   ├── architecture.md
│   ├── development.md
│   ├── conventions.md
│   ├── security.md
│   ├── decisions/
│   ├── reviews/
│   └── ai/
│       └── prompts/
│
├── .github/
│   └── workflows/
│
├── package.json
├── pnpm-workspace.yaml
├── .gitignore
└── .repomixignore
```

# 16. Architecture Boundaries

Предпочтительное направление:

```text
apps
  ↓
packages
```

Packages не должны зависеть от конкретных applications.

Необходимо избегать:

- circular dependencies;
- скрытых cross-service dependencies;
- shared mutable state;
- зависимости package от конкретного application.

# 17. Dependency Direction

Рекомендуемая модель:

```text
Applications
      ↓
Domain / Shared Packages
      ↓
Infrastructure / Libraries
```

Плохая модель:

```text
app
 ↓
package
 ↓
another app
 ↓
another package
 ↓
app
```

При появлении такой зависимости архитектура должна быть пересмотрена.

# 18. API Changes

Перед изменением API:

```text
API
 ↓
Find consumers
 ↓
Frontend
Backend
Workers
Tests
Integrations
Documentation
```

Cline должен определить:

- кто использует API;
- какие contracts существуют;
- является ли изменение breaking;
- нужны ли migrations;
- какие tests необходимо обновить.

# 19. Database Changes

Database migrations относятся к high-risk changes.

Перед migration необходимо:

1. проверить текущую schema;
2. определить consumers;
3. определить backward compatibility;
4. подготовить migration;
5. обновить tests;
6. проверить rollback strategy;
7. получить approval.

Особое внимание:

```text
DROP
DELETE
TRUNCATE
ALTER
```

Production migrations должны выполняться контролируемо.

# 20. Git Workflow

Перед задачей:

```bash
git status
```

После изменений:

```bash
git diff
git diff --stat
```

Перед commit:

```bash
git diff --check
```

Проверить:

- случайные файлы;
- secrets;
- debug code;
- unrelated changes;
- generated files.

# 21. Atomic Commits

Предпочтительно:

```text
feat(auth): add refresh token rotation
test(auth): add refresh token integration tests
fix(auth): reject revoked refresh tokens
```

Не рекомендуется:

```text
feat: update everything
```

Atomic commits упрощают review, rollback, debugging, architecture analysis и AI-assisted development.

# 22. Repomix Strategy

Repomix является **external analysis boundary**.

```text
Git Repository
      ↓
    Repomix
      ↓
Snapshot
      ↓
External Web AI
      ↓
Independent Review
```

Repomix НЕ используется как:

- Source of Truth;
- постоянная база проекта;
- замена Cline;
- замена Git;
- обязательный этап каждого commit.

# 23. Когда использовать Repomix

Repomix рекомендуется для:

### Architecture Review

```text
Analyze current architecture.
```

### Monorepo Review

```text
Analyze apps/packages boundaries.
```

### Dependency Review

```text
Find problematic dependency directions.
```

### Security Review

```text
Identify potential security risks.
```

### Scalability Review

```text
Identify architectural bottlenecks.
```

### Technical Debt Review

```text
Identify accumulated structural debt.
```

### Independent Second Opinion

```text
Cline implemented the feature.

Now independently review the resulting repository.
```

Последний сценарий особенно важен.

# 24. Когда Repomix НЕ нужен

Не использовать полный snapshot для:

- простого bug fix;
- одной функции;
- небольшого refactoring;
- очевидного typo;
- локального теста;
- простой документации.

Для таких задач обычно достаточно:

```text
Cline
 +
Repository Search
```

# 25. Repomix Snapshot Types

## Architecture Snapshot

```text
repomix-architecture.md
```

Для анализа всей системы.

## Service Snapshot

```text
repomix-auth.md
repomix-api.md
repomix-worker.md
```

Для анализа конкретного bounded context.

## Change Snapshot

Snapshot, сфокусированный на текущем изменении:

```text
Git diff
 +
affected files
 +
related dependencies
```

Используется для PR review, feature review и refactoring review.

# 26. Repomix Review Lifecycle

```text
1. Finish milestone
        ↓
2. Ensure Git state is known
        ↓
3. Generate fresh Repomix snapshot
        ↓
4. Verify excluded files
        ↓
5. Send snapshot to Web AI
        ↓
6. Request independent review
        ↓
7. Collect findings
        ↓
8. Validate findings against repository
        ↓
9. Developer decides
        ↓
10. Create TASK / ADR if required
```

# 27. Snapshot Freshness

Repomix snapshot является временным.

```text
10:00
Git
 ↓
Repomix
 ↓
snapshot.md

10:30
Cline modifies repository

11:00
snapshot.md = outdated
```

Поэтому architectural review должен проводиться по свежему snapshot.

# 28. Repomix Security Boundary

Перед экспортом repository необходимо исключить:

```text
.env*
*.pem
*.key
credentials/
secrets/
private keys
production data
customer data
```

Также следует исключать:

```text
node_modules/
dist/
build/
coverage/
.cache/
logs/
```

Но dependency lockfiles, например:

```text
pnpm-lock.yaml
```

могут быть полезны для dependency analysis.

# 29. Security Principle

Security scanning не является разрешением отправлять repository наружу.

Принцип:

```text
Automatic exclusion
        +
Security scan
        +
Manual verification
```

Только после этого snapshot передаётся внешнему AI.

# 30. Independent Architecture Review

После значимого milestone:

```text
Cline
 ↓
Implementation
 ↓
Tests
 ↓
Git Commit
 ↓
Repomix
 ↓
External Web AI
 ↓
Architecture Review
 ↓
Developer
```

AI reviewer не должен автоматически менять repository.

# 31. Architecture Review Prompt

```text
Analyze this repository snapshot as an independent software architecture reviewer.

Do not modify the code.

First understand the existing architecture.

Review:

1. Architecture
2. Package boundaries
3. Service boundaries
4. Dependency direction
5. Coupling
6. Cohesion
7. API contracts
8. Database boundaries
9. Error handling
10. Security
11. Scalability
12. Observability
13. Testing
14. Maintainability
15. Technical debt

Do not recommend rewriting working code merely because another approach is theoretically better.

Focus on concrete engineering risks.

For every finding provide:

- Severity
- Evidence
- Problem
- Why it matters
- Recommended action
- Potential downside

Separate:

- Confirmed problems
- Probable problems
- Suggestions
- Optional improvements
```

# 32. AI Findings Must Be Validated

AI review не является истиной.

```text
AI:
"Package A has a circular dependency."

        ↓

Developer verifies repository

        ↓

Confirmed
    ↓
TASK / ADR

Not confirmed
    ↓
Reject finding
```

Никогда не следует менять архитектуру только потому, что AI предложил более «красивую» структуру.

# 33. Multi-Model Review

Для важных milestone можно использовать несколько моделей.

```text
                    Repomix
                       │
          ┌────────────┼────────────┐
          ▼            ▼            ▼
       Model A       Model B      Model C
          │            │            │
          ▼            ▼            ▼
    Architecture   Security      Quality
       Review        Review        Review
          │            │            │
          └────────────┼────────────┘
                       ▼
                   Developer
```

Если модели дают разные рекомендации:

```text
Model A ≠ Model B
       ↓
Developer investigates
```

Не:

```text
majority vote = truth
```

# 34. Architecture Evolution

Архитектура является эволюционной.

```text
Initial Architecture
        ↓
Feature
        ↓
New Requirements
        ↓
New Knowledge
        ↓
Architecture Review
        ↓
Improvement
```

Периодически необходимо проверять:

- coupling;
- cohesion;
- boundaries;
- dependency direction;
- technical debt;
- scalability;
- testability.

# 35. Architecture Review Frequency

Не нужно запускать полный review после каждого commit.

Рекомендуемые события:

### Major milestone

```text
v0.1
v0.2
v0.3
```

### Architectural change

```text
new service
new package
new database
new messaging system
new authentication system
```

### Before major release

```text
release candidate
 ↓
architecture review
```

### После большого refactoring

```text
large refactoring
 ↓
independent review
```

# 36. Cost Strategy

Стоимость AI зависит от:

```text
Context size
+
Request count
+
Tool calls
+
Reasoning
+
Conversation history
+
Repeated attempts
```

Поэтому модель выбирается по сложности задачи.

## Cheap / Fast Model

Использовать для:

- boilerplate;
- CRUD;
- простых tests;
- небольших fixes;
- documentation;
- очевидных refactoring.

## Reasoning Model

Использовать для:

- сложного debugging;
- concurrency;
- distributed systems;
- database consistency;
- архитектуры;
- сложного refactoring;
- анализа нескольких компонентов.

# 37. Context Cost Control

Не нужно отправлять AI:

```text
entire repository
```

если задача требует:

```text
3 files
```

Правильный принцип:

> **Minimum sufficient context.**

Но:

> **Never sacrifice correctness merely to reduce tokens.**

Если для понимания архитектуры требуется весь repository — полный context оправдан.

# 38. Web AI Cost Optimization

Web AI полезно использовать для:

- предварительного research;
- объяснения ошибок;
- анализа документации;
- альтернативных решений;
- независимого review.

Результат Web AI передаётся Cline только после проверки Developer.

```text
Web AI
 ↓
Proposal
 ↓
Developer validation
 ↓
Cline
```

# 39. Local AI

При наличии подходящего hardware можно использовать:

- Ollama;
- LM Studio;
- локальные coding models;
- локальные inference tools.

Основное преимущество:

```text
No API cost
+
Local data
```

Выбор между local и cloud делается по:

- quality;
- latency;
- hardware;
- privacy;
- cost;
- context size.

# 40. Cline Model Strategy

```text
Simple task
    ↓
Fast / cheap model

Complex implementation
    ↓
Stronger model

Architecture / difficult debugging
    ↓
Reasoning model
```

Не следует использовать самую дорогую модель для каждого запроса.

# 41. Conversation Management

### New task

Использовать для:

- новой feature;
- нового bug;
- нового architectural change.

### Continue context

Использовать, если предыдущий context действительно нужен.

### Compact

Использовать, когда текущая история всё ещё релевантна, но context становится слишком большим.

Основной принцип:

> Старый контекст не должен сохраняться только потому, что он существует.

# 42. Agent Safety

Cline может взаимодействовать с filesystem и terminal.

Особое внимание:

```text
git reset --hard
rm
rm -rf
docker compose down -v
database migrations
production commands
git push
deployment
secret changes
```

Destructive operations должны требовать явного понимания и approval.

# 43. Dependency Policy

Перед добавлением dependency AI должен проверить:

```text
1. Does an existing dependency solve the problem?
2. Is the new dependency necessary?
3. Is it maintained?
4. Does it introduce security risks?
5. Does it increase bundle size?
6. Does it increase operational complexity?
7. Does it duplicate existing functionality?
```

# 44. CI/CD Quality Gate

```text
Git Push
   ↓
GitHub Actions
   ↓
Install
   ↓
Lint
   ↓
Typecheck
   ↓
Test
   ↓
Build
   ↓
Deploy
```

Production deployment разрешается только после успешного quality gate.

# 45. CI Is Not a Substitute for Local Testing

До push:

```text
Local
 ↓
lint
 ↓
typecheck
 ↓
test
 ↓
build
```

После push:

```text
CI
 ↓
same quality gate
```

CI должен подтверждать результат, а не впервые обнаруживать базовые ошибки.

# 46. Documentation Strategy

Документация проекта должна находиться в Git.

```text
docs/
├── architecture.md
├── development.md
├── conventions.md
├── security.md
├── decisions/
└── reviews/
```

История Web AI не является документацией.

Если AI предложил важное решение:

```text
AI discussion
 ↓
Developer decision
 ↓
ADR
 ↓
Git
```

# 47. AI Review Reports

Результаты существенных AI reviews можно сохранять:

```text
docs/reviews/
├── architecture-2026-08.md
├── security-2026-08.md
└── monorepo-2026-08.md
```

Report должен быть:

- датирован;
- связан с commit/tag;
- понятен без исходного AI chat;
- проверен Developer.

Пример:

```markdown
Commit: abc123
Review Date: 2026-08-30
Reviewer: External AI

Findings:
- HIGH: ...
- MEDIUM: ...
- LOW: ...

Developer Decision:
- HIGH → TASK-123
- MEDIUM → accepted risk
- LOW → rejected
```

# 48. AI Prompt Library

Рекомендуемые prompts:

```text
docs/ai/prompts/
├── initialization.md
├── feature.md
├── debugging.md
├── refactoring.md
├── architecture-review.md
├── security-review.md
└── release-review.md
```

Prompt library должна версионироваться вместе с проектом.

# 49. Standard Feature Prompt

```text
Implement the following feature:

[FEATURE]

Requirements:

[REQUIREMENTS]

Constraints:

[CONSTRAINTS]

Before editing:

1. Inspect the relevant code.
2. Find all affected components.
3. Find existing consumers.
4. Explain the implementation plan.
5. Identify risks.

After approval:

1. Implement the feature.
2. Add/update tests.
3. Run lint.
4. Run typecheck.
5. Run tests.
6. Run build if relevant.
7. Review the final git diff.

Do not modify unrelated files.
Do not introduce unnecessary dependencies.
Do not change architectural boundaries without approval.
```

# 50. Standard Debugging Prompt

```text
Investigate this issue:

[ERROR]

Before changing anything:

1. Reproduce the problem.
2. Inspect the relevant implementation.
3. Identify the root cause.
4. Explain why the current implementation fails.
5. Propose the smallest safe fix.

Do not make speculative changes.

After approval:

1. Implement the fix.
2. Add a regression test.
3. Run relevant tests.
4. Run typecheck.
5. Run lint.
6. Review the final diff.
```

# 51. Standard Refactoring Prompt

```text
Refactor the following area:

[TARGET]

Goals:

[GOALS]

Constraints:

- Preserve behavior.
- Preserve public APIs unless explicitly required.
- Do not introduce unnecessary dependencies.
- Do not modify unrelated components.
- Do not change architectural boundaries without approval.

Before editing:

1. Identify current behavior.
2. Identify consumers.
3. Identify tests.
4. Identify risks.
5. Propose a step-by-step refactoring plan.

After approval:

1. Implement incrementally.
2. Run tests after each meaningful step.
3. Run lint.
4. Run typecheck.
5. Review git diff.
```

# 52. Final Engineering Loop

```text
                         IDEA
                           │
                           ▼
                    ┌──────────────┐
                    │    WEB AI    │
                    │   Research   │
                    └──────┬───────┘
                           │
                           ▼
                    ┌──────────────┐
                    │  DEVELOPER   │
                    │   DECISION   │
                    └──────┬───────┘
                           │
                           ▼
                    ┌──────────────┐
                    │  ADR / TASK  │
                    └──────┬───────┘
                           │
                           ▼
                    ┌──────────────┐
                    │    CLINE     │
                    │     PLAN     │
                    └──────┬───────┘
                           │
                       APPROVAL
                           │
                           ▼
                    ┌──────────────┐
                    │ IMPLEMENT    │
                    └──────┬───────┘
                           │
                           ▼
                    ┌──────────────┐
                    │ TEST / LINT  │
                    │ TYPECHECK    │
                    │ BUILD        │
                    └──────┬───────┘
                           │
                           ▼
                       GIT DIFF
                           │
                           ▼
                        COMMIT
                           │
                           ▼
                         PUSH
                           │
                           ▼
                          CI
                           │
                           ▼
                        DEPLOY
                           │
                           ▼
                      MILESTONE
                           │
                           ▼
                        REPOMIX
                           │
                           ▼
                     EXTERNAL AI
                         REVIEW
                           │
                           ▼
                      DEVELOPER
                           │
                    ┌──────┴──────┐
                    ▼             ▼
                  ADR/TASK      ACCEPT
                    │
                    ▼
                  CLINE
```

# 53. Golden Rules

## Architecture

1. **Developer decides — AI proposes.**
2. **Git is the Source of Truth.**
3. **Architecture decisions are documented in ADR.**
4. **Do not allow AI to silently change architectural boundaries.**
5. **Prefer evolutionary architecture over premature complexity.**

## Development

6. **Cline is the primary implementation agent.**
7. **Use the minimum sufficient context.**
8. **Large tasks must be divided into verifiable steps.**
9. **Tests are part of implementation, not an afterthought.**
10. **Review `git diff` before every commit.**

## Repomix

11. **Repomix is a snapshot, not the source of truth.**
12. **Do not use full Repomix for every task.**
13. **Use Repomix primarily for independent review.**
14. **Generate a fresh snapshot before major architecture review.**
15. **Never expose secrets or production data in snapshots.**

## AI Review

16. **AI review is an opinion, not a fact.**
17. **Validate important AI findings against the actual repository.**
18. **Multiple models may provide independent opinions.**
19. **Do not accept a recommendation merely because it sounds sophisticated.**

## Security

20. **Destructive commands require approval.**
21. **Secrets must never enter AI context.**
22. **Production operations require explicit control.**

## Cost

23. **Use cheap models for simple work.**
24. **Use reasoning models for genuinely difficult problems.**
25. **Do not waste tokens on irrelevant repository context.**
26. **Stop repeated failed agent loops and investigate the root cause.**

## Quality

27. **Local checks before push.**
28. **CI is the final technical quality gate.**
29. **Architecture should be reviewed periodically.**
30. **Every significant architectural change should leave a trace in Git.**

# 54. Final Principle

The objective is not:

> **"Let AI build the project."**

The objective is:

> **"Build an engineering system in which AI performs a large amount of development work while architecture, source of truth, security, quality gates and final decisions remain under developer control."**

The final model is:

```text
                WEB AI
             Think / Research
                   │
                   ▼
               DEVELOPER
                 Decide
                   │
                   ▼
             ADR / TASK
                   │
                   ▼
                CLINE
        Implement / Test / Debug
                   │
                   ▼
                  GIT
            Source of Truth
                   │
             ┌─────┴─────┐
             ▼           ▼
            CI         REPOMIX
             │           │
             ▼           ▼
          Deploy     External AI
                       Review
                          │
                          ▼
                      DEVELOPER
```

## One sentence summary

> **Web AI думает и консультирует → Developer принимает решения → Cline реализует → Git фиксирует истину → CI проверяет → Repomix периодически выносит snapshot наружу → независимый AI критикует → Developer решает, что улучшать дальше.**
