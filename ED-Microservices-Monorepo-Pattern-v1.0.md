# Evolutionary Design Microservices Monorepo Pattern

## PNPM Workspaces + Turborepo

### Версия 1.0

---

## 1. Что такое ED Microservices Monorepo

**ED Microservices Monorepo** — практический паттерн проектирования и организации микросервисной системы, в котором:

- исходный код сервисов находится в едином monorepo;
- `pnpm workspaces` управляет workspace и dependency boundaries;
- `Turborepo` управляет task graph и incremental execution;
- каждый микросервис является самостоятельной runtime/deployment единицей;
- каждый сервис может использовать внутреннюю модель Evolutionary Design;
- межсервисное взаимодействие происходит через явные API/events/contracts;
- общий код выделяется только после доказанной необходимости повторного использования;
- архитектура эволюционирует вместе с системой, сохраняя устойчивые границы ответственности.

Главная идея:

> **Monorepo не является монолитом.**

Единый repository определяет организацию исходного кода.

Микросервис определяет runtime и deployment boundary.

ED определяет внутреннюю архитектуру сервиса.

---

## 2. Главная архитектурная модель

Паттерн состоит из нескольких уровней:

```text
                         SYSTEM
                           │
                           ▼
                      MONOREPO
                           │
              ┌────────────┴────────────┐
              │                         │
             apps                    packages
              │                         │
              ▼                         ▼
        MICROservices              shared capabilities
              │
              ▼
          ED SERVICE
              │
      ┌───────┼────────┐
      ▼       ▼        ▼
     app   features  services
                       │
                       ▼
                     shared
```

Таким образом:

```text
Monorepo
    ↓
Microservices
    ↓
ED architecture
    ↓
Application capabilities
```

---

## 3. Главная идея: граница ответственности

Структура проекта не должна строиться вокруг технического типа файла.

Не:

```text
components/
hooks/
utils/
services/
api/
types/
```

а:

```text
apps/
packages/
tooling/
infrastructure/
```

и уже **внутри конкретного сервиса**:

```text
app/
features/
services/
shared/
```

Ключевой принцип:

> **Каталог должен отражать архитектурную ответственность, а не тип исходного файла.**

---

## 4. Два уровня архитектуры

В ED Microservices Monorepo существуют **две независимые архитектурные границы**.

### Уровень 1 — система

```text
apps/
packages/
tooling/
infrastructure/
```

Он определяет:

- какие deployable units существуют;
- какие shared packages существуют;
- какие tooling packages используются;
- как организована инфраструктура.

### Уровень 2 — микросервис

```text
app/
features/
services/
shared/
```

Он определяет внутреннюю структуру конкретного сервиса.

Получается:

```text
                    MONOREPO
                       │
        ┌──────────────┼──────────────┐
        │              │              │
      auth           users          orders
        │              │              │
        ▼              ▼              ▼
       ED             ED             ED
        │              │              │
     app/...         app/...        app/...
```

---

## 5. Каноническая структура monorepo

Базовый reference tree:

```text
repository/
│
├── apps/
│   ├── gateway/
│   ├── auth/
│   ├── users/
│   ├── orders/
│   └── notifications/
│
├── packages/
│   ├── contracts/
│   ├── config/
│   ├── logger/
│   ├── errors/
│   └── observability/
│
├── tooling/
│   ├── eslint/
│   ├── prettier/
│   └── typescript/
│
├── infrastructure/
│   ├── docker/
│   ├── compose/
│   └── k8s/
│
├── .github/
│   └── workflows/
│
├── package.json
├── pnpm-workspace.yaml
├── pnpm-lock.yaml
└── turbo.json
```

Необязательно создавать всю структуру заранее.

Структура должна **эволюционировать вместе с реальными потребностями**.

---

## 6. `apps`

`apps` содержит самостоятельные application/deployment units.

Например:

```text
apps/
├── gateway/
├── auth/
├── users/
└── orders/
```

Каждый каталог представляет отдельный сервис:

```text
apps/auth
apps/users
apps/orders
```

Каждый сервис должен иметь собственный:

