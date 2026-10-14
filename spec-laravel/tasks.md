# Tareas de Implementación — Manager Library (Laravel & React)

| # | Tarea | Depende de | Criterio de Aceptación / Evidencia |
|---|---|---|---|
| T-01 | Configuración Base e Infraestructura (`infra` y `api`) | — | `GET /health` responde `{"status":"ok"}`; Docker Compose levanta PostgreSQL 16 vacío |
| T-02 | Migraciones DDL Completas y Semilla de Categorías | T-01 | Se crean tablas (`category`, `book`, `member`, `loan`, `loan_item`, `user`), FKs restrictivas, checks y se siembran las 5 categorías fijas |
| T-03 | Dominio: Modelos y Value Objects (`Isbn`, `Quantity`, etc.) | T-01 | Pruebas unitarias de invariantes de negocio de entidades |
| T-04 | Dominio y Repositorio: Entidad `Book` (Retiro, reposición, baja lógica) | T-03 | Disponibles nunca menores a 0 ni mayores a total copias |
| T-05 | Dominio y Repositorio: `Loan` y `LoanItem` | T-04 | Regla de 14 días, estados calculados (`open`, `overdue`, `closed`) y límite de 3 libros |
| T-06 | Dominio y Repositorio: `Member` y `User` | T-03 | Normalización de usuarios, unicidad de documento, rol restringido |
| T-07 | Servicios de Aplicación y Autenticación JWT | T-06 | Emisión de token Bearer de 8 horas con claims `sub` y `role` |
| T-08 | Servicios de Aplicación de Catálogo y Libros | T-04 | Búsqueda parcial, filtros por categoría, paginación y manejo de portadas |
| T-09 | Caso de Uso Prestar Libros (`LendBooksService`) | T-05, T-08 | Operación atómica; validación de préstamos vencidos y límite de 3 |
| T-10 | Caso de Uso Devolución y Consulta de Vencidos | T-09 | Devolución por línea; reposición de disponibles; detección de vencidos |
| T-11 | Caso de Uso Reporte de Libros Más Prestados | T-09 | Consulta agregada sobre líneas congeladas en rango de fechas |
| T-12 | Controladores y Manejo Unificado de Errores HTTP | T-07..T-11 | Respuestas estructuradas con `code`, `message`, `details` según `api-contract.md` |
| T-13 | Sembrado de Administrador Inicial en Arranque | T-12 | Admin inicial provisionado desde variables de entorno |
| T-14 | Implementación de `tool/` (Sembrador de Demostración) | T-13 | Script que siembra ≥ 40 libros, 10 socios, 15 préstamos vía API HTTP |
| T-15 | Implementación de `page/` (Sitio Web Público Estático) | — | Página estática responsiva sin dependencias de API |
| T-16 | Implementación de `app/` (Frontend React) | T-12 | SPA funcional: Autenticación, Catálogo, Socios, Préstamos, Vencidos y Reportes |
