# AI Engineering Strategy

## VS Code + Cline + OpenRouter + Web AI + Repomix + Local Intelligence + GitHub

> **Version:** 2.2  
> **Status:** Active  
> **Previous:** 2.1 — Targeted / Evidence-Based Review  
> **Purpose:** эволюция стратегии в сторону Local Engineering Intelligence при сохранении Developer control, Git as Source of Truth и minimum sufficient context.

---

# 0. Evolution Roadmap

```text
v2.0
AI-assisted development
        ↓
v2.1
Targeted / Evidence-Based Review
        ↓
v2.2                                          ← CURRENT
Local Engineering Intelligence
        ↓
v2.3
Automated Context & Impact Analysis
        ↓
v3.0
AI Engineering Control Plane
```

| Version | Focus | Key Addition |
|---------|-------|--------------|
| **v2.0** | AI-assisted development | Cline + Web AI + basic workflow |
| **v2.1** | Targeted / Evidence-Based Review | Context Levels, Review Contract, Change Snapshot, findings classification |
| **v2.2** | Local Engineering Intelligence | Local models, local code intelligence, offline capability, privacy-first layer |
| **v2.3** | Automated Context & Impact Analysis | Auto classification, impact graph, targeted snapshot generation |
| **v3.0** | AI Engineering Control Plane | Orchestration, policy engine, audit trail, multi-agent coordination |

**Invariant across all versions:**

> Developer decides — AI proposes.  
> Git is the Source of Truth.  
> Minimum sufficient context.  
> AI findings require repository validation.

---

# 1. Executive Summary

Версия 2.2 сохраняет всю архитектуру и принципы v2.1 и добавляет **Local Engineering Intelligence** — слой локального анализа и локальных моделей, который:

- снижает стоимость и latency для рутинных задач;
- повышает privacy (sensitive code не уходит наружу без необходимости);
- позволяет работать offline / air-gapped;
- готовит почву для автоматизации context & impact analysis в v2.3.

Целевая модель v2.2:

```text
                    WEB AI
              Research / Independent Review
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
         CI      Local Intelligence  History
          │            │
          ▼            ▼
       Quality     Symbol Index
        Gate       Call Graph
                   Local Models
                   Pre-Review
                       │
                       ▼
                 Targeted Context
                       │
                       ▼
              External AI (when needed)
                       │
                       ▼
                  DEVELOPER
                 Validate / Decide
```

Главное изменение относительно v2.1:

> Значительная часть рутинного анализа, C0–C2 задач и первичного review теперь может выполняться **локально**, без отправки контекста во внешние модели.

---

# 2. Core Philosophy (unchanged + extended)

## Unchanged principles from v2.1

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

## New principles introduced in v2.2

11. **Local-first for routine work.**  
    C0–C2 tasks, simple analysis and pre-review should prefer local intelligence when quality is sufficient.

12. **External AI only when justified.**  
    Use external models for research, complex reasoning, independent review and cases where local models are demonstrably insufficient.

13. **Privacy by default.**  
    Sensitive code, credentials and production data never leave the local environment without explicit Developer decision.

14. **Local intelligence is assistive, not authoritative.**  
    Local models and indexes are tools. Final decisions and Source of Truth remain with Developer and Git.

15. **Capability over hype.**  
    Local models are chosen by measured quality, latency, context window and hardware cost — not by marketing.

---

# 3. Tool Responsibility (updated)

