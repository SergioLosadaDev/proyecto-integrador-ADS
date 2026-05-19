# Entregable 2 — Especificación de Requisitos de Software (SRS)

**Proyecto:** Accesorios D&M — Sistema E-Commerce  
**Versión:** 1.0  
**Fecha:** 2026-05-18  
**Estándar:** IEEE 830 adaptado

---

## 1. Introducción

### 1.1 Propósito

Este documento especifica los requerimientos funcionales y no funcionales del MVP del sistema e-commerce Accesorios D&M. Está dirigido al equipo de desarrollo, al cliente (propietaria del negocio) y a los evaluadores del proyecto integrador.

### 1.2 Alcance del MVP

El MVP cubre los módulos de: autenticación y roles, catálogo e inventario, carrito de compras, gestión de órdenes y pagos, e interfaz web del cliente y administrador.

### 1.3 Definiciones

| Término     | Definición                                                                |
| ----------- | ------------------------------------------------------------------------- |
| JWT         | JSON Web Token — estándar de autenticación sin estado                     |
| RS256       | Algoritmo RSA + SHA-256 para firma de tokens JWT                          |
| SKU         | Stock Keeping Unit — código único de producto                             |
| CRUD        | Create, Read, Update, Delete                                              |
| API Gateway | Punto de entrada único para todos los microservicios                      |
| Soft delete | Eliminación lógica: el registro se desactiva pero no se borra físicamente |

---

## 2. Requerimientos Funcionales

### Módulo 1: Seguridad y Autenticación

| ID    | Requerimiento                                                                     | Criterios de Aceptación                                                                                                                                                                                              | Prioridad        |
| ----- | --------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ---------------- |
| RF-01 | El sistema debe permitir el registro de usuarios con nombre, correo y contraseña. | - El correo debe ser único; si ya existe → error `409 USER_ALREADY_EXISTS`.<br>- La contraseña se almacena con hash bcrypt (cost ≥ 12).<br>- Se devuelve `201 Created` con el perfil del usuario (sin contraseña).   | Should Have      |
| RF-02 | El sistema debe permitir el inicio de sesión mediante correo y contraseña.        | - Credenciales correctas → `200 OK` con token JWT y `refreshToken`.<br>- Credenciales incorrectas → `401 INVALID_CREDENTIALS`.<br>- El token incluye claims: `sub`, `email`, `roles`, `iat`, `exp`.                  | Must Have        |
| RF-03 | El sistema debe generar y validar tokens JWT RS256 para autenticación.            | - El token tiene vigencia de 1 hora.<br>- La firma usa clave privada RSA 2048-bit.<br>- El Gateway valida localmente con la clave pública sin llamar al Security Service.<br>- Token expirado → `401 TOKEN_EXPIRED`. | Must Have        |
| RF-04 | El sistema debe manejar roles de usuario: ADMIN y CLIENTE.                        | - Un usuario tiene exactamente un rol asignado.<br>- Los endpoints protegidos validan el rol via guard.<br>- Rol incorrecto → `403 FORBIDDEN`.                                                                       | Must Have        |
| RF-05 | El sistema debe permitir la asignación de roles a usuarios por parte del ADMIN.   | - Solo usuarios ADMIN pueden cambiar roles.<br>- No se puede asignar un rol inexistente.<br>- Cambio exitoso → `200 OK` con el usuario actualizado.                                                                  | Won't Have (MVP) |
| RF-06 | El sistema debe restringir el acceso a endpoints según el rol del usuario.        | - Endpoints ADMIN son inaccesibles para CLIENTE.<br>- Todos los endpoints excepto login, registro y catálogo público requieren token válido.<br>- Token ausente → `401 UNAUTHORIZED`.                                | Must Have        |

### Módulo 2: Catálogo e Inventario

