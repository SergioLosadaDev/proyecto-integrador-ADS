# Entregable 5 — Historias de Usuario

**Proyecto:** Accesorios D&M — Sistema E-Commerce  
**Versión:** 1.0  
**Fecha:** 2026-05-18  
**Formato:** Agile — con DOR (Definition of Ready), DOD (Definition of Done) y Criterios de Aceptación

---

## HU-001 — Registro e Inicio de Sesión

| Campo            | Valor                             |
| ---------------- | --------------------------------- |
| **ID**           | HU-001                            |
| **Épica**        | EP-01: Seguridad y Autenticación  |
| **Feature**      | FT-01: Gestión de Identidad       |
| **Prioridad**    | Alta (Must Have)                  |
| **Story Points** | 8                                 |
| **Sprint**       | Sprint 1                          |
| **RF Asociados** | RF-01, RF-02, RF-03, RF-04, RF-06 |

### Historia de Usuario

> **Como** cliente del e-commerce de Accesorios D&M,  
> **quiero** poder registrarme con mi correo y contraseña, e iniciar sesión de forma segura,  
> **para** acceder a mi cuenta personal, ver mis pedidos y realizar compras de manera autenticada.

### Definition of Ready (DOR) — Criterios para iniciar desarrollo

- [x] La historia está estimada (8 story points).
- [x] Los mockups del formulario de login y registro están aprobados.
- [x] El contrato API de autenticación está documentado (`security-service-v1.yaml`).
- [x] El esquema de base de datos para `security.empleado` y `clientes.cliente` está migrado.
- [x] Las claves RSA (pública/privada) para JWT están generadas y en el `.env.example`.
- [x] Los criterios de aceptación están definidos y acordados con el equipo.
- [x] La historia no tiene dependencias bloqueantes pendientes.

### Criterios de Aceptación

**CA-001.1 — Registro exitoso:**

- Dado que un visitante accede al formulario de registro,
- Cuando completa nombre completo, correo válido y contraseña (mín. 8 caracteres, al menos 1 número),
- Entonces el sistema crea la cuenta con rol `CLIENTE`, encripta la contraseña con bcrypt y devuelve `201 Created` con el perfil del usuario (sin contraseña).

**CA-001.2 — Correo ya registrado:**

- Dado que un visitante intenta registrarse con un correo ya existente,
- Cuando envía el formulario,
- Entonces el sistema devuelve `409 USER_ALREADY_EXISTS` y el mensaje "El correo ya está registrado".

**CA-001.3 — Login exitoso:**

- Dado que un usuario registrado accede al formulario de login,
- Cuando ingresa correo y contraseña correctos,
- Entonces el sistema devuelve `200 OK` con `accessToken` (JWT RS256, exp: 1h) y `refreshToken`, y la UI redirige al catálogo.

**CA-001.4 — Credenciales inválidas:**

- Dado que un usuario intenta iniciar sesión,
- Cuando ingresa una contraseña incorrecta,
- Entonces el sistema devuelve `401 INVALID_CREDENTIALS` y muestra "Correo o contraseña incorrectos" (sin especificar cuál falló, por seguridad).

**CA-001.5 — Token JWT válido en requests posteriores:**

- Dado que un usuario está autenticado,
- Cuando realiza cualquier request a endpoints protegidos con su token,
- Entonces el Gateway valida el token localmente (sin llamar al Security Service) y permite el acceso.

**CA-001.6 — Token expirado:**

- Dado que el token del usuario ha expirado (después de 1 hora),
- Cuando intenta acceder a un recurso protegido,
- Entonces el sistema devuelve `401 TOKEN_EXPIRED` y el frontend lo redirige al login.

### Definition of Done (DOD) — Criterios para cerrar la historia

