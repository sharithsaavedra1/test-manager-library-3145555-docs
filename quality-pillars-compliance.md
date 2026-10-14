# Cumplimiento de Pilares de Calidad — Manager Library

> **Ficha ADSO 3145555 · SDD Spec-Driven Development**
> Aplicación de Calidad de Software sobre la Especificación y Arquitectura

## 1. Pilares de Calidad Evaluados

### Pilar 1: Integridad y Confiabilidad de Datos (Motor de Base de Datos)
- **Cumplimiento:** Toda invariante crítica es asegurada en PostgreSQL 16 dentro del esquema `library`:
  - `ck_book_available_range`: `0 <= available_copies AND available_copies <= total_copies`
  - `ck_book_total_positive`: `total_copies >= 1`
  - `ck_user_role`: `role IN ('admin', 'librarian')`
  - Claves foráneas con políticas `RESTRICT` para evitar huérfanos históricos en préstamos.
  - Índices únicos parciales (`isbn` único solo para libros donde `deleted_at IS NULL`).

### Pilar 2: Desacoplamiento y Arquitectura Hexagonal
- **Cumplimiento:**
  - El Dominio no depende de Laravel ni de Eloquent. Contiene clases de entidad (`Book`, `Loan`, `LoanItem`, `Member`, `User`) y objetos de valor (`Isbn`, `Quantity`).
  - La capa de Aplicación define Puertos (Interfaces) para Repositorios, TokenIssuer, PasswordHasher y CoverStorage.
  - Los Adaptadores Inbound (Controladores HTTP) manejan la conversión de peticiones y respuestas según el contrato `api-contract.md`.
  - Los Adaptadores Outbound (Repositorios Eloquent/DB, Hasher Argon2/Bcrypt, Storage local) implementan dichos puertos.

### Pilar 3: Seguridad y Privacidad
- **Cumplimiento:**
  - Cero secretos o contraseñas en logs o respuestas HTTP.
  - Hash seguro de contraseñas.
  - Autenticación mediante tokens JWT con claims `sub` y `role`, con expiración a 8 horas (28800 segundos).
  - Los reportes no exponen identificadores ni datos personales de socios o bibliotecarios.

### Pilar 4: Inmutabilidad del Histórico de Préstamos
- **Cumplimiento:**
  - Las líneas de préstamo (`loan_item`) congelan `book_title`, `isbn` y `category_name` en el instante del préstamo.
  - El reporte de libros más prestados agrupa por los valores congelados, satisfaciendo que modificaciones posteriores al catálogo no alteren el histórico contable.
