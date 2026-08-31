# AI Engineering Strategy

## VS Code + Cline + OpenRouter + Web AI + Repomix + GitHub

> **Version:** 2.1  
> **Status:** Active  
> **Purpose:** стандартизировать AI-assisted разработку, архитектурный анализ, targeted code review, security review и evolution проекта.

---

# 1. Executive Summary

Эта стратегия определяет инженерный процесс, в котором AI выполняет значительную часть разработки, анализа, тестирования и review, но **Developer сохраняет контроль над архитектурой, требованиями, безопасностью, Source of Truth и финальными решениями**.

Главный принцип:

> **AI должен получать не максимум контекста, а минимальный достаточный контекст, который позволяет корректно ответить на конкретный инженерный вопрос.**

Для реализации этого принципа Repomix используется не как механизм постоянной передачи всего repository, а как **контролируемый context-packaging layer** между Git/repository и внешним AI.

Целевая модель:

```text
                    WEB AI
              Research / Review
                       │
                       ▼
                  DEVELOPER
              Decision / Approval
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
          ┌────────────┼────────────┐
          ▼            ▼            ▼
         CI         Repomix       History
          │            │
          ▼            ▼
       Quality      Targeted
        Gate        Context
                       │
                       ▼
                 External AI
                     Review
                       │
                       ▼
                  DEVELOPER
                 Validate / Decide
```

---

# 2. Core Philosophy

AI не рассматривается как один универсальный инструмент.

Каждый инструмент имеет отдельную ответственность:

```text
Developer
    │
    ├── requirements
    ├── architecture
    ├── decisions
    ├── security responsibility
    └── final approval
            │
            ├──────────────┬──────────────┐
            ▼              ▼              ▼
         Web AI          Cline         Repomix
       Think/Research   Implement     Package Context
            │              │              │
            └──────────────┼──────────────┘
                           ▼
                          Git
                           │
                           ▼
                           CI
```

## Fundamental principles

1. **Developer decides — AI proposes.**
2. **Git is the Source of Truth.**
3. **Cline works with the live repository.**
4. **Web AI is a consultant and independent reviewer.**
5. **Repomix packages selected repository context.**
6. **Minimum sufficient context is preferred.**
7. **Correctness has priority over token minimization.**
8. **Important AI findings must be validated against the repository.**
9. **Architectural decisions leave a trace in Git/ADR.**
10. **Security boundaries are explicit and enforced before external sharing.**

---

# 3. Tool Responsibility

| Responsibility | Developer | Web AI | Cline | Repomix | Git | CI |
|---|---:|---:|---:|---:|---:|---:|
| Requirements | ✅ | Assist | | | | |
| Architecture decisions | ✅ | Assist | | | | |
| Research | | ✅ | | | | |
| Repository exploration | | | ✅ | | | |
| Implementation | Approval | | ✅ | | | |
| File modification | Approval | | ✅ | | | |
| Terminal | Approval | | ✅ | | | |
| Tests | | | ✅ | | | ✅ |
| Git history | | | | | ✅ | |
| Change detection | | | | | ✅ | |
| Context packaging | | | | ✅ | | |
| Architecture review | ✅ | ✅ | | ✅ | | |
| Security review | ✅ | ✅ | | ✅ | | |
| Scalability review | ✅ | ✅ | | ✅ | | |
| Independent review | ✅ | ✅ | | ✅ | | |
| Lint/typecheck/build | | | ✅ | | | ✅ |
| Deployment | Approval | | | | | ✅ |

---

# 4. Source of Truth

Git is the authoritative representation of the project.

```text
Git Repository
      =
Current Project State
```

AI conversations, Repomix snapshots, review reports and generated plans are **derived artifacts**.

They must never silently override the repository.

The hierarchy is:

```text
Git
 │
 ├── source code
 ├── configuration
 ├── tests
 ├── documentation
 └── history
       │
       ├── Repomix snapshot
       ├── AI review
       └── derived recommendations
```