- [ ] Endpoint `POST /api/v1/auth/register` implementado, probado y documentado.
- [ ] Endpoint `POST /api/v1/auth/login` implementado, probado y documentado.
- [ ] La contraseña se almacena con bcrypt cost ≥ 12 (verificado en BD).
- [ ] El token JWT incluye claims: `sub`, `email`, `roles`, `iat`, `exp`.
- [ ] El Gateway valida el JWT RS256 con la clave pública sin llamar al Security Service.
- [ ] Pruebas unitarias del `AuthService` con cobertura ≥ 70%.
- [ ] Pruebas de integración: registro, login correcto, login incorrecto, token expirado.
- [ ] Formulario de login y registro en Angular con validación de campos.
- [ ] Código revisado y aprobado por al menos 1 reviewer.
- [ ] PR mergeado a `develop`.
- [ ] No hay secretos en el repositorio.

---

## HU-002 — Consulta del Catálogo de Productos

| Campo            | Valor                        |
| ---------------- | ---------------------------- |
| **ID**           | HU-002                       |
| **Épica**        | EP-02: Catálogo e Inventario |
| **Feature**      | FT-02: Consulta de Productos |
| **Prioridad**    | Alta (Must Have)             |
| **Story Points** | 5                            |
| **Sprint**       | Sprint 1                     |
| **RF Asociados** | RF-10, RF-11, RF-24          |

### Historia de Usuario

> **Como** cliente autenticado,  
> **quiero** explorar el catálogo de accesorios con filtros por categoría y material,  
> **para** encontrar rápidamente los productos que me interesan y tomar decisiones de compra informadas.

### Definition of Ready (DOR)

- [x] La historia está estimada (5 story points).
- [x] El endpoint `GET /api/v1/catalog/products` está especificado en el OpenAPI YAML.
- [x] Los datos de prueba (categorías, materiales, productos) están en las migraciones Liquibase.
- [x] El mockup de la página del catálogo está aprobado (grilla de productos).
- [x] Los criterios de paginación y filtros están acordados: `page`, `size`, `categoryId`, `materialId`, `nombre`.
- [x] No hay dependencias bloqueantes.

### Criterios de Aceptación

**CA-002.1 — Listado paginado:**

- Dado que un usuario autenticado accede al catálogo,
- Cuando carga la página `/catalog`,
- Entonces ve una grilla de 20 productos (máximo) con imagen principal, nombre y precio, con controles de paginación.

**CA-002.2 — Filtro por categoría:**

- Dado que el usuario selecciona la categoría "Collares",
- Cuando aplica el filtro,
- Entonces solo se muestran productos de esa categoría; el contador de resultados se actualiza.

**CA-002.3 — Búsqueda por nombre:**

- Dado que el usuario escribe "plata" en el buscador,
- Cuando ejecuta la búsqueda,
- Entonces el sistema devuelve todos los productos cuyo nombre contenga "plata" (búsqueda case-insensitive, ILIKE en PostgreSQL).

**CA-002.4 — Detalle de producto:**

- Dado que el usuario hace clic en un producto,
- Cuando carga la página de detalle,
- Entonces ve: todas las imágenes del producto, descripción completa, precio, categoría, material y stock disponible.

**CA-002.5 — Productos inactivos no visibles:**

- Dado que un producto fue desactivado por el Admin,
- Cuando el cliente consulta el catálogo,
- Entonces ese producto no aparece en ningún listado ni es accesible por URL directa.

**CA-002.6 — Tiempo de carga:**

- Dado que el usuario carga el catálogo,
- Cuando la lista tiene hasta 1000 productos en BD,
- Entonces la respuesta del API llega en menos de 500 ms (p95).

### Definition of Done (DOD)

- [ ] `GET /api/v1/catalog/products` con paginación y filtros implementado y documentado.
- [ ] `GET /api/v1/catalog/products/{id}` con detalle completo (categoría, material, imágenes).
- [ ] Componente Angular de catálogo: grilla, filtros, paginación.
- [ ] Componente Angular de detalle de producto.
- [ ] Pruebas del `ProductService` con cobertura ≥ 70%.
- [ ] La búsqueda por nombre es case-insensitive (ILIKE verificado).
- [ ] Código revisado por al menos 1 reviewer.
- [ ] PR mergeado a `develop`.

---