```text
package.json
tsconfig.json
Dockerfile
runtime configuration
entrypoint
tests
```

Сервис может независимо:

- запускаться;
- тестироваться;
- собираться;
- контейнеризироваться;
- деплоиться;
- масштабироваться.

---

## 7. Microservice ≠ package

`packages/logger`:

```text
reusable library
```

`apps/orders`:

```text
deployable application
```

Поэтому:

```text
packages/logger
```

не является микросервисом.

А:

```text
apps/orders
```

является потенциально самостоятельной runtime-единицей.

---

## 8. Внутренняя структура микросервиса

Каждый сервис может использовать ED:

```text
apps/orders/
│
├── src/
│   ├── app/
│   ├── features/
│   ├── services/
│   └── shared/
│
├── tests/
│
├── Dockerfile
├── package.json
├── tsconfig.json
└── .env.example
```

Например:

```text
apps/orders/
└── src/
    ├── app/
    │
    ├── features/
    │   ├── order-creation/
    │   ├── order-cancellation/
    │   └── order-history/
    │
    ├── services/
    │   ├── payment/
    │   └── notification/
    │
    └── shared/
        ├── lib/
        ├── config/
        └── domain/
```

Таким образом исходная модель:

```text
app
features
services
shared
```

становится **внутренним стандартом каждого сервиса**.

---

## 9. `app` внутри микросервиса

`app` является runtime/composition layer.

Он отвечает за:

- bootstrap;
- dependency composition;
- runtime adapters;
- transport entrypoints;
- middleware;
- lifecycle;
- configuration wiring.

Принцип:

> **`app` подключает функциональность, но не владеет реализацией бизнес-функциональности.**

Например:

```text
app/
├── main.ts
├── router.ts
├── providers.ts
└── bootstrap.ts
```

---

## 10. `features`

`feature` — законченная capability или business operation.

Например:

```text
features/
├── user-registration/
├── password-reset/
├── order-creation/
├── order-cancellation/
└── payment-processing/
```

Feature определяется как самостоятельный кусок функциональности с законченным пользовательским или бизнес-смыслом.

---

## 11. Структура feature

Минимальная feature:

```text
features/order-creation/
└── index.ts
```

По мере роста:

```text
features/order-creation/
├── index.ts
├── _api.ts
├── ui/
├── model/
├── lib/
└── __tests__/
```

Не следует создавать все каталоги заранее.

Правило:

> **Local first → Reuse second → Extract only when justified.**

---

## 12. `services` внутри микросервиса

Необходимо различать два понятия:

```text
Microservice
    ↓
apps/orders
```

и:

```text
Internal service
    ↓
apps/orders/src/services/payment
```

Internal service — самостоятельный механизм **внутри конкретного микросервиса**.

Например:

```text
services/
├── payment/
├── notification/
└── storage/
```

Он может использоваться несколькими features одного сервиса.

---

## 13. Feature → service

Допустимое направление:

```text
feature
   │
   ▼
service
   │
   ▼
shared
```

Например:

```text
order-creation
      │
      ▼
payment-service
      │
      ▼
shared
```

Service не должен зависеть от feature.

---

## 14. `shared`

`shared` остаётся фундаментом **конкретного сервиса**.

Например:

```text
shared/
├── lib/
├── config/
├── api/
└── domain/
```

Он не должен знать о:

```text
features/
services/
```

То есть:

```text
shared → feature
shared → service
```

запрещено.

---

## 15. Dependency Direction внутри сервиса

Каноническая модель:

```text
                app
                 │
                 ▼
             features
                 │
                 ▼
              services
                 │
                 ▼
               shared
```

Матрица:

| From | Allowed |
|---|---|
| `app` | `features`, `services`, `shared` |
| `features` | `services`, `shared` |
| `services` | `shared` |
| `shared` | external libraries / own shared layer |

Запрещается:

```text
shared → features
shared → services
services → features
```

И по умолчанию:

```text
feature A → feature B
```

не допускается.

---

## 16. Dependency Direction между микросервисами

Нельзя:

```text
orders
  ↓
users source code
```

