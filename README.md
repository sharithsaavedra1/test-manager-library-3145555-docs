# test-manager-library-3145555-docs

> **Prueba técnica · Ficha ADSO 3145555**
> Horario: **miércoles 14 de octubre de 2026, de 6:50 a. m. a 11:00 a. m.** (hora Colombia). Entrega a las **11:00 a. m.**

Este repositorio contiene el **spec** de *Manager Library*. Es el único con contenido: los otros cinco empiezan vacíos.

## Instrucciones

Cada aprendiz debe **crear el fork** de los seis repositorios del proyecto y **resolver el proyecto
con el spec planteado**.

1. Hacer fork, a su cuenta de GitHub, de cada repositorio de la tabla del final.
2. Leer el spec en [`test-manager-library-3145555-docs`](https://github.com/code-sena/test-manager-library-3145555-docs).
   Se entrega en dos versiones: `spec-python/` y `spec-.net/`.
3. Desarrollar en los forks.

## El reto se desarrolla con React y PHP (Laravel)

El spec está escrito para Python y para .NET, pero el reto **no** se hace en esos lenguajes:

| Capa | Tecnología del reto |
|---|---|
| Frontend | React |
| Backend | PHP con Laravel |

Lo que el spec define sobre el negocio —historias, criterios de aceptación, reglas, contrato de la
API, modelo de datos— se respeta. Lo que define sobre la tecnología se traduce a React y Laravel.

## La prueba no consiste en escribir el código

El propósito principal es ver la **capacidad de desempeño con SDD** (*Spec-Driven Development*,
desarrollo guiado por especificación): cómo se lee, se interpreta y se aplica una especificación
para llevarla a un stack distinto. El código es el medio, no el fin.

## Entrega

Cuenta lo que quede **subido a los forks antes de las 11:00 a. m.** Lo que llegue después no se revisa.
No hay prórroga: suban avances parciales con commits pequeños durante la prueba.

## Qué contiene este repositorio

| Carpeta / Documento | Contenido |
|---|---|
| [`technical-debt.md`](technical-debt.md) | **Registro Formal de Deuda Técnica e Inconsistencias Resueltas (Regla de Oro)** |
| [`quality-pillars-compliance.md`](quality-pillars-compliance.md) | **Cumplimiento de Pilares de Calidad y Solución de Inconsistencias** |
| [`spec-laravel/`](spec-laravel/) | **Especificación Oficial adaptada a PHP (Laravel) y React (Stack del Reto)** |
| [`spec-python/`](spec-python/) | El spec de Manager Library escrito para un backend Python (referencia) |
| [`spec-.net/`](spec-.net/) | El mismo sistema especificado para un backend .NET (referencia) |

## Cómo se lee el spec

En `spec-laravel/` (o en cualquiera de las carpetas de referencia), en este orden:

1. `constitution.md` — principios innegociables.
2. `spec.md` — qué debe hacer el sistema: actores, historias, criterios de aceptación, reglas de negocio.
3. `technical-debt.md` y `quality-pillars-compliance.md` — inconsistencias resueltas y pilares de calidad.
4. `plan.md`, `architecture.md` y `arquitectura-panoramica.md` — cómo se construye.
5. `data-model.md` y `api-contract.md` — el modelo de datos y el contrato de la API.
6. `tasks.md` — qué hay que hacer y con qué evidencia se da por hecho.
7. `adr/` — las decisiones de arquitectura, con sus alternativas y consecuencias.

## Los seis componentes del sistema

| Componente | Qué va ahí |
|---|---|
| [`docs/`](.) | El spec completo, inconsistencias y deuda técnica |
| [`api/`](../api) | Backend en PHP (Laravel) |
| [`app/`](../app) | Frontend en React |
| [`page/`](../page) | Sitio público estático de presentación |
| [`infra/`](../infra) | Contenedores, red, volúmenes y motor de base de datos vacío |
| [`tool/`](../tool) | Utilidades: sembrador de datos de demostración |