## HU-003 — Gestión del Carrito de Compras

| Campo            | Valor                              |
| ---------------- | ---------------------------------- |
| **ID**           | HU-003                             |
| **Épica**        | EP-03: Carrito y Proceso de Compra |
| **Feature**      | FT-03: Carrito de Compras          |
| **Prioridad**    | Alta (Must Have)                   |
| **Story Points** | 8                                  |
| **Sprint**       | Sprint 2                           |
| **RF Asociados** | RF-13, RF-14, RF-15, RF-16, RF-17  |

### Historia de Usuario

> **Como** cliente autenticado,  
> **quiero** agregar productos a un carrito, modificar cantidades y ver el total actualizado,  
> **para** preparar mi pedido de accesorios y tener claridad sobre el valor total antes de pagar.

### Definition of Ready (DOR)

- [x] La historia está estimada (8 story points).
- [x] El endpoint de Inventory Service para consultar stock (`GET /internal/stock/{id}`) está implementado.
- [x] El esquema de BD `ventas.carrito` y `ventas.item_carrito` está migrado.
- [x] El contrato API del Payment Service está especificado para los endpoints de carrito.
- [x] El mockup del carrito (listado de ítems, totales, botones de modificar/eliminar) está aprobado.
- [x] Los criterios de validación de stock están acordados (RF-13).

### Criterios de Aceptación

**CA-003.1 — Agregar al carrito con stock disponible:**

- Dado que el cliente está en el detalle de un producto con stock = 10,
- Cuando agrega 2 unidades al carrito,
- Entonces el ítem aparece en el carrito con cantidad 2, precio unitario capturado al momento y subtotal calculado.

**CA-003.2 — Validación de stock insuficiente:**

- Dado que el cliente intenta agregar 5 unidades de un producto con stock = 2,
- Cuando hace clic en "Agregar al carrito",
- Entonces el sistema muestra "Solo quedan 2 unidades disponibles" y no agrega el ítem.

**CA-003.3 — Modificar cantidad:**

- Dado que el cliente tiene un ítem en el carrito con cantidad 2,
- Cuando incrementa la cantidad a 3 (y hay stock ≥ 3),
- Entonces el ítem se actualiza y el total del carrito se recalcula automáticamente.

**CA-003.4 — Eliminar ítem del carrito:**

- Dado que el cliente tiene 3 ítems en el carrito,
- Cuando elimina uno,
- Entonces ese ítem desaparece del carrito y el total se recalcula mostrando solo los 2 restantes.

**CA-003.5 — Total del carrito:**

- Dado que el carrito tiene ítems con diferentes cantidades y precios,
- Cuando el cliente visualiza el carrito,
- Entonces el total mostrado es la suma exacta de (precio_unitario × cantidad) de todos los ítems activos.

**CA-003.6 — Un carrito activo por usuario:**

- Dado que el cliente cierra sesión y vuelve a iniciarla,
- Cuando accede al carrito,
- Entonces los ítems del carrito anterior siguen ahí (persistencia entre sesiones).

### Definition of Done (DOD)

- [ ] `POST /api/v1/cart/items` — agregar ítem con validación de stock.
- [ ] `PUT /api/v1/cart/items/{id}` — modificar cantidad con revalidación de stock.
- [ ] `DELETE /api/v1/cart/items/{id}` — eliminar ítem del carrito.
- [ ] `GET /api/v1/cart` — ver carrito activo con total calculado.
- [ ] Comunicación entre Payment Service e Inventory Service para validar stock implementada.
- [ ] El total se recalcula correctamente en cada modificación.
- [ ] Pruebas unitarias del `CartService` con cobertura ≥ 70%.
- [ ] Componente Angular del carrito con modificación y eliminación de ítems.
- [ ] Código revisado por al menos 1 reviewer y PR mergeado a `develop`.

---

## HU-004 — Administración de Productos (Panel Admin)