То есть:

```ts
import { UserService } from '@services/users';
```

не является способом интеграции микросервисов.

Правильно:

```text
orders
   │
   ├── HTTP
   ├── gRPC
   └── Message/Event
   │
   ▼
users
```

Микросервисы не должны использовать исходный код друг друга как внутреннюю библиотеку.

---

## 17. Runtime boundary

```text
┌──────────────┐
│ orders       │
│              │
│ own code     │
│ own runtime  │
│ own config   │
└──────┬───────┘
       │
       │ network
       ▼
┌──────────────┐
│ users        │
│              │
│ own code     │
│ own runtime  │
│ own config   │
└──────────────┘
```

Это позволяет каждому сервису иметь независимый lifecycle.

---

## 18. `packages`

`packages` содержит код, который **доказанно является общим**.

Например:

```text
packages/
├── contracts/
├── logger/
├── errors/
├── config/
└── observability/
```

`packages` не является глобальной свалкой shared-кода.

Правило:

```text
service-local
      │
      │ proven reuse
      ▼
workspace package
```

---

## 19. Когда код переносится из сервиса в package

Начинаем:

```text
apps/orders/src/shared/lib/foo.ts
```

Позже обнаруживаем:

```text
orders → foo
users  → foo
auth   → foo
```

и `foo` действительно не содержит service-specific semantics.

Тогда:

```text
apps/orders/src/shared/lib/foo.ts
```

может эволюционировать в:

```text
packages/foo/
```

Это результат доказанной потребности, а не обязательный рефакторинг.

---

## 20. `contracts`

Особое место занимают contracts:

```text
packages/contracts/
├── auth/
├── users/
├── orders/
└── events/
```

Contracts могут описывать:

```text
HTTP API
DTO
events
message schemas
validation schemas
protocol definitions
```

Но contracts не должны содержать implementation.

Плохо:

```text
packages/contracts/
└── UserService.ts
```

Хорошо:

```text
packages/contracts/
└── users/
    └── user-created.ts
```

Например:

```ts
export interface UserCreatedEvent {
  userId: string;
  email: string;
  createdAt: string;
}
```

---

## 21. Contract ≠ shared business logic

Различаем:

```text
Contract
```

и:

```text
Business implementation
```

Contract говорит:

```text
что сервис предоставляет
```

Implementation говорит:

```text
как сервис это делает
```

Поэтому:

```text
packages/contracts
```

может быть общим.

Но:

```text
apps/users/src/features/user-creation
```

остаётся владельцем реализации.

---

## 22. Публичные API packages

Workspace package должен иметь явную public boundary.

Например:

```text
packages/contracts/
├── index.ts
├── auth/
├── users/
└── orders/
```

Внешний код:

```ts
import { UserCreatedEvent } from '@repo/contracts';
```

а не:

```ts
import { UserCreatedEvent } from '@repo/contracts/users/events/user-created';
```

Публичная граница позволяет менять внутреннюю структуру package без изменения всех consumers.

---

## 23. PNPM Workspaces

Корневой:

```yaml
packages:
  - "apps/*"
  - "packages/*"
  - "tooling/*"
```

Каждый workspace является самостоятельным package.

Например:

```json
{
  "name": "@repo/orders",
  "private": true,
  "version": "1.0.0"
}
```

Другой workspace может использовать package:

```json
{
  "dependencies": {
    "@repo/contracts": "workspace:*"
  }
}
```

Получаем dependency graph:

```text
@repo/orders
      │
      ▼
@repo/contracts
```

---

## 24. Что делает PNPM

PNPM отвечает за:

```text
workspace
dependencies
lockfile
package resolution
package linking
```

PNPM не отвечает за:

```text
runtime communication
deployment
service discovery
container orchestration
```

Это разные уровни системы.

---

## 25. Что делает Turborepo

Turborepo отвечает за execution graph:

```text
build
test
lint
typecheck
```

Например:

```text
contracts:build
       │
       ▼
orders:build
       │
       ▼
gateway:build
```

Turborepo позволяет:

- выполнять задачи с учётом dependency graph;
- не выполнять ненужные задачи;
- использовать cache;
- выполнять задачи параллельно;
- фильтровать workspace packages.

---

## 26. Канонический `turbo.json`

Базовая модель:

```json
{
  "tasks": {
    "build": {
      "dependsOn": ["^build"],
      "outputs": ["dist/**"]
    },
    "test": {
      "dependsOn": ["^build"],
      "outputs": []
    },
    "lint": {
      "outputs": []
    },
    "typecheck": {
      "outputs": []
    },
    "dev": {
      "cache": false,
      "persistent": true
    }
  }
}
```

Важно:

```text
PNPM
  dependency graph

Turbo
  task graph
```

Они связаны, но не являются одним и тем же механизмом.

---

## 27. Build Graph

Например:

```text
packages/contracts
        │
        ▼
apps/orders
        │
        ▼
apps/gateway
```

Turborepo может превратить это в:

```text
contracts:build
        │
        ▼
orders:build
        │
        ▼
gateway:build
```

Независимые ветки могут выполняться параллельно:

```text
              contracts
              /       \
             ▼         ▼
          orders      users
             │         │
             └────┬────┘
                  ▼
               gateway
```

---

## 28. Incremental development

Если изменён только:

```text
apps/orders
```

не обязательно пересобирать весь repository.

Например:

```bash
pnpm turbo build --filter=@repo/orders
```

Архитектурная модель:

```text
repository
     │
     ├── unchanged
     │      X
     │
     └── changed
            │
            ▼
         orders
            │
            ▼
          build
```

---

## 29. Docker boundary

Каждый microservice получает собственный image:

```text
apps/
├── auth/Dockerfile
├── users/Dockerfile
├── orders/Dockerfile
└── gateway/Dockerfile
```

Например:

```text
GHCR
│
├── auth:1.0.0
├── users:1.0.0
├── orders:1.0.0
└── gateway:1.0.0
```

Docker реализует:

> **runtime isolation and delivery boundary.**

---

## 30. Deployment independence

Изменение:

```text
apps/orders
```

не должно автоматически означать необходимость нового runtime процесса:

```text
auth
users
```

Если dependency graph и release policy это позволяют:

```text
orders
   │
   ▼
orders image
   │
   ▼
deploy orders
```

остальные сервисы остаются неизменными.

---

## 31. Database ownership

Микросервис должен владеть своими данными.

Каноническая модель:

```text
auth
 │
 ▼
Auth DB

users
 │
 ▼
Users DB

orders
 │
 ▼
Orders DB
```

Сервис не должен напрямую читать или изменять таблицы другого сервиса.

Нежелательно:

```text
orders
  └──────► users_db.users
```

Правильная модель:

```text
orders
   │
   ├── API
   │
   └── events
        │
        ▼
      users
```

---

## 32. Data ownership как архитектурная граница

```text
Service owns:
    code
    business rules
    API
    data
```

Другой сервис получает необходимую информацию через контракт.

Это уменьшает скрытые coupling points.

---

## 33. Межсервисное взаимодействие

Возможны различные transport mechanisms:

```text
Service A
    │
    ├── HTTP
    ├── gRPC
    └── Message Broker
            │
            ▼
        Service B
```

Конкретный транспорт не является главным элементом паттерна.

Главное:

> **Граница взаимодействия должна быть явной.**

---

## 34. Synchronous communication

Например:

```text
orders
   │
   │ GET /users/:id
   ▼
users
```

Преимущество:

- простой request/response;
- непосредственный результат операции.

Недостаток:

- runtime coupling;
- зависимость доступности;
- latency propagation.

---

## 35. Asynchronous communication

Например:

```text
orders
   │
   │ OrderCreated
   ▼
Message Broker
   │
   ├──────► notifications
   │
   └──────► analytics
```

Это уменьшает непосредственную runtime связанность между consumers.

---

## 36. Events

Event должен описывать факт:

```text
UserCreated
OrderCreated
PaymentCompleted
```

а не внутренний метод класса:

```text
executeUserInternalProcess
```

Событие представляет **контракт**, а не implementation detail.