| ID    | Requerimiento                                                                               | Criterios de Aceptación                                                                                                                                                                                                                                                                                         | Prioridad   |
| ----- | ------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ----------- |
| RF-07 | El sistema debe permitir crear productos con nombre, SKU, precio, categoría y material.     | - SKU único; duplicado → `409 PRODUCT_ALREADY_EXISTS`.<br>- Precio > 0; inválido → `422 VALIDATION_ERROR`.<br>- Solo ADMIN puede crear productos.<br>- Retorna `201 Created` con el producto completo.                                                                                                          | Must Have   |
| RF-08 | El sistema debe permitir actualizar los datos de un producto existente.                     | - Solo ADMIN puede actualizar.<br>- Producto inexistente → `404 PRODUCT_NOT_FOUND`.<br>- SKU al actualizar no puede colisionar con otro producto.<br>- Retorna `200 OK` con producto actualizado.                                                                                                               | Must Have   |
| RF-09 | El sistema debe permitir la eliminación lógica de productos (soft delete).                  | - Solo ADMIN puede eliminar.<br>- El producto se marca `activo = false`; no se borra físicamente.<br>- Productos inactivos no aparecen en el catálogo público.<br>- Retorna `204 No Content`.                                                                                                                   | Must Have   |
| RF-10 | El sistema debe listar los productos disponibles con paginación y filtros.                  | - Endpoint `GET /api/v1/catalog/products` devuelve lista paginada.<br>- Parámetros: `page`, `size`, `categoryId`, `materialId`, `nombre`, `activo`.<br>- Solo devuelve productos con `activo = true` por defecto.<br>- Respuesta incluye metadatos de paginación.                                               | Must Have   |
| RF-11 | El sistema debe permitir gestionar categorías de productos (CRUD).                          | - Solo ADMIN puede crear/actualizar/eliminar categorías.<br>- Nombre de categoría único; duplicado → `409 CATEGORY_ALREADY_EXISTS`.<br>- La eliminación es lógica.<br>- `GET /api/v1/catalog/categories` es accesible para usuarios autenticados.                                                               | Should Have |
| RF-12 | El sistema debe gestionar el stock de cada producto.                                        | - El stock se actualiza automáticamente con cada movimiento de inventario.<br>- `GET /api/v1/inventory/stock` lista el stock de todos los productos.<br>- `GET /api/v1/inventory/stock/{id}` devuelve stock de un producto específico.<br>- Stock nunca puede ser negativo; intento → `409 INSUFFICIENT_STOCK`. | Must Have   |
| RF-13 | El sistema debe validar la disponibilidad de stock antes de agregar un producto al carrito. | - Si `stock < cantidadSolicitada` → `409 INSUFFICIENT_STOCK`.<br>- Si el producto está inactivo → `404 PRODUCT_NOT_FOUND`.<br>- La validación ocurre en tiempo real en el Inventory Service.                                                                                                                    | Must Have   |

### Módulo 3: Carrito de Compras

| ID    | Requerimiento                                                                            | Criterios de Aceptación                                                                                                                                                                                                                   | Prioridad   |
| ----- | ---------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ----------- |
| RF-14 | El sistema debe permitir agregar productos al carrito de compra del usuario autenticado. | - Solo usuarios CLIENTE autenticados pueden agregar al carrito.<br>- Si el producto ya está en el carrito, se suma la cantidad.<br>- Se valida stock antes de agregar (RF-13).<br>- Retorna `201 Created` con el ítem de carrito.         | Must Have   |
| RF-15 | El sistema debe permitir modificar la cantidad de un producto en el carrito.             | - Cantidad nueva debe ser > 0; si es 0 → se elimina el ítem.<br>- Se revalida stock disponible.<br>- Retorna `200 OK` con el ítem actualizado.                                                                                            | Should Have |
| RF-16 | El sistema debe permitir eliminar un producto del carrito.                               | - Solo el propietario del carrito puede eliminar ítems.<br>- Ítem inexistente → `404 CART_ITEM_NOT_FOUND`.<br>- Retorna `204 No Content`.                                                                                                 | Should Have |
| RF-17 | El sistema debe calcular automáticamente el total del carrito.                           | - El total se recalcula en cada cambio del carrito.<br>- Total = suma de (precio_unitario × cantidad) de todos los ítems.<br>- El precio_unitario se captura al momento de agregar al carrito (no varía si el producto cambia de precio). | Must Have   |
| RF-18 | El sistema debe persistir el carrito asociado al usuario autenticado entre sesiones.     | - El carrito se recupera automáticamente en el siguiente login.<br>- Solo existe un carrito activo por usuario.<br>- El carrito persiste en la base de datos (no solo en memoria).                                                        | Could Have  |