| Responsibility              | Developer | Web AI | Cline | Local Intel | Repomix | Git | CI |
|-----------------------------|:---------:|:------:|:-----:|:-----------:|:-------:|:---:|:--:|
| Requirements                | ✅        | Assist |       |             |         |     |    |
| Architecture decisions      | ✅        | Assist |       | Assist      |         |     |    |
| Research                    |           | ✅     |       | Limited     |         |     |    |
| Repository exploration      |           |        | ✅    | ✅          |         |     |    |
| Implementation              | Approval  |        | ✅    | Assist      |         |     |    |
| File modification           | Approval  |        | ✅    |             |         |     |    |
| Terminal                    | Approval  |        | ✅    |             |         |     |    |
| Tests                       |           |        | ✅    |             |         |     | ✅ |
| Git history                 |           |        |       |             |         | ✅  |    |
| Change detection            |           |        |       | ✅          |         | ✅  |    |
| Symbol / call graph         |           |        |       | ✅          |         |     |    |
| Local pre-review            |           |        |       | ✅          |         |     |    |
| Context packaging           |           |        |       | Assist      | ✅      |     |    |
| Architecture review         | ✅        | ✅     |       | Assist      | ✅      |     |    |
| Security review             | ✅        | ✅     |       | Assist      | ✅      |     |    |
| Independent review          | ✅        | ✅     |       |             | ✅      |     |    |
| Lint / typecheck / build    |           |        | ✅    |             |         |     | ✅ |
| Deployment                  | Approval  |        |       |             |         |     | ✅ |

---

# 4. Local Engineering Intelligence Layer

Local Intelligence — это набор локальных компонентов, которые работают **рядом** с репозиторием и не требуют отправки кода наружу.

## 4.1 Components

```text
Local Engineering Intelligence
├── Code Intelligence
│   ├── Symbol Index
│   ├── Call Graph / Reference Graph
│   ├── Dependency Graph
│   └── Structure Overview
├── Local Models
│   ├── Coding models (completion, edit, explain)
│   ├── Reasoning models (when hardware allows)
│   └── Embedding models (for semantic search)
├── Local Analysis
│   ├── Diff analysis
│   ├── Impact estimation (basic)
│   ├── Pre-review checks
│   └── Convention / pattern detection
└── Local Runtime
    ├── Ollama / LM Studio / llama.cpp / etc.
    ├── LSP servers
    └── Tree-sitter / similar parsers
```

## 4.2 Responsibilities of Local Intelligence

| Task                        | Local preferred | External preferred |
|-----------------------------|-----------------|--------------------|
| Symbol lookup               | ✅              |                    |
| Find references / consumers | ✅              |                    |
| Simple explain / docstring  | ✅              |                    |
| Boilerplate / CRUD          | ✅              |                    |
| Unit test generation (simple) | ✅            |                    |
| Local diff understanding    | ✅              |                    |
| C0–C2 implementation        | ✅              |                    |
| Complex debugging           | Assist          | ✅                 |
| Architecture discussion     | Assist          | ✅                 |
| Independent security review |                 | ✅                 |
| Multi-component reasoning   | Assist          | ✅                 |
| Research / comparison       |                 | ✅                 |

## 4.3 Local-first Decision Rule

```text
Is the task C0–C2 and quality of local model sufficient?
        │
   ┌────┴────┐
  YES       NO
   │         │
   ▼         ▼
Local     External
Model     (or hybrid)
```

Hybrid pattern (recommended):

```text
Local Intelligence
    ↓
Pre-analysis + candidate findings
    ↓
Developer / Cline
    ↓
(only if needed) Targeted Snapshot → External AI
```

---

# 5. Context Strategy (inherited + local enhancement)

Все Context Levels C0–C5 из v2.1 остаются в силе.

Local Intelligence усиливает их следующим образом:

| Level | Local enhancement |
|-------|-------------------|
| **C0** | Instant symbol + definition via index |
| **C1** | File + imports resolved locally |
| **C2** | Subsystem + local tests + local deps via graph |
| **C3** | Consumers / reverse references from call graph |
| **C4** | Package boundary analysis via dependency graph |
| **C5** | Full structure overview (still prefer targeted when possible) |

**Правило остаётся прежним:**

> Начинать с минимального контекста.  
> Расширять только при недостатке evidence.  
> Correctness > token count.

