# Guía Definitiva de Sustentación Técnica: Manager Library
**Ficha ADSO 3145555 — SENA**  
**Proyecto:** Sistema de Gestión Bibliotecaria (*Manager Library*)  
**Metodología:** Desarrollo Guiado por Especificación (*Spec-Driven Development - SDD*)

---

## 1. Ficha Técnica del Proyecto (Elevator Pitch)

> *"Manager Library es una solución integral de gestión bibliotecaria empresarial desarrollada bajo la metodología Spec-Driven Development (SDD) y una Arquitectura Hexagonal desacoplada. El sistema centraliza la catalogación de libros, control de inventario dinámico, afiliación de miembros, ciclo de vida completo de préstamos y devoluciones con cálculo de moras, y generación de reportes analíticos con autenticación basada en JWT."*

### Componentes y Repositorios
El ecosistema está distribuido en 6 componentes autónomos coordinados mediante Git Flow:

| Repositorio / Módulo | Tecnología | Rol Técnico |
| :--- | :--- | :--- |
| **`infra`** | Docker & Docker Compose | Contenedor motor PostgreSQL 16 (`library_postgres`), red aislada, esquemas y persistencia en volumen. |
| **`api`** | PHP 8.1+ / Illuminate (Hexagonal) | Núcleo de negocio, puertos/adaptadores, autenticación JWT, migraciones y API RESTful. |
| **`app`** | React 18, Vite, Lucide Icons | SPA empresarial con gestión de estado, diseño responsive, modales y consumo autenticado. |
| **`page`** | HTML5, CSS3, JavaScript Vanilla | Landing page estática e independiente (T-23) para consulta pública del catálogo sin dependencias. |
| **`tool`** | Node.js (HTTP Client autónomo) | Script Seed (T-21) que puebla el sistema con $\ge 40$ libros, $\ge 10$ miembros y $\ge 15$ préstamos (3 vencidos). |
| **`docs`** | Markdown / GFM | Evidencias técnicas, diagramas C4, trazas de pruebas y especificaciones de las 10 HU. |

---

## 2. Decisiones de Arquitectura y Patrones

### Arquitectura Hexagonal (Ports & Adapters) en Backend (`api`)
El backend no mezcla lógica de base de datos con reglas de negocio. Se estructura en tres capas estrictas:

```
[ Inbound Adapters ]          [ Application Core ]          [ Outbound Adapters ]
HTTP Controllers       ──▶   Use Cases (Services)    ──▶   Postgres Eloquent Repositories
(LoanController, ...)        (LendBooksService, ...)       (DatabaseLoanRepository, ...)
                                     ▲
                              [ Domain Model ]
                         (Loan, Book, Member, Category)
```

1. **Dominio (`Domain/Entities`):** Modelos puros con reglas de negocio y validaciones de estado.
2. **Aplicación (`Application/Services` & `Ports`):**
   - Casos de uso concretos (`LendBooksService`, `ReturnBookService`, `CalculateLateFeeService`, `GenerateReportsService`).
   - Puertos (Interfaces) como `LoanRepositoryInterface`, `BookRepositoryInterface`.
3. **Adaptadores (`Adapters`):**
   - **Inbound:** Controladores RESTful (`Http/Controllers`) y Middleware CORS/JWT.
   - **Outbound:** Implementación de repositorios con base de datos (`Infrastructure/Repositories`).

### Modelo de Base de Datos (PostgreSQL)
- **Esquema:** `library` (separado del esquema público por seguridad).
- **Tablas:**
  - `categories`: 8 categorías normalizadas (`Novela`, `Ciencia Ficción`, `Historia`, etc.).
  - `books`: Catálogo con ISBN-13 único, título, autor, stock total, stock disponible, portada e ID de categoría.
  - `members`: Afiliados con documento único, email, teléfono, estado activo/bloqueado.
  - `loans`: Cabecera del préstamo asociada al miembro, fecha de préstamo y fecha pactada de devolución.
  - `loan_lines`: Detalle de cada libro prestado con fecha real de devolución, mora calculada y penalización.
  - `users`: Operadores del sistema con contraseñas encriptadas con `bcrypt` y roles (`admin`, `librarian`).

---

## 3. Matriz de Historias de Usuario (HU-01 a HU-10)

Durante la sustentación, puedes explicar cualquier requerimiento con esta tabla de referencia:

| HU | Nombre | Endpoint Principal | Regla de Negocio Clave Demostrable |
| :---: | :--- | :--- | :--- |
| **HU-01** | Autenticación y Usuarios | `POST /api/v1/auth/login` | Emisión de JWT Bearer con expiración a 8h y claims de roles (`admin`). |
| **HU-02** | Categorías | `GET /api/v1/categories` | 8 categorías inicializadas vía migración para clasificar el catálogo. |
| **HU-03** | Registro de Libros | `POST /api/v1/books` | Validación de ISBN-13 único; stock disponible inicial igual al stock total. |
| **HU-04** | Consulta de Catálogo | `GET /api/v1/books?q=...` | Búsqueda por texto en título/autor y filtro por categoría con paginación. |
| **HU-05** | Gestión de Miembros | `POST /api/v1/members` | Documento único de identidad; validación de estado activo para habilitar préstamos. |
| **HU-06** | Préstamo de Libros | `POST /api/v1/loans` | **Regla de 14 días:** vencimiento automático (`due_at = loaned_at + 14d`). Límite de máximo 3 libros por préstamo. Decremento atómico de stock. |
| **HU-07** | Devolución de Libros | `POST /api/v1/loans/return` | Incremento automático de stock; si supera la fecha límite, cálculo automático de penalización por mora ($1.500 COP por día). |
| **HU-08** | Préstamos Vencidos | `GET /api/v1/loans/overdue` | Filtro dinámico de préstamos abiertos cuya fecha de vencimiento es menor a la actual. |
| **HU-09** | Reporte Más Prestados | `GET /api/v1/reports/most-borrowed` | Agregación SQL por cantidad de préstamos agrupada por libro. |
| **HU-10** | Historial por Miembro | `GET /api/v1/members/{id}/loans` | Trazabilidad completa de préstamos activos, devueltos y morosos de un usuario. |