An AI statement such as:

```text
"Architecture should be changed."
```

is only a proposal.

The valid process is:

```text
AI Proposal
    ↓
Developer Validation
    ↓
Decision
    ↓
ADR / TASK
    ↓
Implementation
    ↓
Git
```

---

# 5. AI Context Strategy

## 5.1 Minimum Sufficient Context

Не следует автоматически передавать AI весь repository.

Плохой подход:

```text
500,000 lines
       ↓
      LLM
```

Предпочтительный:

```text
Question
   ↓
Task
   ↓
Search
   ↓
Relevant symbols
   ↓
Relevant files
   ↓
Consumers
   ↓
Tests
   ↓
Dependencies
   ↓
LLM
```

Главное правило:

> **Minimum sufficient context.**

Но оно имеет обязательное исключение:

> **Never sacrifice correctness merely to reduce tokens.**

Если для ответа на вопрос действительно необходим весь repository, полный context оправдан.

---

# 6. Context Levels

Контекст должен масштабироваться постепенно.

## C0 — Symbol Context

```text
function
class
method
type
interface
```

Использование:

- локальный bug;
- маленькая логическая ошибка;
- точечное объяснение.

## C1 — File Context

```text
target file
+
imports
+
local types
```

Использование:

- локальный refactoring;
- небольшая feature;
- unit test.

## C2 — Subsystem Context

```text
source
+
tests
+
types
+
local dependencies
```

Использование:

- feature внутри одного subsystem;
- сложный bug;
- изменение поведения.

## C3 — Dependency Neighborhood

```text
changed code
+
direct consumers
+
reverse consumers
+
interfaces
+
tests
+
relevant dependencies
```

Использование:

- API changes;
- cross-file changes;
- shared package changes;
- integration.

## C4 — Service / Package Context

```text
package/service
+
contracts
+
configuration
+
consumers
+
tests
+
dependency boundaries
```

Использование:

- service changes;
- package refactoring;
- database changes;
- cross-service behavior.

## C5 — Repository Context

```text
whole repository
```

Использование:

- architecture review;
- monorepo review;
- major refactoring;
- release architecture review;
- systemic security/scalability review.

---

# 7. Context Expansion Principle

Начинать необходимо с минимального контекста.

```text
Minimal Context
      ↓
    Review
      ↓
Can correctness be established?
      │
   ┌──┴──┐
  YES    NO
   │      │
   ▼      ▼
 DONE   EXPAND
          │
          ▼
       Review
```

AI должен иметь возможность явно сказать:

```text
"I cannot establish this conclusion from the supplied context."
```

Это лучше, чем заставлять модель делать предположения.

## Context Expansion Rule

Если reviewer не может подтвердить вывод:

1. определить, какой evidence отсутствует;
2. расширить scope;
3. добавить только необходимый context;
4. повторить review.

Пример:

```text
C2
 ↓
review
 ↓
missing consumer
 ↓
C3
 ↓
review
 ↓
missing package boundary
 ↓
C4
```

---

# 8. Repomix as Context Packaging Layer

Repomix является **external analysis boundary**.

```text
Git Repository
      ↓
Change / Review Scope
      ↓
Repomix
      ↓
Snapshot
      ↓
External AI
```

Repomix НЕ является:

- Source of Truth;
- заменой Git;
- заменой Cline;
- постоянной базой проекта;
- обязательным этапом каждого commit;
- причиной для отправки всего repository наружу.

Основная задача Repomix:

> **сформировать воспроизводимый, ограниченный и проверяемый набор evidence для конкретного анализа.**

---

# 9. Targeted Repomix

После изменений, внесённых AI-агентом, не следует автоматически создавать полный snapshot.

Правильный процесс:

```text
Cline
  ↓
Implementation
  ↓
Tests
  ↓
git diff
  ↓
Change Classification
  ↓
Impact Analysis
  ↓
Review Scope
  ↓
Repomix
  ↓
External Review
```

Таким образом Repomix является **последним этапом формирования context**, а не первым.

