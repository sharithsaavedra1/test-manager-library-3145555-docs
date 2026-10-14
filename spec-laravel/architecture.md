# Arquitectura Hexagonal — Manager Library (PHP / Laravel)

## 1. Capas y Principio de Inversión de Dependencias

```mermaid
flowchart LR
    subgraph Entrada
      HTTP["Adapters/Inbound/Http<br/>(Laravel Controllers & Middlewares)"]
    end
    subgraph Núcleo
      APP["Application<br/>Services & Outbound Ports"]
      DOM["Domain<br/>Entities & Value Objects"]
    end
    subgraph Salida
      DB["Adapters/Outbound/Persistence<br/>(PostgreSQL 16 & Repositories)"]
      STO["Adapters/Outbound/Storage<br/>(CoverStorage)"]
      SEC["Adapters/Outbound/Security<br/>(PasswordHasher & JwtTokenIssuer)"]
    end
    HTTP --> APP --> DOM
    DB -. implementa .-> APP
    STO -. implementa .-> APP
    SEC -. implementa .-> APP
```

## 2. Estructura de Directorios en `api/`

```text
api/
├── app/
│   ├── Domain/
│   │   ├── Model/ (Book, Loan, LoanItem, Member, User, Category)
│   │   ├── ValueObjects/ (Isbn, Quantity, DateRange)
│   │   └── Exceptions/ (Domain Exceptions)
│   ├── Application/
│   │   ├── Ports/
│   │   │   ├── BookRepositoryInterface.php
│   │   │   ├── LoanRepositoryInterface.php
│   │   │   ├── MemberRepositoryInterface.php
│   │   │   ├── UserRepositoryInterface.php
│   │   │   ├── ReportReaderInterface.php
│   │   │   ├── TokenIssuerInterface.php
│   │   │   ├── PasswordHasherInterface.php
│   │   │   └── CoverStorageInterface.php
│   │   └── Services/
│   │       ├── LoginService.php
│   │       ├── CreateLibrarianService.php
│   │       ├── BookService.php
│   │       ├── MemberService.php
│   │       ├── LendBooksService.php
│   │       ├── ReturnItemService.php
│   │       ├── OverdueLoansService.php
│   │       └── MostBorrowedReportService.php
│   └── Adapters/
│       ├── Inbound/Http/
│       │   ├── Controllers/
│       │   ├── Middleware/ (AuthMiddleware, RoleMiddleware)
│       │   └── Requests/
│       └── Outbound/
│           ├── Persistence/ (Eloquent/PDO Repositories)
│           ├── Security/ (JwtTokenIssuer, ArgonPasswordHasher)
│           └── Storage/ (LocalCoverStorage)
```