### Módulo 4: Pagos y Órdenes

| ID    | Requerimiento                                                                       | Criterios de Aceptación                                                                                                                                                                                                                                                                                                                      | Prioridad   |
| ----- | ----------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ----------- |
| RF-19 | El sistema debe permitir configurar los métodos de pago disponibles (ADMIN).        | - Solo ADMIN puede activar/desactivar métodos de pago.<br>- Métodos MVP: transferencia bancaria y pago contra entrega.<br>- `GET /api/v1/payments/methods` lista los métodos activos.                                                                                                                                                        | Could Have  |
| RF-20 | El sistema debe permitir registrar una orden de compra a partir del carrito activo. | - `POST /api/v1/orders` crea la orden desde el carrito activo del usuario.<br>- La orden captura los precios y cantidades al momento de la creación.<br>- El carrito pasa a estado `procesado` tras generar la orden.<br>- Se descuenta el stock de cada producto en el Inventory Service.<br>- Retorna `201 Created` con la orden completa. | Must Have   |
| RF-21 | El sistema debe registrar el estado del pago asociado a una orden.                  | - La orden nace con estado de pago `PENDING`.<br>- El estado puede actualizarse a `PAID`, `FAILED` o `REFUNDED`.<br>- Solo ADMIN puede cambiar manualmente el estado de pago.                                                                                                                                                                | Should Have |
| RF-22 | El sistema debe actualizar el estado de la orden (ciclo de vida).                   | - Estados posibles: `CREATED → CONFIRMED → SHIPPED → DELIVERED → CANCELLED`.<br>- Solo ADMIN puede avanzar el estado.<br>- Cada cambio queda registrado en el historial con fecha y observación.<br>- Estado regresivo no permitido (excepto CANCELLED desde cualquier estado).                                                              | Must Have   |
| RF-23 | El sistema debe permitir parametrizar impuestos o recargos aplicables.              | - El IVA se configura como parámetro global (default: 0% para MVP artesanías).<br>- El sistema aplica el impuesto automáticamente al calcular el total de la orden.<br>- Solo ADMIN puede modificar los parámetros fiscales.                                                                                                                 | Could Have  |

### Módulo 5: Frontend Web

| ID    | Requerimiento                                                                              | Criterios de Aceptación                                                                                                                                                                                                                        | Prioridad   |
| ----- | ------------------------------------------------------------------------------------------ | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ----------- |
| RF-24 | El sistema debe mostrar el catálogo de productos en la interfaz web.                       | - La página del catálogo carga en menos de 3 segundos.<br>- Los productos se muestran en grilla con imagen, nombre y precio.<br>- El cliente puede filtrar por categoría y material.<br>- Se implementa paginación de 20 productos por página. | Must Have   |
| RF-25 | El sistema debe permitir autenticación desde la interfaz web.                              | - Formulario de login con validación en cliente.<br>- Token JWT almacenado en `localStorage`/`sessionStorage`.<br>- Redirección al catálogo tras login exitoso.<br>- Mensajes de error claros para credenciales inválidas.                     | Must Have   |
| RF-26 | El sistema debe permitir la administración de productos desde la interfaz web (rol ADMIN). | - Panel de administración accesible solo con rol ADMIN.<br>- Formulario de creación/edición de productos con validación.<br>- Lista de productos con opción de desactivar/activar.<br>- Confirmación antes de eliminar un producto.            | Should Have |
| RF-27 | El sistema debe mostrar un resumen de compra antes de confirmar el pago.                   | - Página de resumen muestra: ítems, cantidades, precios, subtotal y total.<br>- El cliente puede regresar al carrito para modificar.<br>- Confirmación explícita del pedido con botón "Confirmar compra".                                      | Could Have  |
| RF-28 | El sistema debe permitir cerrar sesión.                                                    | - Botón de logout visible en el header para usuarios autenticados.<br>- Al cerrar sesión se elimina el token del storage del navegador.<br>- Se redirige al usuario a la página de inicio/login.                                               | Should Have |