---

# 10. Evidence-Based Review

Review должен основываться на evidence.

Recommended evidence layers:

```text
L1  Task / Requirements
L2  Git Diff
L3  Changed Files
L4  Direct Consumers
L5  Tests
L6  Dependency Context
L7  Architecture Context
L8  Configuration / Contracts
```

Не все layers нужны для каждой задачи.

Например, локальный change:

```text
L1 + L2 + L3 + L5
```

API change:

```text
L1 + L2 + L3 + L4 + L5 + L8
```

Architecture change:

```text
L1 + L2 + L3 + L6 + L7 + L8
```

---

# 11. Git Diff as Review Anchor

Для post-implementation review основным anchor должен быть Git diff.

Минимальная структура:

```text
BASE COMMIT
TARGET COMMIT

TASK
REQUIREMENTS

GIT DIFF

CHANGED FILES

AFFECTED CONSUMERS

TESTS

RELEVANT DEPENDENCIES

REVIEW OBJECTIVE
```

Reviewer должен отвечать прежде всего на вопрос:

> **Does the change correctly implement the requested behavior without introducing unintended consequences?**

а не:

> "Как можно было бы переписать этот repository красивее?"

---

# 12. Before / After / Diff Model

Для важных изменений полезно рассматривать три состояния:

```text
BASE
  │
  ├──────────────► DIFF
  │
  ▼
CURRENT
```

Review context:

```text
BASE STATE
+
CURRENT STATE
+
CHANGESET
+
REQUIREMENTS
```

Это позволяет проверить:

- что именно изменилось;
- что не должно было измениться;
- какие behavioral changes появились;
- какие architectural boundaries затронуты;
- какие regressions возможны.

---

# 13. Review Contract

Каждый существенный external review должен иметь явный Review Contract.

Пример:

```yaml
review:
  type: change-review

base_commit: abc123
target_commit: def456

objective:
  - correctness
  - regression
  - architecture
  - security

scope:
  - packages/auth
  - apps/api

out_of_scope:
  - frontend
  - deployment
  - unrelated packages

requirements:
  - refresh tokens rotate
  - revoked tokens cannot be reused
  - old token becomes invalid

constraints:
  - preserve API
  - no unnecessary dependencies
```

Review Contract отвечает на четыре вопроса:

1. Что проверяется?
2. На каком состоянии?
3. Какие части repository входят в scope?
4. Какие части находятся вне scope?

---

# 14. Review Types

## R1 — Change Review

Для конкретного изменения.

```text
Git diff
+
changed files
+
tests
+
consumers
```

Цель:

- correctness;
- regression;
- scope compliance;
- test coverage.

---

## R2 — Impact Review

Для изменений с широким blast radius.

```text
changed files
+
direct dependencies
+
reverse dependencies
+
API contracts
+
tests
+
configuration
```

Цель:

- определить affected components;
- найти скрытых consumers;
- выявить breaking changes.

---

## R3 — Security Review

Контекст:

```text
authentication
authorization
validation
tokens
permissions
database access
external integrations
configuration
error handling
logging
```

Цель:

- security vulnerabilities;
- privilege escalation;
- secret exposure;
- insecure defaults;
- trust-boundary violations.

---

## R4 — Architecture Review

Контекст:

```text
apps
packages
services
interfaces
dependency graph
configuration
architecture documentation
```

Цель:

- coupling;
- cohesion;
- boundaries;
- dependency direction;
- scalability;
- maintainability;
- technical debt.

---

## R5 — Release Review

Перед major release:

```text
architecture
+
critical paths
+
recent changes
+
tests
+
CI/CD
+
configuration
+
operational concerns
```

---

# 15. Change Classification

После Cline изменения должны быть классифицированы.

```text
LOCAL
  C0-C2

CROSS-FILE
  C2-C3

API / CONTRACT
  C3-C4

DATABASE
  C3-C4

ARCHITECTURAL
  C4-C5

SYSTEMIC
  C5
```

