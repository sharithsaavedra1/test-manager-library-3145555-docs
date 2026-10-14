# Plan Técnico — Manager Library (PHP / Laravel + React)

> **Adaptación SDD para el Stack del Reto: PHP (Laravel) y Frontend React**
> Rige bajo la Constitución, `spec.md`, `data-model.md` y `api-contract.md`.

## 1. Decisiones Técnicas Adaptadas

| # | Decisión | Elección (Laravel + React) | Justificación Arquitectural |
|---|---|---|---|
| D-01 | Lenguaje y runtime Backend | PHP 8.1+ / Laravel 10/11 | Stack del reto de la evaluación |
| D-02 | Framework HTTP | Laravel Routing & Controllers | Cumplimiento del contrato en `api-contract.md` (`/api/v1`) |
| D-03 | Persistencia | Migraciones Laravel sobre PostgreSQL 16 (esquema `library`) | El esquema es propiedad de las migraciones (ADR-001) |
| D-04 | Concurrencia | Restricción del motor `ck_book_available_range` + decremento atómico | Garantiza RNF-2 (último ejemplar) sin añadir columnas ajenas al alcance |
| D-05 | Fechas | UTC ISO 8601 con sufijo `Z` (`Carbon::now('UTC')`), reloj abstraído | Soporte para pruebas de vencimiento a 14 días |
| D-06 | Reporte | Consulta SQL directa agregada sobre `loan_item` congelado | Cumplimiento estricto de ADR-004 |
| D-07 | Objetos de Valor | Clases inmutables PHP en `Domain\ValueObjects` (`Isbn`, `Quantity`) | Protección de invariantes sin persistencia redundante |
| D-08 | Portadas | Almacenamiento local en disco bajo `storage/app/covers/` | Generación de clave opaca (UUID) |
| D-09 | Contraseñas | Argon2id / Bcrypt mediante `PasswordHasherInterface` | Artículo IX (nunca en texto claro) |
| D-10 | Arranque | Seeder/Comando `library:seed-admin` desde `.env` (`ADMIN_USERNAME`, `ADMIN_PASSWORD`) | La BD nunca arranca con hashes fijos |
| D-11 | Autenticación | Emisor de JWT / Token Bearer con vigencia de 8 horas (28800 s) | `access_token`, `token_type`, `expires_in`, `role` |
| D-12 | Frontend | React 18+ (SPA con Vite) + Vanilla CSS | Módulos para Catálogo, Socios, Préstamos, Devoluciones, Vencidos y Reportes |

## 2. Configuración de Entorno (.env)

```env
APP_NAME="Manager Library"
APP_ENV=local
APP_KEY=base64:...
APP_DEBUG=true
APP_URL=http://localhost:8000

DB_CONNECTION=pgsql
DB_HOST=localhost
DB_PORT=5432
DB_DATABASE=library
DB_USERNAME=library
DB_PASSWORD=library
DB_SCHEMA=library

JWT_SECRET=super_secret_jwt_key_library_3145555_production_safe
ADMIN_USERNAME=admin
ADMIN_PASSWORD=AdminPassword123!
CORS_ORIGIN=http://localhost:5173
```
