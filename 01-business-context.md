# Entregable 1 — Documentación del Negocio y Contexto

**Proyecto:** Accesorios D&M — Sistema E-Commerce  
**Versión:** 1.0  
**Fecha:** 2026-05-18  
**Equipo:** Sergio A. Losada B.

---

## 1. Descripción del Problema

### 1.1 Situación Actual (AS-IS)

Accesorios D&M es un emprendimiento dedicado a la fabricación y comercialización de accesorios artesanales (collares, pulseras, aretes, anillos) elaborados principalmente en plata 925, acero inoxidable y materiales naturales. Actualmente la empresa opera exclusivamente a través de canales informales:

- **Instagram** (`@accesorios_dm`): publicación de fotos de productos y recepción de consultas por mensajes directos.
- **WhatsApp Business**: negociación de precios, confirmación de pedidos y coordinación de pagos/entregas.
- **Gestión manual de inventario**: registro en hojas de cálculo (Excel/Google Sheets) sin control en tiempo real.
- **Cobros**: transferencias bancarias o pagos en efectivo, sin trazabilidad sistematizada.
- **Registro de clientes**: sin base de datos centralizada; la información queda dispersa en conversaciones de WhatsApp.

### 1.2 Problemas Identificados

| #    | Problema                                                                        | Impacto                                                    |
| ---- | ------------------------------------------------------------------------------- | ---------------------------------------------------------- |
| P-01 | Los pedidos se gestionan manualmente por chat, generando pérdidas y confusiones | Alto — pedidos incompletos o duplicados                    |
| P-02 | No existe control de inventario en tiempo real                                  | Alto — ventas de productos sin stock                       |
| P-03 | No hay catálogo digital accesible 24/7                                          | Medio — dependencia del horario de la vendedora            |
| P-04 | Los datos de clientes están dispersos en chats                                  | Medio — imposibilidad de fidelización                      |
| P-05 | Sin historial de órdenes ni seguimiento de pedidos                              | Medio — clientes sin trazabilidad de su compra             |
| P-06 | Proceso de pago no estandarizado                                                | Alto — riesgo de fraudes y errores                         |
| P-07 | No existe diferenciación de roles (administrador vs. cliente)                   | Bajo-Medio — cualquier persona puede modificar información |
| P-08 | Escalabilidad limitada: una sola persona atiende todo                           | Alto — cuello de botella operativo                         |

### 1.3 Justificación del Proyecto

El crecimiento sostenido del e-commerce en Colombia (incremento del 32% en ventas online en 2024 según la Cámara Colombiana de Comercio Electrónico) y el potencial del segmento de joyería artesanal hacen necesaria la digitalización del negocio. La construcción de un sistema propio permitirá:

- Eliminar intermediarios y reducir la carga operativa manual en un estimado del 70%.
- Escalar la oferta de productos sin aumentar el personal de atención.
- Generar datos de negocio para decisiones estratégicas (productos más vendidos, clientes frecuentes).
- Proporcionar una experiencia de compra profesional y confiable a los clientes.
- Habilitar múltiples canales de venta (web y mobile) desde una sola plataforma backend.

---

## 2. Stakeholders

### 2.1 Stakeholders Internos

| Rol                            | Nombre              | Responsabilidades                           | Interés en el Sistema                   |
| ------------------------------ | ------------------- | ------------------------------------------- | --------------------------------------- |
| Propietaria / Administradora   | Propietaria D&M     | Gestión de productos, inventario, pedidos   | Alto — usuaria primaria del panel admin |
| Desarrollador Backend / DevOps | Sergio A. Losada B. | Arquitectura, servicios backend, despliegue | Alto — desarrollador principal          |

### 2.2 Stakeholders Externos

| Rol                         | Descripción                                                       | Interés en el Sistema                            |
| --------------------------- | ----------------------------------------------------------------- | ------------------------------------------------ |
| Cliente final               | Persona que compra accesorios (principalmente mujeres 18-45 años) | Alto — experiencia de compra ágil y segura       |
| Proveedor de pagos          | Pasarela de pagos (Wompi / Epayco / PayU Colombia)                | Medio — integración técnica                      |
| Proveedor de alojamiento    | Nube (Render / Railway / AWS)                                     | Bajo — infraestructura                           |
| Autoridades fiscales (DIAN) | Cumplimiento tributario                                           | Bajo — facturas electrónicas (versiones futuras) |

---

## 3. Contexto del Dominio