Пример:

```text
one function changed
    ↓
LOCAL
    ↓
no Repomix required
```

Другой:

```text
authentication package changed
    ↓
API consumers found
    ↓
C3/C4
    ↓
targeted Repomix
```

---

# 16. Change Snapshot

Основной snapshot для post-agent review:

```text
Change Snapshot
=
Git diff
+
changed files
+
affected consumers
+
tests
+
relevant dependencies
+
task requirements
```

Он предназначен для:

- PR review;
- feature review;
- refactoring review;
- AI-agent output review.

---

# 17. Architecture Snapshot

Используется для системного анализа:

```text
apps
+
packages
+
services
+
dependency relationships
+
configuration
+
architecture documentation
```

Не следует использовать его для каждой маленькой задачи.

---

# 18. Service / Package Snapshot

Для bounded context:

```text
repomix-auth.md
repomix-api.md
repomix-worker.md
```

Контекст:

```text
service/package
+
interfaces
+
consumers
+
tests
+
configuration
```

---

# 19. Snapshot Freshness

Snapshot является временным artifact.

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

Поэтому:

> **Review должен ссылаться на конкретный commit/state, а snapshot должен быть generated from that state.**

Для значимого review необходимо фиксировать:

```text
base commit
target commit
generation date
review date
snapshot type
scope
```

---

# 20. Security Boundary

Перед внешним export необходимо исключать:

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

Также обычно исключаются:

```text
node_modules/
dist/
build/
coverage/
.cache/
logs/
```

Dependency lockfiles могут быть полезны:

```text
pnpm-lock.yaml
package-lock.json
yarn.lock
```

если они нужны для dependency analysis.

Security process:

```text
Automatic exclusion
       +
Security scan
       +
Manual verification
       ↓
External AI
```

Важно:

> Security scanning не является разрешением отправлять repository наружу.

---

# 21. Repomix Configuration Principles

`.repomixignore` должен быть защитным слоем.

Пример:

```gitignore
# Secrets
.env
.env.*
!.env.example
*.pem
*.key
*.p12
*.pfx

# Credentials
credentials/
secrets/
private/
certificates/

# Production data
*.sql
*.dump
*.bak
backups/
data/

# Generated
node_modules/
dist/
build/
coverage/
.cache/
.tmp/
logs/

# IDE
.vscode/
.idea/

# OS
.DS_Store
Thumbs.db
```

При этом repository-specific exceptions должны быть осознанными.

Не следует бездумно исключать:

```text
package manifests
lockfiles
workspace configuration
architecture docs
tests
CI configuration
API contracts
```

если они необходимы для конкретного review.

---

# 22. Snapshot Manifest

Каждый significant snapshot рекомендуется сопровождать manifest.

Пример:

```yaml
snapshot:
  type: change-review
  generated_at: 2026-08-31

repository:
  base_commit: abc123
  target_commit: def456

scope:
  - packages/auth/src
  - packages/auth/test
  - apps/api/src/auth

excluded:
  - secrets
  - generated
  - unrelated applications

evidence:
  - git-diff
  - changed-files
  - consumers
  - tests
  - dependencies

review:
  objective:
    - correctness
    - regression
    - security
```

Manifest делает snapshot воспроизводимым и объяснимым.

---

# 23. Standard Post-Agent Workflow

После значимого изменения:

```text
1. Cline implementation
        ↓
2. Local tests
        ↓
3. git status
        ↓
4. git diff
        ↓
5. git diff --stat
        ↓
6. classify change
        ↓
7. determine impact
        ↓
8. determine context level
        ↓
9. create targeted snapshot
        ↓
10. verify exclusions
        ↓
11. external AI review
        ↓
12. collect findings
        ↓
13. validate findings
        ↓
14. Developer decision
        ↓
15. TASK / ADR if required
        ↓
16. Cline fixes
        ↓
17. tests
        ↓
18. Git commit
```

---

# 24. Review Should Be Evidence-Driven