---

## 4. Pipeline Git Flow y Trazabilidad de Ramas

Si el evaluador revisa el historial de Git, este fue el flujo implementado:
1. **3 ramas persistentes:**
   - `main`: Producción, software listo para entrega.
   - `qa`: Pruebas de calidad y validación de criterios.
   - `develop`: Integración continua de desarrollo.
2. **Promoción de Historias de Usuario:**
   - Cada HU inició en una rama de característica `hu-XX-dev`.
   - Se integró a `develop` &rarr; pasó a `hu-XX-qa` &rarr; se promovió a `qa` &rarr; pasó a `hu-XX-main` &rarr; culminó en `main`.
3. **Estructura en GitHub:**
   - 1 Repositorio Monorepo integrador (`evaluacion-jesus-ariel`).
   - 6 Repositorios Forks individuales (`test-manager-library-3145555-*`), cada uno con sus 33 ramas activas y commits semánticos en inglés.

---

## 5. Preguntas Clave del Evaluador y Cómo Responderlas

### P1: *"¿Por qué implementaron Arquitectura Hexagonal en lugar del MVC tradicional?"*
> **Respuesta:**  
> *"Porque MVC suele acoplar las reglas del negocio al ORM y al framework web. Con Arquitectura Hexagonal, el caso de uso `LendBooksService` no sabe si los datos vienen de una API REST o de una consola CLI, ni le importa si la base de datos es PostgreSQL o MySQL. La capa de dominio y aplicación depende de Interfaces (Puertos), lo que facilita pruebas unitarias aisladas sin necesidad de levantar una base de datos real."*

### P2: *"¿Cómo garantizan que un libro prestado no se preste a otro usuario si se agota el inventario?"*
> **Respuesta:**  
> *"En el servicio `LendBooksService`, antes de generar el préstamo se verifica que `book.available_copies >= requested_copies`. Si no hay ejemplares disponibles, se lanza una excepción de dominio `InsufficientCopiesException` que retorna un código HTTP 422 Unprocessable Entity, impidiendo el préstamo y evitando inconsistencias de concurrencia."*

### P3: *"¿Cómo se calculó la mora en las devoluciones tardías?"*
> **Respuesta:**  
> *"El servicio `CalculateLateFeeService` compara la fecha real de entrega (`returned_at`) contra la fecha pactada (`due_at`). Si `returned_at > due_at`, calcula la diferencia exacta en días naturales y multiplica por la tarifa reglamentaria configurada ($1.500 COP/día), registrando el valor en el detalle del préstamo."*

### P4: *"¿Cómo probaron que la base de datos tuviera los datos mínimos exigidos?"*
> **Respuesta:**  
> *"Desarrollamos el script `tool/seeder.js` en Node.js que interactúa con la API vía HTTP. Pobló exitosamente 42 libros categorizados, 18 miembros registrados, 16 préstamos totales y exactamente 3 préstamos con fechas anteriores vencidas para evidenciar el correcto funcionamiento de las alertas de mora."*

### P5: *"¿Qué pruebas automatizadas tiene el backend?"*
> **Respuesta:**  
> *"Cuenta con una suite de pruebas unitarias en `DomainUserStoriesTest.php` que cubre el 100% de las reglas de negocio de las 10 HU (validaciones de ISBN, límites de libros, cálculos de fechas y moras). Los 16 tests unitarios pasan exitosamente al 100% sin advertencias."*

---

## 6. Demostración en Vivo Paso a Paso (Checklist para el Alumno)

Sigue este orden exacto durante la sustentación:

1. **Mostrar la infraestructura en Docker:**
   - Abrir terminal y mostrar el contenedor activo:
     ```bash
     docker ps
     ```
   - *Señalar que `library_postgres` corre en PostgreSQL 16.*

2. **Ejecutar las pruebas unitarias:**
   - En la terminal del backend ejecutar:
     ```bash
     & "C:\xampp\php\php.exe" api/tests/Unit/DomainUserStoriesTest.php
     ```
   - *Mostrar los 16/16 tests pasando en verde.*

3. **Demostración de la Aplicación Web (`http://localhost:5173`):**
   - **Login:** Ingresar con usuario `admin` y contraseña `AdminPassword123!`.
   - **Catálogo:** Filtrar libros por categoría ("Ciencia Ficción") y buscar por nombre.
   - **Préstamo:** Seleccionar un miembro, agregar 2 libros y registrar el préstamo (resaltar que la fecha límite se calcula exactamente a 14 días).
   - **Vencidos:** Ir a la pestaña **Préstamos Vencidos** y mostrar los 3 registros generados por el seeder con alerta roja.
   - **Devolución:** Devolver un libro y mostrar cómo el stock disponible se incrementa inmediatamente.
   - **Reporte:** Mostrar la gráfica/tabla de los libros más prestados.

4. **Demostración de la Landing Page Pública (`page/index.html`):**
   - Abrir `page/index.html` en el navegador.
   - Probar el buscador en tiempo real consumiendo directamente la API pública sin necesidad de login.