### 3.1 Dominio Principal: E-Commerce de Accesorios Artesanales

El dominio abarca el ciclo completo de una venta online:

```
Catálogo → Selección → Carrito → Pago → Pedido → Entrega → Historial
```

### 3.2 Bounded Contexts Identificados

| Bounded Context           | Responsabilidad                                     | Servicio                           |
| ------------------------- | --------------------------------------------------- | ---------------------------------- |
| **Seguridad**             | Autenticación, autorización, roles de usuario       | `accesorios-dm-security-service`   |
| **Catálogo / Inventario** | Productos, categorías, materiales, stock            | `accesorios-dm-inventory-service`  |
| **Ventas / Carrito**      | Carrito de compras, ítems, totales                  | `accesorios-dm-payment-service`    |
| **Pagos / Órdenes**       | Órdenes de compra, estados de pago, parametrización | `accesorios-dm-payment-service`    |
| **Logística**             | Estados del pedido, historial de cambios            | Integrado en payment service (MVP) |
| **Frontend Web**          | Interfaz del cliente y del administrador            | `accesorios-dm-frontend`           |

### 3.3 Usuarios del Sistema

| Tipo          | Descripción                         | Permisos                                                          |
| ------------- | ----------------------------------- | ----------------------------------------------------------------- |
| **ADMIN**     | Propietaria o empleado de confianza | Gestión completa: productos, inventario, órdenes, usuarios        |
| **CLIENTE**   | Comprador registrado                | Navegar catálogo, gestionar carrito, realizar y consultar pedidos |
| **Visitante** | Usuario no autenticado              | Solo visualización pública del catálogo (si se habilita)          |

### 3.4 Restricciones y Supuestos

| Tipo                    | Descripción                                                                         |
| ----------------------- | ----------------------------------------------------------------------------------- |
| Restricción técnica     | El MVP debe funcionar con infraestructura de bajo costo (Docker + VPS ~$10 USD/mes) |
| Restricción de tiempo   | Entrega del MVP en 4 sprints de 2 semanas                                           |
| Supuesto                | Los pagos en el MVP se gestionan contra una pasarela de pruebas (sandbox)           |
| Supuesto                | El envío físico es coordinado externamente; el sistema solo registra la dirección   |
| Restricción regulatoria | No se requiere factura electrónica DIAN en el MVP                                   |

---

## 4. Visión del Producto

> **Para** clientes que desean comprar accesorios artesanales de calidad,  
> **el sistema Accesorios D&M** es una plataforma e-commerce web y mobile,  
> **que** centraliza el catálogo, el carrito de compras, los pagos y la gestión de pedidos,  
> **a diferencia de** la gestión manual por Instagram/WhatsApp,  
> **nuestro producto** elimina la dependencia de un intermediario humano, ofrece disponibilidad 24/7 y garantiza trazabilidad completa de cada transacción.

---

## 5. Arquitectura de Alto Nivel del MVP

```
┌────────────────────────────────────────────────────┐
│                   CLIENTES                         │
│  ┌──────────────────┐    ┌──────────────────────┐  │
│  │  Frontend Web    │    │  App Móvil (futuro)  │  │
│  │  (Angular)       │    │  (Flutter)           │  │
│  │  :4200           │    │                      │  │
│  └────────┬─────────┘    └──────────┬───────────┘  │
└───────────┼─────────────────────────┼──────────────┘
            │ HTTPS / REST            │
            └──────────────┬──────────┘
                           ▼
              ┌────────────────────────┐
              │     API Gateway        │
              │     (NestJS)           │  JWT RS256 · Rate Limiting
              │     :3000              │  CORS · Proxy
              └────────────┬───────────┘
          ┌────────────────┼─────────────────┐
          ▼                ▼                 ▼
 ┌────────────────┐ ┌──────────────┐ ┌──────────────────┐
 │ Inventory Svc  │ │ Security Svc │ │  Payment Svc     │
 │ (Spring Boot)  │ │ (Spring Boot)│ │  (Spring Boot)   │
 │ :8082          │ │ :8081        │ │  :8083           │
 └───────┬────────┘ └──────┬───────┘ └────────┬─────────┘
         │                 │                  │
         └─────────────────┴──────────────────┘
                           │
              ┌────────────▼────────────┐
              │      PostgreSQL 16      │
              │  schemas: catalogo      │
              │           inventario    │
              │           security      │
              │           ventas        │
              │           logistica     │
              │           clientes      │
              └─────────────────────────┘
```