Не следует давать reviewer только instruction:

```text
Review this code.
```

Лучше:

```text
Review the supplied change against the supplied requirements.

Use only evidence available in the snapshot.

Do not assume missing information.

If the evidence is insufficient, explicitly state what is missing.

Do not recommend architectural rewrites merely because another design is theoretically cleaner.
```

---

# 25. Standard Change Review Prompt

```text
You are an independent software engineer reviewing an AI-generated change.

Do not modify the code.

First understand:
1. The task.
2. The requirements.
3. The base state.
4. The current state.
5. The Git diff.
6. The affected components.
7. The tests.

Review specifically for:

1. Requirement correctness
2. Behavioral correctness
3. Regression risk
4. API compatibility
5. Dependency impact
6. Error handling
7. Security implications
8. Test coverage
9. Architectural consistency
10. Unintended scope expansion

For every finding provide:

- Severity
- Evidence
- Location
- Problem
- Why it matters
- Recommended action
- Potential downside

Classify every finding as:

- CONFIRMED
- PROBABLE
- UNCERTAIN
- SUGGESTION
- OUT_OF_SCOPE

Do not infer facts that are not supported by the supplied repository context.

If information is missing, state the missing evidence.

Do not recommend rewriting working code merely because another approach is theoretically better.

Focus on concrete engineering risks.
```

---

# 26. Standard Architecture Review Prompt

```text
Analyze this repository snapshot as an independent software architecture reviewer.

Do not modify the code.

First reconstruct the existing architecture.

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
10. Security boundaries
11. Scalability
12. Observability
13. Testing
14. Maintainability
15. Technical debt

Do not recommend rewriting working code merely because another architecture is theoretically better.

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
- Uncertain findings
- Suggestions
- Optional improvements

If the snapshot is insufficient to establish a conclusion, explicitly state what additional context is required.
```

---

# 27. Standard Security Review Prompt

```text
You are performing an independent security review.

Do not modify the code.

Review the supplied implementation for:

1. Authentication
2. Authorization
3. Input validation
4. Trust boundaries
5. Secret handling
6. Token/session handling
7. Database access
8. External integrations
9. Error handling
10. Logging
11. Sensitive data exposure
12. Dependency risks
13. Privilege escalation
14. Injection risks
15. Configuration weaknesses

For each finding provide:

- Severity
- Evidence
- Attack/Failure scenario
- Impact
- Recommended mitigation
- Residual risk

Do not claim a vulnerability without evidence.

Distinguish confirmed issues from hypotheses.

If required context is missing, identify it explicitly.
```

---

# 28. Standard Regression Review Prompt

```text
Review the supplied change for regression risk.

Compare:

BASE
CURRENT
DIFF
REQUIREMENTS
TESTS

Identify:

1. Existing behavior that may have changed.
2. Consumers that may break.
3. API compatibility issues.
4. Edge cases not covered.
5. Missing regression tests.
6. Configuration or deployment implications.
7. Data compatibility issues.

Prioritize concrete evidence.

Do not invent consumers or runtime behavior that cannot be established from the supplied context.
```

---

# 29. AI Finding Classification

AI review output is not truth.

Every finding should be classified:

```text
CONFIRMED
    Evidence directly establishes the problem.

PROBABLE
    Strong evidence exists, but repository/runtime validation is still needed.

UNCERTAIN
    Insufficient evidence.

SUGGESTION
    Improvement rather than defect.

OUT_OF_SCOPE
    Potentially valid, but outside current review scope.
```

Example:

```text
AI:
"Package A has a circular dependency."

        ↓

Developer verifies repository

        ↓

Confirmed
    ↓
TASK / ADR

or

Not confirmed
    ↓
Reject finding
```

---

# 30. Multi-Model Review

Для важных milestone можно использовать несколько моделей.

```text
                    Targeted Snapshot
                           │
             ┌─────────────┼─────────────┐
             ▼             ▼             ▼
          Model A       Model B       Model C
             │             │             │
             ▼             ▼             ▼
        Correctness     Security     Architecture
             │             │             │
             └─────────────┼─────────────┘
                           ▼
                       Developer
```

