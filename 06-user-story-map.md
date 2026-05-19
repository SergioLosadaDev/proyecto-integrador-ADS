# Entregable 6 — User Story Map y Roadmap del MVP

**Proyecto:** Accesorios D&M — Sistema E-Commerce  
**Versión:** 1.0  
**Fecha:** 2026-05-18

---

## 1. User Story Map

El User Story Map organiza las historias de usuario en torno al **viaje del usuario** (eje horizontal) y las **prioridades de entrega** (eje vertical, de mayor a menor valor para el MVP).

### Eje Horizontal — Actividades del Usuario (Journey)

```
ACTIVIDAD    │  AUTENTICARSE  │  EXPLORAR CATÁLOGO  │  COMPRAR  │  GESTIÓN ADMIN  │  POSTVENTA
─────────────┼────────────────┼─────────────────────┼───────────┼─────────────────┼────────────
```

### Eje Vertical — Nivel de entrega (Release)

```
══════════════════════════════════════════════════════════════════════════════════════════════════
ACTIVIDADES  │ AUTENTICARSE       │ EXPLORAR CATÁLOGO    │ COMPRAR           │ GESTIÓN ADMIN      │ POSTVENTA
══════════════════════════════════════════════════════════════════════════════════════════════════
ÉPICAS        EP-01: Seguridad    EP-02: Catálogo        EP-03: Carrito      EP-04: Admin         EP-05: Historial
══════════════════════════════════════════════════════════════════════════════════════════════════

RELEASE 1 ── ─────────────────── ─────────────────────  ─────────────────── ─────────────────── ───────────
(MVP Core)   [HU-001]            [HU-002]               [HU-003]            [HU-004]            [HU-005]
              Registro e          Consulta catálogo       Gestión del         Admin de            Confirmación
              Inicio de sesión    con filtros             carrito             productos           y seguimiento
                                                                                                  de orden

RELEASE 2 ── ─────────────────── ─────────────────────  ─────────────────── ─────────────────── ───────────
(Mejoras     [HU-006]            [HU-007]               [HU-008]            [HU-009]            [HU-010]
 UX)          Recuperar           Comparar               Persistencia        Panel de            Notificaciones
              contraseña          productos              carrito             inventario          por email

RELEASE 3 ── ─────────────────── ─────────────────────  ─────────────────── ─────────────────── ───────────
(Crecimiento [HU-011]            [HU-012]               [HU-013]            [HU-014]            [HU-015]
 del negocio) OAuth / Google     Wishlist               Pago en línea       Reportes de         Programa de
              login               favoritos              integrado           ventas              fidelización
══════════════════════════════════════════════════════════════════════════════════════════════════
```

---

## 2. Épicas del Proyecto

### EP-01 — Seguridad y Autenticación

| Campo                | Valor                                                                                                                                    |
| -------------------- | ---------------------------------------------------------------------------------------------------------------------------------------- |
| **ID**               | EP-01                                                                                                                                    |
| **Descripción**      | Implementar el sistema de autenticación, autorización y gestión de roles que protege todos los recursos del e-commerce.                  |
| **Valor de Negocio** | Permite que solo usuarios autorizados accedan a los recursos correspondientes; protege los datos de clientes y la operación del negocio. |
| **KPI de Éxito**     | 0 accesos no autorizados a endpoints protegidos; tiempo de validación de token < 50 ms.                                                  |

**Features asociadas:**

| ID Feature | Nombre                      | HUs                                 | Sprint   |
| ---------- | --------------------------- | ----------------------------------- | -------- |
| FT-01      | Gestión de Identidad        | HU-001: Registro e Inicio de Sesión | Sprint 1 |
| FT-02      | Control de Acceso por Roles | HU-004 (parcial: guard ADMIN)       | Sprint 2 |

---

### EP-02 — Catálogo e Inventario