Local layer делает минимальный контекст **дешёвым и быстрым** для получения.

---

# 6. Targeted Repomix in v2.2

Repomix остаётся **external analysis boundary**.

Изменения в v2.2:

- Local Intelligence может **подготавливать** candidate scope (changed files, consumers, tests).
- Developer / Cline утверждает scope.
- Только после утверждения формируется targeted snapshot.
- Для C0–C2 задач Repomix часто не требуется вовсе.

Рекомендуемый поток:

```text
Cline / Local change
    ↓
Local diff + symbol / consumer analysis
    ↓
Change Classification (manual in v2.2)
    ↓
Context Level decision
    ↓
(If external review needed)
    Review Contract
    ↓
Targeted Repomix
    ↓
External AI
```

---

# 7. Local Pre-Review

Перед отправкой изменений во внешний review (или даже перед commit) Local Intelligence может выполнить lightweight pre-review:

- Синтаксические и очевидные логические проблемы
- Нарушение локальных conventions
- Отсутствие тестов на изменённые пути
- Подозрительные паттерны (hardcoded secrets, TODO left in production paths и т.п.)
- Базовая оценка blast radius

Результаты pre-review:

- **не** являются Source of Truth;
- помогают Developer и Cline быстрее находить очевидные проблемы;
- могут уменьшать количество findings, которые потом всплывают во внешнем review.

---

# 8. Model Selection Strategy (updated)

## Fast / Cheap / Local

- Boilerplate
- CRUD
- Simple tests
- Small fixes
- Documentation
- C0–C2 tasks
- Local pre-review

## Reasoning / External

- Complex debugging
- Concurrency / distributed systems
- Database consistency
- Architecture
- Security review
- Multi-component impact
- Cases where local model quality is insufficient

**v2.2 principle:**

> Сначала проверяем, может ли задачу качественно решить локальная модель.  
> Только потом эскалируем во внешнюю.

---

# 9. Privacy & Security Boundary (strengthened)

Перед любым внешним export (включая targeted Repomix):

```text
Automatic exclusion
       +
Local security scan
       +
Manual verification by Developer
       ↓
External AI
```

Дополнительно в v2.2:

- Sensitive packages / modules могут быть помечены как **local-only**.
- Local Intelligence никогда не должна автоматически отправлять контекст наружу.
- Все внешние вызовы — явное действие Developer или явно разрешённый workflow.

Исключения (как в v2.1):

```text
.env*
*.pem / *.key
credentials/ secrets/
production data
customer data
node_modules/ dist/ build/ coverage/ logs/
```

---

# 10. Standard Workflows in v2.2

## 10.1 Simple change (C0–C2)

```text
Task
 ↓
Local Intelligence (explore + plan)
 ↓
Developer approval
 ↓
Cline / Local model implementation
 ↓
Local tests + lint + typecheck
 ↓
Local pre-review
 ↓
git diff review by Developer
 ↓
Commit
```

Внешний AI и Repomix обычно **не нужны**.

## 10.2 Significant change (C3+)

```text
Task
 ↓
Local Intelligence (structure + consumers)
 ↓
Developer decision + ADR/TASK if needed
 ↓
Cline implementation
 ↓
Local quality gates
 ↓
Change classification + impact (manual / assisted)
 ↓
Review Contract
 ↓
Targeted Repomix
 ↓
External AI review
 ↓
Validation against repository
 ↓
Developer decision
 ↓
Fixes → tests → commit
```

## 10.3 Architecture / Security / Release

Как в v2.1, с возможным локальным pre-analysis для подготовки scope.

---

# 11. Hardware & Runtime Considerations

Local Intelligence зависит от доступного железа.

Рекомендации:

| Hardware profile      | Recommended use                          |
|-----------------------|------------------------------------------|
| Modest (16–32 GB RAM) | Small coding models, embeddings, indexing |
| Strong (64 GB+)       | Larger coding + light reasoning models   |
| High-end GPU          | Stronger local reasoning, larger context |