---

## 37. Архитектурная модель dependencies

В результате существуют три разных graph:

```text
1. SOURCE GRAPH

pnpm
 │
 ▼
package dependencies


2. TASK GRAPH

Turborepo
 │
 ▼
build/test/lint


3. RUNTIME GRAPH

network/events
 │
 ▼
microservice communication
```

Их нельзя смешивать.

---

## 38. Полная модель

```text
                         CLIENTS
                            │
                            ▼
                     ┌─────────────┐
                     │   Gateway   │
                     └──────┬──────┘
                            │
             ┌──────────────┼──────────────┐
             ▼              ▼              ▼
           Auth           Users          Orders
             │              │              │
             ▼              ▼              ▼
          Auth DB        Users DB       Orders DB


══════════════════════ MONOREPO ══════════════════════

                         PNPM
                          │
                  dependency graph
                          │
                       TURBO
                          │
                     task graph
                          │
       ┌──────────────────┼──────────────────┐
       ▼                  ▼                  ▼
    auth app           users app          orders app
       │                  │                  │
       ▼                  ▼                  ▼
      ED                 ED                 ED
       │                  │                  │
 app/features/...   app/features/...   app/features/...


packages/
├── contracts
├── logger
├── config
├── errors
└── observability


══════════════════════ DELIVERY ══════════════════════

                     Docker
                       │
                       ▼
                      GHCR
                       │
                       ▼
                    Deploy
```

---

## 39. Четыре главных уровня ответственности

```text
MONOREPO
→ Где находится код?

MICROSERVICE
→ Кто владеет runtime capability?

ED
→ Как организована внутренняя ответственность?

CONTRACT
→ Как сервис взаимодействует с внешним миром?
```

---

## 40. Каноническая dependency matrix

### На уровне monorepo

```text
apps
  ↓
packages
```

### Внутри service

```text
app
 ↓
features
 ↓
services
 ↓
shared
```

### Между services

```text
service A
    │
    │ API / Event
    ▼
service B
```

Не:

```text
service A
    ↓
service B source code
```

---

## 41. Что запрещено

### 41.1 Shared → application

```text
packages/logger
      X
      ↓
apps/orders
```

### 41.2 Service → feature

```text
internal service
      X
      ↓
feature
```

### 41.3 Feature → feature

По умолчанию:

```text
feature A
    X
    ↓
feature B
```

Если такая зависимость становится постоянной, нужно пересмотреть границы.

### 41.4 Microservice → microservice source

```text
orders
   X
   ↓
users/src/*
```

### 41.5 Service → чужая database

```text
orders
   X
   ↓
users DB
```

### 41.6 Global dumping ground

Не создавать без конкретной архитектурной ответственности:

```text
packages/common/
packages/utils/
packages/shared/
packages/misc/
```

---

## 42. Антипаттерн: Monolith Monorepo

Плохая структура:

```text
apps/
└── backend/
    ├── users/
    ├── orders/
    ├── payments/
    └── notifications/
```

если всё работает как единый runtime и модули напрямую импортируют друг друга.

Это может быть **modular monolith**, но не микросервисная архитектура.

Monorepo сам по себе не делает систему микросервисной.

---

## 43. Антипаттерн: Shared Everything

Плохая модель:

```text
packages/shared/
├── database/
├── users/
├── orders/
├── auth/
├── payment/
└── random/
```

В результате:

```text
auth
  ↕
shared
  ↕
users
  ↕
orders
```

возникает скрытая связанность.

Правило:

> **Сначала локализуй ответственность. Потом докажи переиспользуемость.**

---

## 44. Антипаттерн: Common Types

Плохой вариант:

```text
packages/types/
├── User.ts
├── Order.ts
├── Payment.ts
├── Session.ts
└── Everything.ts
```

Тип должен принадлежать своему владельцу.

Возможны:

```text
feature-specific
service-specific
contract-specific
system-wide
```

но не все типы автоматически становятся global types.

---

## 45. Антипаттерн: premature extraction

Плохой процесс:

```text
new code
   ↓
"может пригодиться"
   ↓
packages/common
```