---

## 3. Requerimientos No Funcionales

| ID     | Categoría               | Requerimiento                                                                                                                         | Criterio de Aceptación                                                                                                                          | Prioridad |
| ------ | ----------------------- | ------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------- | --------- |
| RNF-01 | **Rendimiento**         | El tiempo de respuesta del API Gateway no debe superar los 500 ms en el percentil 95 bajo carga normal.                               | Prueba de carga con 50 usuarios concurrentes durante 2 minutos; p95 < 500 ms medido con k6 o JMeter.                                            | Alta      |
| RNF-02 | **Disponibilidad**      | El sistema debe tener una disponibilidad del 99% en horario laboral (8:00–22:00 hora Colombia).                                       | Monitoreo con uptime check cada 5 minutos durante 30 días; máximo 4.3 horas de caída mensual.                                                   | Alta      |
| RNF-03 | **Seguridad**           | Las contraseñas de usuarios deben almacenarse con hash bcrypt con salt rounds ≥ 12.                                                   | Verificación en base de datos: ninguna contraseña en texto plano. Audit con herramienta OWASP ZAP.                                              | Alta      |
| RNF-04 | **Seguridad**           | Toda comunicación entre el cliente y el servidor debe usar HTTPS (TLS 1.2 o superior).                                                | El servidor rechaza conexiones HTTP; certificado SSL válido verificado con SSL Labs (grado A mínimo).                                           | Alta      |
| RNF-05 | **Seguridad**           | El sistema debe implementar protección contra fuerza bruta: bloquear la cuenta después de 5 intentos fallidos de login en 15 minutos. | Prueba automatizada: 6 intentos fallidos consecutivos; el 6° devuelve `429 TOO_MANY_REQUESTS` con tiempo de espera.                             | Media     |
| RNF-06 | **Escalabilidad**       | Cada microservicio debe poder escalar horizontalmente de forma independiente.                                                         | Los servicios son stateless (sin sesión en memoria); pueden ejecutar múltiples instancias detrás de un load balancer sin modificaciones.        | Media     |
| RNF-07 | **Mantenibilidad**      | La cobertura de pruebas unitarias del backend debe ser mínimo del 70% en las clases de servicio.                                      | Reporte de cobertura generado con JaCoCo (Inventory Service) y Jest (API Gateway); cobertura ≥ 70% visible en CI.                               | Media     |
| RNF-08 | **Usabilidad**          | La interfaz web debe ser responsiva y funcionar correctamente en dispositivos móviles (resolución mínima 375px).                      | Prueba en Chrome DevTools con viewport móvil; sin desbordamiento horizontal, elementos clickeables con tamaño mínimo 44×44 px.                  | Alta      |
| RNF-09 | **Usabilidad**          | Las páginas del frontend deben cargar en menos de 3 segundos en una conexión de 10 Mbps.                                              | Medición con Lighthouse en modo mobile; Performance score ≥ 70, LCP < 3 s.                                                                      | Media     |
| RNF-10 | **Portabilidad**        | El sistema completo (backend + base de datos) debe poder levantarse en cualquier entorno con Docker y Docker Compose.                 | `docker compose up` levanta todos los servicios sin configuración manual adicional, usando el `.env.example` como base.                         | Alta      |
| RNF-11 | **Trazabilidad**        | Cada solicitud HTTP debe generar un `traceId` único propagado entre servicios para correlación de logs.                               | Verificar en los logs de todos los servicios que un mismo `traceId` aparece para la misma solicitud.                                            | Media     |
| RNF-12 | **Integridad de datos** | El sistema no debe permitir vender más unidades de las que hay en stock.                                                              | Test de concurrencia: 10 usuarios intentando comprar el último ítem simultáneamente; solo 1 compra exitosa, 9 reciben `409 INSUFFICIENT_STOCK`. | Alta      |
| RNF-13 | **Recuperabilidad**     | El sistema debe incluir estrategia de backup automático diario de la base de datos PostgreSQL.                                        | Script de backup configurado en cron; backup generado en las últimas 24 horas verificable en almacenamiento.                                    | Media     |
| RNF-14 | **Compatibilidad**      | El frontend debe funcionar en las últimas 2 versiones de Chrome, Firefox, Safari y Edge.                                              | Pruebas manuales o con BrowserStack en los 4 navegadores; sin errores críticos de JavaScript en la consola.                                     | Media     |
| RNF-15 | **Auditabilidad**       | Toda acción de modificación de datos (crear/actualizar/eliminar) por parte del ADMIN debe quedar registrada en log de auditoría.      | Los logs del servicio registran: timestamp, usuario (sub del JWT), acción realizada y recurso afectado. Verificable en Kibana o grep en logs.   | Baja      |