Принцип:

> Лучше стабильная и предсказуемая локальная модель среднего качества, чем попытка запустить слишком большую модель с постоянными OOM / деградацией.

---

# 12. Relationship to Future Versions

## → v2.3 Automated Context & Impact Analysis

v2.2 создаёт фундамент:

- Local symbol / call / dependency graphs
- Local diff understanding
- Basic impact estimation

v2.3 превратит это в автоматический pipeline:

```text
git diff
  → auto Change Classification
  → auto Impact Graph
  → auto Context Level suggestion
  → auto Review Contract skeleton
  → auto Targeted Snapshot + Manifest
```

Человек будет утверждать, а не собирать вручную.

## → v3.0 AI Engineering Control Plane

v2.2 + v2.3 дают необходимые building blocks:

- Local intelligence
- Automated context packaging
- Clear review contracts
- Evidence-based findings

v3.0 добавит:

- Policy engine
- Multi-agent orchestration
- Full audit trail
- Cost / quality observability
- Controlled escalation paths

---

# 13. Anti-Patterns specific to v2.2

## Blind trust in local models

```text
Local model says "OK"
    ↓
Commit without review
```

Недопустимо. Local models — assistive.

## Sending everything external "just in case"

```text
Even simple C1 change
    ↓
Full external review
```

Противоречит local-first и minimum context.

## Running local models without quality baseline

```text
Any local model is better than none
```

Нет. Плохая локальная модель может вносить больше шума, чем пользы. Измеряйте.

## Treating local index as Source of Truth

```text
Index says X
    ↓
Ignore actual Git state
```

Index может быть stale. Git всегда главный.

---

# 14. Definition of Done (v2.2 additions)

К Definition of Done из v2.1 добавляются:

- [ ] Local quality gates passed (where applicable)
- [ ] Local pre-review performed for non-trivial changes
- [ ] Decision made whether external review is required
- [ ] If external review used — targeted snapshot + Review Contract present
- [ ] No sensitive data left local environment without explicit approval

---

# 15. Golden Rules (v2.2)

### Inherited (selected)

1. Developer decides — AI proposes.
2. Git is the Source of Truth.
3. Use the minimum sufficient context.
4. AI review is an opinion, not a fact.
5. Validate important findings against the repository.
6. Never expose secrets or production data.

### New in v2.2

7. Prefer local intelligence for C0–C2 and routine analysis.
8. Escalate to external AI only when local capability is insufficient or independent review is required.
9. Local models and indexes are assistive tools, not authorities.
10. Measure local model quality; do not assume "local = good enough".
11. Privacy by default — external sharing is an explicit decision.
12. Keep the path to v2.3 open: invest in local graphs and structured analysis.

---

# 16. Final Principle (v2.2)

The objective is still not:

> "Let AI build the project."

And not:

> "Run everything on the largest possible local model."

The objective is:

> **Build an engineering system in which routine intelligence is local, cheap and private; complex reasoning and independent review remain available externally; architecture, source of truth, security and final decisions stay under developer control.**

---

# 17. One-Sentence Summary (v2.2)

> **Local Intelligence handles routine exploration, C0–C2 work and pre-review → Developer decides → Cline implements → Git remains Source of Truth → targeted evidence-based context goes to external AI only when justified → findings are validated → decisions are recorded.**

---

# 18. Next Steps (toward v2.3)

Рекомендуемый порядок подготовки к следующей версии:

1. Стабилизировать local symbol / reference / dependency graph.
2. Научиться надёжно извлекать consumers из локального индекса.
3. Формализовать Change Classification (даже если пока вручную).
4. Начать сохранять structured impact notes рядом с значимыми изменениями.
5. Измерять, какие классы задач локальные модели закрывают качественно.

Когда эти элементы стабильны — можно переходить к **v2.3 Automated Context & Impact Analysis**.