Правильный:

```text
new code
   ↓
local service
   ↓
real reuse appears
   ↓
evaluate extraction
   ↓
package
```

---

## 46. Антипаттерн: техническая классификация

Не следует строить monorepo вокруг:

```text
components/
hooks/
utils/
services/
types/
```

вместо:

```text
apps/
packages/
tooling/
infrastructure/
```

И внутри приложения:

```text
features/
services/
shared/
```

Технические категории допустимы **внутри архитектурной границы**.

---

## 47. Как определить новый microservice

Не следует создавать новый сервис только потому, что появился новый каталог.

Нужно определить:

1. Есть ли самостоятельная responsibility?
2. Есть ли собственный business capability?
3. Нужен ли отдельный lifecycle?
4. Нужна ли независимая масштабируемость?
5. Есть ли собственный data ownership?
6. Нужен ли отдельный deployment boundary?
7. Имеет ли сервис чёткий контракт?

Если ответов недостаточно, возможно, это пока feature или module внутри существующего сервиса.

---

## 48. Microservice должен иметь ownership

Каноническая модель:

```text
Auth Service
    owns
    ├── authentication
    ├── authorization
    ├── auth API
    └── auth data
```

```text
Order Service
    owns
    ├── order lifecycle
    ├── order API
    └── order data
```

Ownership важнее физического расположения файлов.

---

## 49. Evolutionary Service Extraction

Начало:

```text
apps/backend/
└── features/orders/
```

Появляется самостоятельная capability.

Затем:

```text
apps/orders/
```

Появляется отдельный runtime.

После этого:

```text
orders
   │
   ▼
orders DB
```

и:

```text
orders API
```

Таким образом:

```text
module
   ↓
feature
   ↓
service
   ↓
microservice
```

Это применение **Evolutionary Design на уровне распределённой системы**.

---

## 50. Не нужно начинать с 20 микросервисов

Можно начать:

```text
apps/
└── backend/
```

или:

```text
apps/
├── api/
└── web/
```

а затем выделять самостоятельные runtime boundaries по мере появления реальных требований.

Принцип:

> **Architecture evolves with the system.**

---

## 51. Как строить проект с нуля

### Шаг 1. Создать monorepo

```text
pnpm
+
workspace
+
turbo
```

### Шаг 2. Создать минимальный набор apps

Например:

```text
apps/
├── gateway/
└── api/
```

Не создавать заранее множество сервисов без реальной необходимости.

### Шаг 3. Определить capabilities

Например:

```text
authentication
users
orders
payments
notifications
```

### Шаг 4. Определить ownership

```text
auth → authentication
users → users
orders → orders
```

### Шаг 5. Определить data ownership

```text
auth → auth DB
users → users DB
orders → orders DB
```

### Шаг 6. Определить contracts

```text
packages/contracts
```

только для реальных межсервисных границ.

### Шаг 7. Внутри каждого сервиса применить ED

```text
app/
features/
services/
shared/
```

### Шаг 8. Настроить dependency rules

Проверить:

```text
apps → packages
```

и:

```text
app → features → services → shared
```

### Шаг 9. Настроить Turbo

```text
build
test
lint
typecheck
dev
```

### Шаг 10. Только затем Docker/CI/CD

```text
build
 ↓
image
 ↓
registry
 ↓
deploy
```

---

## 52. Migration existing system

Для существующего проекта миграция должна быть постепенной.

```text
existing application
        │
        ▼
identify capabilities
        │
        ▼
identify ownership
        │
        ▼
introduce workspace boundaries
        │
        ▼
extract packages only where justified
        │
        ▼
extract microservices only where justified
```

---

## 53. Architecture Smells

### Большой `shared`

```text
packages/shared/
```

становится самым большим package.

Это сигнал пересмотреть ownership.

### Большой service

```text
apps/backend/
```

содержит сотни несвязанных capabilities.

Возможно, необходимо выделить boundaries.

### Слишком много microservices

```text
apps/
├── get-user/
├── create-user/
├── update-user/
├── delete-user/
...
```