| Campo                | Valor                                                                                                             |
| -------------------- | ----------------------------------------------------------------------------------------------------------------- |
| **ID**               | EP-02                                                                                                             |
| **Descripción**      | Gestionar el catálogo de productos (CRUD), categorías, materiales, imágenes y el control de stock en tiempo real. |
| **Valor de Negocio** | Es la base del e-commerce: sin catálogo no hay ventas. Reemplaza las publicaciones manuales de Instagram.         |
| **KPI de Éxito**     | 100% de productos con stock actualizado en tiempo real; 0 ventas de productos sin stock.                          |

**Features asociadas:**

| ID Feature | Nombre                     | HUs                        | Sprint   |
| ---------- | -------------------------- | -------------------------- | -------- |
| FT-03      | Consulta de Productos      | HU-002: Consulta Catálogo  | Sprint 1 |
| FT-04      | Gestión Admin de Productos | HU-004: Admin de Productos | Sprint 2 |
| FT-05      | Control de Inventario      | (incluida en HU-004)       | Sprint 2 |

---

### EP-03 — Carrito y Proceso de Compra

| Campo                | Valor                                                                                                                                                |
| -------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------- |
| **ID**               | EP-03                                                                                                                                                |
| **Descripción**      | Implementar el flujo completo de compra: agregar productos al carrito, calcular totales, confirmar la orden y gestionar el ciclo de vida del pedido. |
| **Valor de Negocio** | Nucleo transaccional del e-commerce; reemplaza la gestión manual de pedidos por WhatsApp.                                                            |
| **KPI de Éxito**     | Tasa de conversión carrito → orden > 60%; tiempo promedio de checkout < 5 minutos.                                                                   |

**Features asociadas:**

| ID Feature | Nombre                | HUs                                | Sprint   |
| ---------- | --------------------- | ---------------------------------- | -------- |
| FT-06      | Carrito de Compras    | HU-003: Gestión Carrito            | Sprint 2 |
| FT-07      | Órdenes y Seguimiento | HU-005: Confirmación y Seguimiento | Sprint 3 |

---

### EP-04 — Panel de Administración

| Campo                | Valor                                                                                                                                                |
| -------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------- |
| **ID**               | EP-04                                                                                                                                                |
| **Descripción**      | Proveer a la propietaria (Admin) una interfaz web para gestionar productos, órdenes, inventario y parámetros del sistema sin conocimientos técnicos. |
| **Valor de Negocio** | Empodera a la propietaria para operar el negocio de forma autónoma; reduce dependencia del equipo de desarrollo.                                     |
| **KPI de Éxito**     | Admin puede actualizar catálogo y gestionar órdenes sin asistencia técnica.                                                                          |

**Features asociadas:**

| ID Feature | Nombre                     | HUs                    | Sprint   |
| ---------- | -------------------------- | ---------------------- | -------- |
| FT-04      | Gestión Admin de Productos | HU-004                 | Sprint 2 |
| FT-08      | Gestión Admin de Órdenes   | HU-005 (parcial Admin) | Sprint 3 |

---

### EP-05 — Historial y Postventa

| Campo                | Valor                                                                                                                       |
| -------------------- | --------------------------------------------------------------------------------------------------------------------------- |
| **ID**               | EP-05                                                                                                                       |
| **Descripción**      | Ofrecer al cliente visibilidad sobre sus pedidos pasados y el estado del pedido en curso; gestión del historial de estados. |
| **Valor de Negocio** | Genera confianza del cliente y reduce consultas de soporte a la propietaria.                                                |
| **KPI de Éxito**     | 90% de clientes consultan el estado de su pedido en el portal (no por WhatsApp).                                            |

**Features asociadas:**

| ID Feature | Nombre                | HUs                | Sprint   |
| ---------- | --------------------- | ------------------ | -------- |
| FT-07      | Órdenes y Seguimiento | HU-005             | Sprint 3 |
| FT-09      | Notificaciones        | HU-010 (Release 2) | Sprint 4 |

---

## 3. Roadmap del MVP — 4 Sprints