| Campo            | Valor                             |
| ---------------- | --------------------------------- |
| **ID**           | HU-004                            |
| **Épica**        | EP-02: Catálogo e Inventario      |
| **Feature**      | FT-04: Gestión Admin de Productos |
| **Prioridad**    | Alta (Must Have)                  |
| **Story Points** | 8                                 |
| **Sprint**       | Sprint 2                          |
| **RF Asociados** | RF-07, RF-08, RF-09, RF-12, RF-26 |

### Historia de Usuario

> **Como** administradora de Accesorios D&M,  
> **quiero** crear, editar y desactivar productos desde el panel de administración web,  
> **para** mantener el catálogo actualizado con los accesorios disponibles en la tienda sin necesidad de conocimientos técnicos.

### Definition of Ready (DOR)

- [x] La historia está estimada (8 story points).
- [x] Los endpoints CRUD de productos están especificados en el OpenAPI YAML.
- [x] El mockup del panel de administración con formulario de producto está aprobado.
- [x] El esquema de BD para `catalogo.producto`, `catalogo.categoria` y `catalogo.material` está migrado.
- [x] La guarda de roles en el Gateway está implementada (RF-06) para verificar rol ADMIN.
- [x] Los campos requeridos del producto están acordados: SKU, nombre, precio, categoríaId.

### Criterios de Aceptación

**CA-004.1 — Crear producto exitoso:**

- Dado que el administrador completa el formulario con SKU, nombre, precio y categoría,
- Cuando hace clic en "Crear producto",
- Entonces el producto se guarda con `activo = true`, `stock = 0` y aparece en el catálogo inmediatamente.

**CA-004.2 — Validación de SKU único:**

- Dado que el administrador intenta crear un producto con SKU "ACC-001" ya existente,
- Cuando envía el formulario,
- Entonces el sistema devuelve el mensaje "El SKU ACC-001 ya está registrado" sin crear el producto.

**CA-004.3 — Editar producto:**

- Dado que el administrador accede a un producto y modifica su precio,
- Cuando guarda los cambios,
- Entonces el nuevo precio se refleja en el catálogo y en el detalle del producto.

**CA-004.4 — Desactivar producto (soft delete):**

- Dado que el administrador hace clic en "Desactivar" sobre un producto,
- Cuando confirma la acción en el diálogo de confirmación,
- Entonces el producto pasa a `activo = false` y deja de aparecer en el catálogo de clientes (pero el registro permanece en BD).

**CA-004.5 — Solo ADMIN puede gestionar productos:**

- Dado que un usuario con rol CLIENTE intenta acceder a `POST /api/v1/catalog/products`,
- Cuando envía la solicitud con su token,
- Entonces el Gateway devuelve `403 FORBIDDEN` y el producto no se crea.

**CA-004.6 — Gestionar stock (movimiento de inventario):**

- Dado que el administrador registra una entrada de 50 unidades de un producto,
- Cuando guarda el movimiento con tipo "ENTRADA",
- Entonces el stock del producto se incrementa en 50 y el movimiento queda registrado en el historial.

### Definition of Done (DOD)

- [ ] `POST /api/v1/catalog/products` — crear producto (solo ADMIN).
- [ ] `PUT /api/v1/catalog/products/{id}` — actualizar producto (solo ADMIN).
- [ ] `DELETE /api/v1/catalog/products/{id}` — soft delete (solo ADMIN).
- [ ] `POST /api/v1/inventory/movements` — registrar movimiento de stock (ADMIN/VENDEDOR).
- [ ] Guard de rol ADMIN activo en todos los endpoints de escritura.
- [ ] Panel de administración en Angular: listado, formulario de creación/edición, confirmación de desactivación.
- [ ] Pruebas unitarias con cobertura ≥ 70% en `ProductService`.
- [ ] Código revisado y PR mergeado a `develop`.

---

## HU-005 — Confirmación de Compra y Seguimiento de Pedido

| Campo            | Valor                              |
| ---------------- | ---------------------------------- |
| **ID**           | HU-005                             |
| **Épica**        | EP-03: Carrito y Proceso de Compra |
| **Feature**      | FT-05: Órdenes y Seguimiento       |
| **Prioridad**    | Alta (Must Have)                   |
| **Story Points** | 13                                 |
| **Sprint**       | Sprint 3                           |
| **RF Asociados** | RF-20, RF-21, RF-22, RF-27         |