Это может означать, что deployment boundary был ошибочно принят за business capability.

### Cross-service imports

```text
orders → users/src
```

сигнал нарушения runtime boundary.

### Shared business logic

```text
packages/shared/business/
```

часто является признаком неправильного ownership.

---

## 54. Проверяемость архитектуры

Паттерн должен быть **проверяемым автоматически**.

Можно проверять:

```text
shared не импортирует features
shared не импортирует services

service не импортирует feature

feature не импортирует internal другой feature

package не импортирует apps

microservice не импортирует source другого microservice

cycles отсутствуют
```

Это может контролироваться через:

- ESLint;
- TypeScript;
- dependency-cruiser;
- custom scripts;
- CI;
- Turborepo;
- package-level restrictions.

---

## 55. Architecture Decision Tree

При появлении нового кода:

```text
                         NEW CODE
                            │
                            ▼
                  Это отдельный runtime?
                       /          \
                     да            нет
                     │              │
                     ▼              ▼
                   apps       Это часть feature?
                                  /       \
                                да         нет
                                │           │
                                ▼           ▼
                            feature     Independent
                                       mechanism?
                                         /    \
                                       да      нет
                                       │        │
                                       ▼        ▼
                                    service   shared
```

При появлении нового package:

```text
                 NEW SHARED CODE
                        │
                        ▼
              Используется >1 owner?
                   /          \
                 нет           да
                 │              │
                 ▼              ▼
               local       Is it truly
                            generic?
                            /     \
                          нет       да
                          │          │
                          ▼          ▼
                        local     package
```

---

## 56. Как принимать решение о выделении microservice

```text
                    Capability
                        │
                        ▼
                Самостоятельный
                   ownership?
                   /          \
                 нет           да
                 │              │
                 ▼              ▼
              feature       Нужен отдельный
                            runtime boundary?
                              /        \
                            нет         да
                            │            │
                            ▼            ▼
                       существующий   microservice
                         service
```

---

## 57. Каноническая формула

> **Local first → Reuse second → Extract only when justified → Deploy independently when ownership becomes autonomous.**

По-русски:

> **Сначала локализуй ответственность. Затем докажи переиспользуемость. Только после этого выноси код в общий package. Выделяй отдельный микросервис только тогда, когда capability получает самостоятельный ownership и runtime boundary.**

---

## 58. Четыре архитектурных принципа

### 1. Ownership

Каждая responsibility имеет владельца.

### 2. Boundary

Каждый владелец имеет явную границу.

### 3. Direction

Зависимости движутся в предсказуемом направлении.

### 4. Evolution

Границы меняются вместе с системой, а не проектируются раз и навсегда.

---

## 59. Full Reference Tree

```text
repository/
│
├── apps/
│   │
│   ├── gateway/
│   │   ├── src/
│   │   │   ├── app/
│   │   │   ├── features/
│   │   │   ├── services/
│   │   │   └── shared/
│   │   ├── package.json
│   │   ├── tsconfig.json
│   │   └── Dockerfile
│   │
│   ├── auth/
│   │   ├── src/
│   │   │   ├── app/
│   │   │   ├── features/
│   │   │   ├── services/
│   │   │   └── shared/
│   │   ├── package.json
│   │   └── Dockerfile
│   │
│   ├── users/
│   │   ├── src/
│   │   │   ├── app/
│   │   │   ├── features/
│   │   │   ├── services/
│   │   │   └── shared/
│   │   ├── package.json
│   │   └── Dockerfile
│   │
│   └── orders/
│       ├── src/
│       │   ├── app/
│       │   ├── features/
│       │   ├── services/
│       │   └── shared/
│       ├── package.json
│       └── Dockerfile
│
├── packages/
│   ├── contracts/
│   ├── logger/
│   ├── config/
│   ├── errors/
│   └── observability/
│
├── tooling/
│   ├── eslint/
│   ├── prettier/
│   └── typescript/
│
├── infrastructure/
│   ├── docker/
│   ├── compose/
│   └── k8s/
│
├── .github/
│   └── workflows/
│
├── package.json
├── pnpm-workspace.yaml
├── pnpm-lock.yaml
└── turbo.json
```