Разные модели являются независимыми источниками мнения.

Не следует использовать:

```text
majority vote = truth
```

Если модели расходятся:

```text
Model A ≠ Model B
       ↓
Developer investigates
       ↓
Repository evidence
       ↓
Decision
```

---

# 31. Architecture Review Frequency

Полный architecture review не нужен после каждого commit.

Рекомендуемые события:

## Major milestone

```text
v0.1
v0.2
v0.3
```

## Architectural change

```text
new service
new package
new database
new messaging system
new authentication system
```

## Major release

```text
release candidate
      ↓
architecture review
```

## Large refactoring

```text
large refactoring
      ↓
independent review
```

---

# 32. Cline Planning

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

---

# 33. Implementation Rules

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

---

# 34. Testing Quality Gate

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

Команды должны соответствовать конкретному repository.

---

# 35. Git Workflow

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
- generated files;
- unexpected dependency changes.

---

# 36. Atomic Commits

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

Atomic commits упрощают:

- review;
- rollback;
- debugging;
- architecture analysis;
- AI-assisted development;
- targeted snapshot generation.

---

# 37. API Changes

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

Для external review API changes обычно требуют минимум C3 context.

---

# 38. Database Changes

Database migrations относятся к high-risk changes.

Перед migration:

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

Для external review обычно требуется C3/C4 context.

---

# 39. Architecture Boundaries

Для PNPM monorepo предпочтительно:

```text
apps
  ↓
packages
```

Packages не должны зависеть от конкретных applications.

Избегать:

- circular dependencies;
- скрытых cross-service dependencies;
- shared mutable state;
- зависимости package от конкретного application.

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

---

# 40. Dependency Policy

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

Dependency changes should increase review scope when they affect runtime, security, build or architecture.

---

# 41. Agent Debugging Loop

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

---

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

---

# 43. Definition of Done

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
- [ ] Required review completed
- [ ] Findings validated
- [ ] TASK/ADR created where required
```

---

# 44. Documentation Strategy

Документация проекта должна находиться в Git.

```text
docs/
├── architecture.md
├── development.md
├── conventions.md
├── security.md
├── decisions/
├── reviews/
└── ai/
    └── prompts/
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

---

# 45. AI Review Reports

Результаты существенных reviews можно сохранять:

```text
docs/reviews/
├── architecture-2026-08.md
├── security-2026-08.md
├── change-2026-08-31-auth.md
└── release-2026-08.md
```

Report должен быть:

- датирован;
- связан с commit/tag;
- содержать snapshot type;
- содержать scope;
- понятен без исходного AI chat;
- иметь статус findings;
- отражать Developer decision.

Пример:

```markdown
# Change Review

Commit: abc123
Base Commit: 987xyz
Review Date: 2026-08-31
Snapshot Type: R1 Change Review
Context Level: C3
Reviewer: External AI

## Findings

### HIGH
...

### MEDIUM
...

### LOW
...

## Developer Decision

- HIGH → TASK-123
- MEDIUM → accepted risk
- LOW → rejected

## Validation

- [x] Repository verified
- [x] Tests verified
- [x] Git diff verified
```

---

# 46. AI Prompt Library

Рекомендуемая структура:

```text
docs/ai/prompts/
├── initialization.md
├── feature.md
├── debugging.md
├── refactoring.md
├── change-review.md
├── regression-review.md
├── architecture-review.md
├── security-review.md
└── release-review.md
```

Prompt library должна версионироваться вместе с project.

---

# 47. Cost Strategy

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

## Fast / Cheap

- boilerplate;
- CRUD;
- simple tests;
- small fixes;
- documentation;
- obvious refactoring.

## Reasoning Model

- complex debugging;
- concurrency;
- distributed systems;
- database consistency;
- architecture;
- complex refactoring;
- multi-component analysis.