### Historia de Usuario

> **Como** cliente autenticado de Accesorios D&M,  
> **quiero** confirmar mi compra y hacer seguimiento del estado de mi pedido,  
> **para** tener certeza de que mi orden fue recibida correctamente y saber en qué etapa se encuentra el proceso de entrega.

### Definition of Ready (DOR)

- [x] La historia está estimada (13 story points).
- [x] Los endpoints de órdenes están especificados en el contrato API del Payment Service.
- [x] El esquema de BD `ventas.pedido`, `ventas.detalle_pedido`, `logistica.estado_pedido` y `logistica.historial_estado_pedido` está migrado.
- [x] Los estados del pedido están definidos: `CREATED → CONFIRMED → SHIPPED → DELIVERED` y `CANCELLED`.
- [x] La comunicación entre Payment Service e Inventory Service para descontar stock está acordada.
- [x] El mockup de la página de resumen de compra y de "Mis pedidos" está aprobado.
- [x] El equipo acordó que el pago en el MVP se registra manualmente por el Admin (sandbox).

### Criterios de Aceptación

**CA-005.1 — Resumen de compra antes de confirmar:**

- Dado que el cliente tiene ítems en su carrito y hace clic en "Proceder al pago",
- Cuando carga la página de resumen,
- Entonces ve: lista de productos con imágenes, cantidades, precios unitarios, subtotales, y total final destacado.

**CA-005.2 — Confirmar orden:**

- Dado que el cliente está en la página de resumen y completa la dirección de envío,
- Cuando hace clic en "Confirmar compra",
- Entonces el sistema crea la orden con estado `CREATED`, descuenta el stock de cada producto, marca el carrito como `procesado` y muestra el número de orden.

**CA-005.3 — Error por stock agotado al confirmar:**

- Dado que otro cliente compró el último ítem entre que el primero lo agregó al carrito y lo confirmó,
- Cuando el primer cliente confirma su compra,
- Entonces el sistema devuelve `409 INSUFFICIENT_STOCK` indicando el producto sin stock y no crea la orden.

**CA-005.4 — Historial de órdenes del cliente:**

- Dado que el cliente accede a "Mis pedidos",
- Cuando la página carga,
- Entonces ve una lista de sus órdenes anteriores con: número, fecha, total, cantidad de productos y estado actual.

**CA-005.5 — Seguimiento de estado de orden:**

- Dado que el cliente tiene una orden con estado `CONFIRMED`,
- Cuando el administrador actualiza el estado a `SHIPPED` con el número de guía,
- Entonces el cliente puede ver el nuevo estado en "Mis pedidos" y recibe notificación (email en MVP).

**CA-005.6 — Cancelación de orden:**

- Dado que el cliente tiene una orden con estado `CREATED`,
- Cuando solicita cancelarla,
- Entonces el estado pasa a `CANCELLED`, el stock de los productos se restaura y el cambio queda registrado en el historial.

### Definition of Done (DOD)

- [ ] `POST /api/v1/orders` — crear orden desde carrito activo, con descuento de stock.
- [ ] `GET /api/v1/orders` — listar órdenes del usuario autenticado.
- [ ] `GET /api/v1/orders/{id}` — detalle de una orden con historial de estados.
- [ ] `PUT /api/v1/orders/{id}/status` — actualizar estado (solo ADMIN).
- [ ] `DELETE /api/v1/orders/{id}` — cancelar orden (restaura stock).
- [ ] Página de resumen de compra en Angular con confirmación explícita.
- [ ] Página "Mis pedidos" en Angular con historial y estado actual.
- [ ] Manejo de concurrencia: prueba con 2 usuarios comprando el último ítem (solo uno debe tener éxito).
- [ ] Pruebas unitarias del `OrderService` con cobertura ≥ 70%.
- [ ] Código revisado y PR mergeado a `develop`.