---

## 60. One-page Rule Set

```text
1. Monorepo is not a monolith.

2. apps contain deployable applications.

3. Each microservice owns its runtime boundary.

4. Each microservice may use ED internally.

5. app = runtime/composition.

6. features = capabilities.

7. services = reusable independent mechanisms
   inside a service.

8. shared = local foundation.

9. packages = proven cross-service reuse.

10. contracts = explicit integration boundary.

11. Microservices never import each other's source code.

12. Services communicate through explicit contracts.

13. A service owns its data.

14. Do not access another service's database directly.

15. app → features → services → shared.

16. apps → packages.

17. packages must not depend on apps.

18. shared must not depend on application layers.

19. service must not depend on feature.

20. feature-to-feature dependencies are exceptional.

21. Keep code local until reuse is proven.

22. Do not create global dumping grounds.

23. Do not create empty architectural folders.

24. Public module APIs should be explicit.

25. PNPM manages dependencies.

26. Turborepo manages task execution.

27. Docker manages runtime isolation.

28. CI/CD manages delivery.

29. Build graph is not runtime graph.

30. Architecture evolves with the system.
```

---

## 61. Каноническая модель ED Microservices

```text
                         SYSTEM
                           │
                           ▼
                     ┌───────────┐
                     │ MONOREPO  │
                     └─────┬─────┘
                           │
                 PNPM dependency graph
                           │
                           ▼
             ┌─────────────────────────┐
             │          APPS           │
             └────────────┬────────────┘
                          │
          ┌───────────────┼───────────────┐
          ▼               ▼               ▼
        AUTH            USERS           ORDERS
          │               │               │
          ▼               ▼               ▼
        ┌────┐          ┌────┐          ┌────┐
        │ ED │          │ ED │          │ ED │
        └─┬──┘          └─┬──┘          └─┬──┘
          │               │               │
          ▼               ▼               ▼
        app             app             app
          │               │               │
          ▼               ▼               ▼
      features        features        features
          │               │               │
          ▼               ▼               ▼
      services        services        services
          │               │               │
          ▼               ▼               ▼
       shared          shared          shared

              ┌──────────────────────┐
              │      PACKAGES        │
              │ contracts / logger   │
              │ config / errors      │
              └──────────────────────┘

                           │
                           ▼
                    TURBO TASK GRAPH
                           │
                           ▼
                       DOCKER
                           │
                           ▼
                        GHCR
                           │
                           ▼
                       DEPLOYMENT
```

---

## 62. Финальный стандарт

Проект можно считать соответствующим **ED Microservices Monorepo Pattern**, если одновременно выполняются следующие условия:

- monorepo не используется как оправдание монолитной архитектуры;
- каждый microservice имеет чёткий ownership;
- deployment/runtime boundary отделён от source repository boundary;
- внутри сервиса сохраняется направленная архитектура;
- `app` отвечает за composition/runtime;
- `features` отвечают за capabilities;
- `services` предоставляют самостоятельные механизмы;
- `shared` является фундаментом;
- cross-service reuse выделяется в packages только при доказанной необходимости;
- contracts отделены от implementations;
- сервисы не импортируют исходный код друг друга;
- сервисы не используют напрямую чужие databases;
- dependency graph остаётся направленным;
- task graph отделён от runtime graph;
- Docker является deployment boundary, а не архитектурным слоем;
- новый код сначала остаётся локальным;
- extraction выполняется только после появления реальной потребности;
- архитектура может эволюционировать без разрушения существующих boundaries.

---

## 63. Формула паттерна

```text
                    EVOLUTIONARY DESIGN
                           │
                           ▼
                    OWNERSHIP FIRST
                           │
                           ▼
                     CLEAR BOUNDARY
                           │
                           ▼
                   DIRECTED DEPENDENCY
                           │
                           ▼
                    PROVEN REUSE
                           │
                           ▼
                    EXPLICIT CONTRACT
                           │
                           ▼
                 INDEPENDENT RUNTIME
```