```
SPRINT 1 (Semanas 1-2)           SPRINT 2 (Semanas 3-4)
────────────────────────         ──────────────────────────
Objetivo: Base funcional          Objetivo: Flujo de compra
──────────────────────────        ──────────────────────────
✅ HU-001 Autenticación            🔄 HU-003 Carrito
✅ HU-002 Catálogo                 🔄 HU-004 Admin Productos
   - Registro / Login              - Agregar/modificar ítems
   - JWT RS256                     - Validación de stock
   - Listado con filtros           - CRUD admin productos
   - Detalle de producto           - Movimientos inventario

SPRINT 3 (Semanas 5-6)           SPRINT 4 (Semanas 7-8)
────────────────────────         ──────────────────────────
Objetivo: Checkout completo       Objetivo: Calidad y entrega
──────────────────────────        ──────────────────────────
🔜 HU-005 Órdenes                  🔜 Pruebas E2E
   - Confirmar compra              🔜 Corrección de bugs
   - Seguimiento de pedido         🔜 Performance testing
   - Panel admin órdenes           🔜 Documentación final
   - Historial de estados          🔜 Demo con stakeholders
```

### Criterios de Éxito del MVP

| Criterio           | Métrica                                 | Meta                                                                               |
| ------------------ | --------------------------------------- | ---------------------------------------------------------------------------------- |
| Funcionalidad core | RF Must Have implementados              | 100% (RF-02, RF-03, RF-04, RF-07, RF-10, RF-12, RF-14, RF-17, RF-20, RF-22, RF-24) |
| Calidad            | Bugs críticos en demo                   | 0 bugs críticos                                                                    |
| Rendimiento        | Tiempo de respuesta API                 | p95 < 500 ms                                                                       |
| Usabilidad         | Flujo completo de compra sin asistencia | Cliente completa compra sin ayuda externa                                          |
| Despliegue         | Sistema corriendo en Docker             | `docker compose up` funciona en cualquier máquina                                  |
| Cobertura          | Pruebas unitarias servicios core        | ≥ 70% cobertura                                                                    |

---

## 4. Backlog Priorizado (Product Backlog)

| Prioridad | ID     | Historia de Usuario           | Épica       | Story Points | Sprint    |
| --------- | ------ | ----------------------------- | ----------- | ------------ | --------- |
| 1         | HU-001 | Registro e Inicio de Sesión   | EP-01       | 8            | Sprint 1  |
| 2         | HU-002 | Consulta del Catálogo         | EP-02       | 5            | Sprint 1  |
| 3         | HU-003 | Gestión del Carrito           | EP-03       | 8            | Sprint 2  |
| 4         | HU-004 | Admin de Productos            | EP-02/EP-04 | 8            | Sprint 2  |
| 5         | HU-005 | Confirmación y Seguimiento    | EP-03/EP-05 | 13           | Sprint 3  |
| 6         | HU-006 | Recuperar contraseña          | EP-01       | 5            | Release 2 |
| 7         | HU-007 | Comparar productos            | EP-02       | 3            | Release 2 |
| 8         | HU-008 | Persistencia avanzada carrito | EP-03       | 5            | Release 2 |
| 9         | HU-009 | Panel de inventario visual    | EP-04       | 5            | Release 2 |
| 10        | HU-010 | Notificaciones por email      | EP-05       | 8            | Release 2 |
| 11        | HU-011 | OAuth / Google Login          | EP-01       | 8            | Release 3 |
| 12        | HU-012 | Wishlist / Favoritos          | EP-02       | 5            | Release 3 |
| 13        | HU-013 | Pago en línea integrado       | EP-03       | 13           | Release 3 |
| 14        | HU-014 | Reportes de ventas            | EP-04       | 8            | Release 3 |
| 15        | HU-015 | Programa de fidelización      | EP-05       | 13           | Release 3 |

**Total Story Points MVP (Release 1):** 42 SP  
**Total Story Points Roadmap completo:** 115 SP