Главный принцип:

> **Context optimization часто важнее выбора самой дорогой модели.**

---

# 48. Web AI Strategy

Web AI используется для:

- research;
- документации;
- comparison;
- debugging;
- architecture discussion;
- independent review;
- analysis of targeted Repomix snapshots.

Результат Web AI передаётся Cline только после Developer validation.

```text
Web AI
 ↓
Proposal
 ↓
Developer validation
 ↓
Cline
```

---

# 49. Local AI

При наличии подходящего hardware можно использовать:

- Ollama;
- LM Studio;
- local coding models;
- local inference tools.

Основное преимущество:

```text
No API cost
+
Local data
```

Выбор зависит от:

- quality;
- latency;
- hardware;
- privacy;
- cost;
- context size.

---

# 50. Conversation Management

## New task

Использовать для:

- новой feature;
- нового bug;
- нового architectural change.

## Continue context

Использовать, если предыдущий context действительно нужен.

## Compact

Использовать, когда история всё ещё релевантна, но становится слишком большой.

Принцип:

> **Старый контекст не должен сохраняться только потому, что он существует.**

---

# 51. Full Repository Review

Полный repository snapshot оправдан, когда вопрос системный.

Примеры:

```text
How should the monorepo evolve?
```

```text
Are package boundaries correct?
```

```text
Are there systemic dependency problems?
```

```text
What architectural risks exist before release?
```

Но даже при C5 review рекомендуется:

```text
repository snapshot
+
explicit review objective
+
review contract
+
architecture documentation
```

Просто передать полный snapshot без сформулированного вопроса — слабая практика.

---

# 52. Targeted Review Decision Matrix

| Change | Context | Repomix | Review |
|---|---|---:|---|
| One function | C0-C1 | Usually no | Local |
| One file | C1-C2 | Optional | Change |
| Subsystem | C2 | Optional | Change |
| Shared package | C3 | Recommended | Impact |
| API change | C3-C4 | Recommended | Impact |
| DB change | C3-C4 | Recommended | Impact + Security |
| Auth change | C3-C4 | Recommended | Security |
| New service | C4 | Recommended | Architecture |
| New database | C4-C5 | Recommended | Architecture |
| Major refactoring | C4-C5 | Recommended | Architecture |
| Major release | C5 | Recommended | Release |
| System architecture | C5 | Yes | Architecture |

---

# 53. Anti-Patterns

## Full Snapshot Everything

```text
every change
    ↓
full repository
    ↓
LLM
```

Проблемы:

- context waste;
- cost;
- lower signal-to-noise ratio;
- harder review;
- stale snapshots;
- unnecessary exposure.

---

## Tiny Context at Any Cost

```text
3 files only
```

если для понимания изменения требуется 10 файлов.

Проблема:

> token optimization destroys correctness.

---

## AI Architecture by Default

```text
AI says rewrite
    ↓
rewrite
```

Недопустимо.

---

## Review Without Diff

```text
current repository
    ↓
"find problems"
```

Слабее, чем:

```text
base
+
diff
+
current
+
requirements
```

---

## Review Without Scope

```text
Review everything.
```

Вместо:

```text
Objective
Scope
Out-of-scope
Evidence
Acceptance criteria
```

---

## AI Finding as Fact

```text
AI says vulnerability
    ↓
fix immediately
```

Правильно:

```text
AI finding
    ↓
repository validation
    ↓
confirmed?
    ├── yes → task/fix
    └── no  → reject
```

---

# 54. Professional Targeted Review Pipeline

Итоговый рекомендуемый pipeline:

```text
                     REQUIREMENT
                          │
                          ▼
                       RESEARCH
                          │
                          ▼
                      DEVELOPER
                        DECISION
                          │
                          ▼
                      ADR / TASK
                          │
                          ▼
                        CLINE
                   PLAN / IMPLEMENT
                          │
                          ▼
                   LOCAL QUALITY
                          │
                ┌─────────┴─────────┐
                ▼                   ▼
              TEST               GIT DIFF
                                    │
                                    ▼
                           CHANGE CLASSIFICATION
                                    │
                                    ▼
                              IMPACT ANALYSIS
                                    │
                                    ▼
                             CONTEXT LEVEL
                              C0 → C5
                                    │
                                    ▼
                              REVIEW TYPE
                              R1 → R5
                                    │
                                    ▼
                            REVIEW CONTRACT
                                    │
                                    ▼
                               REPOMIX
                                    │
                                    ▼
                            TARGETED SNAPSHOT
                                    │
                                    ▼
                            EXTERNAL AI REVIEW
                                    │
                                    ▼
                              FINDINGS
                                    │
                                    ▼
                              VALIDATION
                                    │
                          ┌─────────┴─────────┐
                          ▼                   ▼
                      CONFIRMED           REJECTED
                          │
                          ▼
                       TASK / ADR
                          │
                          ▼
                        CLINE
                          │
                          ▼
                       TESTS
                          │
                          ▼
                         GIT
                          │
                          ▼
                          CI
```

---

# 55. Golden Rules

## Architecture

1. **Developer decides — AI proposes.**
2. **Git is the Source of Truth.**
3. **Architecture decisions are documented in ADR.**
4. **AI must not silently change architectural boundaries.**
5. **Prefer evolutionary architecture over premature complexity.**

## Development

6. **Cline is the primary implementation agent.**
7. **Use the minimum sufficient context.**
8. **Large tasks must be divided into verifiable steps.**
9. **Tests are part of implementation.**
10. **Review Git diff before every commit.**

## Repomix

11. **Repomix is context packaging, not Source of Truth.**
12. **Do not use full Repomix for every task.**
13. **Anchor change reviews on Git diff.**
14. **Use targeted snapshots whenever possible.**
15. **Expand context only when evidence is insufficient.**
16. **Generate snapshots from a known Git state.**
17. **Never expose secrets or production data.**

## AI Review

18. **Review has an explicit objective and scope.**
19. **AI review is an opinion, not a fact.**
20. **Findings require evidence.**
21. **Validate important findings against the repository.**
22. **Separate confirmed issues from uncertainty and suggestions.**
23. **Multiple models provide independent opinions, not votes.**

## Security

24. **Secrets must never enter external AI context.**
25. **Destructive commands require approval.**
26. **Production operations require explicit control.**
27. **Security scanning does not replace manual verification.**

## Cost

28. **Use cheap models for simple work.**
29. **Use reasoning models for genuinely difficult problems.**
30. **Do not waste tokens on irrelevant context.**
31. **Never reduce context below the level required for correctness.**
32. **Stop repeated failed agent loops and investigate root cause.**

## Quality

33. **Local checks before push.**
34. **CI is the final technical quality gate.**
35. **Architecture is reviewed periodically.**
36. **Significant architectural changes leave a trace in Git.**

---

# 56. Final Principle

The objective is not:

> **"Let AI build the project."**

The objective is:

> **"Build an engineering system in which AI performs a large amount of development and analysis work while architecture, source of truth, security, quality gates and final decisions remain under developer control."**

The mature Repomix model is not:

```text
Repository
    ↓
Full Snapshot
    ↓
AI
```

It is:

```text
Repository
    ↓
Git Diff
    ↓
Change Classification
    ↓
Impact Analysis
    ↓
Context Level
    ↓
Review Contract
    ↓
Evidence Selection
    ↓
Targeted Repomix
    ↓
External AI
    ↓
Findings
    ↓
Repository Validation
    ↓
Developer Decision
```

---

# 57. One-Sentence Summary

> **Web AI думает и консультирует → Developer принимает решения → Cline исследует и реализует → Git фиксирует истину → CI проверяет → Git diff определяет изменение → Impact Analysis определяет blast radius → Repomix формирует минимально достаточный evidence-based context → External AI независимо критикует → Developer валидирует findings и решает, что улучшать дальше.**