---

## 4. Matriz de Trazabilidad RF → Casos de Uso

| Requerimiento Funcional            | Caso de Uso Asociado        | Actor Principal   |
| ---------------------------------- | --------------------------- | ----------------- |
| RF-01 — Registro de usuario        | CU-01: Registrar Usuario    | Cliente           |
| RF-02 — Inicio de sesión           | CU-02: Iniciar Sesión       | Cliente / Admin   |
| RF-03 — Generar/validar JWT        | CU-02: Iniciar Sesión       | Sistema (Gateway) |
| RF-04 — Manejar roles              | CU-02, CU-03, CU-04         | Sistema           |
| RF-06 — Restringir acceso por rol  | CU-03: Gestionar Productos  | Admin             |
| RF-07 — Crear producto             | CU-03: Gestionar Productos  | Admin             |
| RF-08 — Actualizar producto        | CU-03: Gestionar Productos  | Admin             |
| RF-09 — Eliminar producto          | CU-03: Gestionar Productos  | Admin             |
| RF-10 — Listar productos           | CU-04: Consultar Catálogo   | Cliente / Admin   |
| RF-11 — Gestionar categorías       | CU-03: Gestionar Productos  | Admin             |
| RF-12 — Gestionar stock            | CU-05: Gestionar Inventario | Admin             |
| RF-13 — Validar stock en carrito   | CU-06: Gestionar Carrito    | Sistema / Cliente |
| RF-14 — Agregar al carrito         | CU-06: Gestionar Carrito    | Cliente           |
| RF-15 — Modificar cantidad         | CU-06: Gestionar Carrito    | Cliente           |
| RF-16 — Eliminar del carrito       | CU-06: Gestionar Carrito    | Cliente           |
| RF-17 — Calcular total             | CU-06: Gestionar Carrito    | Sistema           |
| RF-18 — Persistir carrito          | CU-06: Gestionar Carrito    | Sistema           |
| RF-19 — Configurar métodos de pago | CU-07: Parametrizar Sistema | Admin             |
| RF-20 — Registrar orden            | CU-08: Realizar Compra      | Cliente           |
| RF-21 — Estado de pago             | CU-09: Gestionar Órdenes    | Admin             |
| RF-22 — Actualizar estado de orden | CU-09: Gestionar Órdenes    | Admin             |
| RF-23 — Parametrizar impuestos     | CU-07: Parametrizar Sistema | Admin             |
| RF-24 — Mostrar catálogo           | CU-04: Consultar Catálogo   | Cliente           |
| RF-25 — Autenticación web          | CU-02: Iniciar Sesión       | Cliente / Admin   |
| RF-26 — Admin de productos web     | CU-03: Gestionar Productos  | Admin             |
| RF-27 — Resumen de compra          | CU-08: Realizar Compra      | Cliente           |
| RF-28 — Cerrar sesión              | CU-10: Cerrar Sesión        | Cliente / Admin   |
