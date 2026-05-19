# Proyecto Integrador — Accesorios D&M E-Commerce

**Institución:** CORHUILA  
**Asignatura:** Análisis y Diseño de Sistemas  
**Equipo:**
| Nombre |
|--------|
| Sergio Andrés Losada Bahamón |
| Julio César Lozano |
| Andrés Felipe Gómez|

**Fecha:** 2026-05-18

---

## Descripción del Proyecto

MVP de sistema e-commerce para **Accesorios D&M**, emprendimiento de joyería artesanal que actualmente opera por Instagram y WhatsApp de forma manual. El sistema digitaliza y centraliza el catálogo, el inventario, el carrito de compras, los pedidos y la autenticación, mediante una arquitectura de microservicios.

---

## Entregables del Proyecto Integrador

| #   | Documento                                | Descripción                                                              | Archivo                                            |
| --- | ---------------------------------------- | ------------------------------------------------------------------------ | -------------------------------------------------- |
| 1   | **Documentación del Negocio y Contexto** | Problema, stakeholders, contexto del dominio, justificación              | [01-business-context.md](./01-business-context.md) |
| 2   | **Análisis de Requerimientos (SRS)**     | 28 RF + 15 RNF con criterios de aceptación y matriz de trazabilidad      | [02-srs-requirements.md](./02-srs-requirements.md) |
| 3   | **Diagramas UML**                        | 6 casos de uso, diagrama de clases, 3 secuencias, 3 diagramas de estados | [03-uml-diagrams.md](./03-uml-diagrams.md)         |
| 4   | **Diagramas BPMN**                       | AS-IS y TO-BE de 3 procesos: Venta, Inventario, Autenticación            | [04-bpmn-diagrams.md](./04-bpmn-diagrams.md)       |
| 5   | **Historias de Usuario**                 | 5 HUs con DOR, DOD y criterios de aceptación                             | [05-user-stories.md](./05-user-stories.md)         |
| 6   | **User Story Map y Roadmap**             | 5 épicas, 9 features, backlog priorizado, roadmap 4 sprints              | [06-user-story-map.md](./06-user-story-map.md)     |
| 7   | **Mockups y Prototipo**                  | 9 pantallas wireframe + guía de implementación visual                    | [07-mockups.md](./07-mockups.md)                   |

---

## Resumen de Cobertura por Criterio del Proyecto

### Requerimientos Funcionales

| Módulo                    | Cantidad RF                         |
| ------------------------- | ----------------------------------- |
| Seguridad y Autenticación | RF-01 a RF-06 (6 RF)                |
| Catálogo e Inventario     | RF-07 a RF-13 (7 RF)                |
| Carrito de Compras        | RF-14 a RF-18 (5 RF)                |
| Pagos y Órdenes           | RF-19 a RF-23 (5 RF)                |
| Frontend Web              | RF-24 a RF-28 (5 RF)                |
| **Total**                 | **28 RF** ✅ (mínimo requerido: 10) |

### Requerimientos No Funcionales

| Categoría                 | Cantidad RNF                         |
| ------------------------- | ------------------------------------ |
| Rendimiento               | 2                                    |
| Seguridad                 | 3                                    |
| Escalabilidad             | 1                                    |
| Mantenibilidad            | 1                                    |
| Usabilidad                | 2                                    |
| Portabilidad              | 1                                    |
| Trazabilidad/Integridad   | 3                                    |
| Recuperabilidad/Auditoría | 2                                    |
| **Total**                 | **15 RNF** ✅ (mínimo requerido: 10) |

### Diagramas UML

| Tipo                      | Cantidad         |
| ------------------------- | ---------------- |
| Diagramas de Casos de Uso | 6 ✅ (mínimo: 5) |
| Diagrama de Clases        | 1 ✅             |
| Diagramas de Secuencia    | 3 ✅ (mínimo: 2) |
| Diagramas de Estados      | 3 ✅             |

### BPMN

| Proceso                | AS-IS | TO-BE |
| ---------------------- | ----- | ----- |
| Proceso de Venta       | ✅    | ✅    |
| Gestión de Inventario  | ✅    | ✅    |
| Autenticación y Acceso | ✅    | ✅    |

### Historias de Usuario

| HU                                  | Tiene DOR | Tiene DOD | Tiene CA |
| ----------------------------------- | --------- | --------- | -------- |
| HU-001: Registro e Inicio de Sesión | ✅        | ✅        | 6 CA ✅  |
| HU-002: Consulta del Catálogo       | ✅        | ✅        | 6 CA ✅  |
| HU-003: Gestión del Carrito         | ✅        | ✅        | 6 CA ✅  |
| HU-004: Admin de Productos          | ✅        | ✅        | 6 CA ✅  |
| HU-005: Confirmación y Seguimiento  | ✅        | ✅        | 6 CA ✅  |

---

## Stack Tecnológico

| Capa              | Tecnología                              |
| ----------------- | --------------------------------------- |
| Frontend Web      | Angular 17 · TypeScript                 |
| API Gateway       | NestJS · Node.js 20 LTS · TypeScript    |
| Inventory Service | Spring Boot 3 · Java 21                 |
| Security Service  | Spring Boot 3 · Java 21                 |
| Payment Service   | Spring Boot 3 · Java 21                 |
| Base de Datos     | PostgreSQL 16 · Liquibase (migraciones) |
| Autenticación     | JWT RS256 · bcrypt                      |
| Contenedores      | Docker · Docker Compose                 |
| Mobile (futuro)   | Flutter / React Native                  |
