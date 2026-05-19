# Entregable 3 — Diagramas UML

**Proyecto:** Accesorios D&M — Sistema E-Commerce  
**Versión:** 1.0  
**Fecha:** 2026-05-18  
**Herramienta de notación:** PlantUML / Mermaid

---

## 1. Diagramas de Casos de Uso

### CU-01: Registro e Inicio de Sesión (Autenticación)

```plantuml
@startuml CU-01-Autenticacion
left to right direction
skinparam actorStyle awesome

actor "Cliente" as C
actor "Administrador" as A
actor "Sistema (Gateway)" as S <<system>>

rectangle "Sistema Accesorios D&M — Autenticación" {
  usecase "Registrar cuenta" as UC1
  usecase "Iniciar sesión" as UC2
  usecase "Validar token JWT" as UC3
  usecase "Renovar token (refresh)" as UC4
  usecase "Cerrar sesión" as UC5
  usecase "Verificar correo único" as UC6
  usecase "Encriptar contraseña" as UC7
}

C --> UC1
C --> UC2
C --> UC5
A --> UC2
A --> UC5

UC1 ..> UC6 : <<include>>
UC1 ..> UC7 : <<include>>
UC2 ..> UC3 : <<include>>
UC2 ..> UC4 : <<extend>>
S --> UC3

note right of UC3
  El Gateway valida JWT RS256
  localmente, sin llamar al
  Security Service
end note
@enduml
```

**Actores:**

- **Cliente:** Usuario registrado que desea acceder al sistema.
- **Administrador:** Empleado de la tienda con permisos elevados.
- **Sistema (Gateway):** Actor secundario que valida tokens automáticamente en cada request.

**Casos de uso:**

| ID  | Nombre                 | Descripción                                                       |
| --- | ---------------------- | ----------------------------------------------------------------- |
| UC1 | Registrar cuenta       | El cliente crea una cuenta con correo, contraseña y nombre        |
| UC2 | Iniciar sesión         | El usuario se autentica y recibe un token JWT RS256               |
| UC3 | Validar token JWT      | El Gateway verifica firma, expiración e issuer del token          |
| UC4 | Renovar token          | Extensión: si el token está próximo a expirar, se emite uno nuevo |
| UC5 | Cerrar sesión          | El token se invalida en cliente; opcionalmente en blacklist       |
| UC6 | Verificar correo único | Include de UC1: comprueba que el correo no esté registrado        |
| UC7 | Encriptar contraseña   | Include de UC1: aplica bcrypt antes de persistir                  |

---

### CU-02: Gestión del Catálogo de Productos (Admin)

```plantuml
@startuml CU-02-GestionCatalogo
left to right direction
skinparam actorStyle awesome

actor "Administrador" as A

rectangle "Sistema Accesorios D&M — Catálogo" {
  usecase "Crear producto" as UC8
  usecase "Editar producto" as UC9
  usecase "Eliminar producto (lógico)" as UC10
  usecase "Gestionar categorías" as UC11
  usecase "Gestionar materiales" as UC12
  usecase "Subir imagen de producto" as UC13
  usecase "Validar SKU único" as UC14
  usecase "Asignar categoría al producto" as UC15
}

A --> UC8
A --> UC9
A --> UC10
A --> UC11
A --> UC12

UC8 ..> UC14 : <<include>>
UC8 ..> UC15 : <<include>>
UC8 ..> UC13 : <<extend>>
UC9 ..> UC14 : <<include>>

note right of UC10
  Soft delete: activo = false
  El producto no aparece en
  el catálogo público
end note
@enduml
```

**Actores:**

- **Administrador:** Único actor autorizado para modificar el catálogo.

**Flujo principal (Crear producto):**

1. Admin accede al panel de administración.
2. Completa formulario: SKU, nombre, descripción, precio, categoría, material.
3. El sistema valida que el SKU no existe.
4. El sistema guarda el producto con `activo = true`.
5. Opcionalmente, el admin sube imágenes del producto.

---

### CU-03: Consulta del Catálogo (Cliente)

```plantuml
@startuml CU-03-ConsultaCatalogo
left to right direction
skinparam actorStyle awesome

actor "Cliente" as C
actor "Visitante" as V

rectangle "Sistema Accesorios D&M — Consulta Catálogo" {
  usecase "Ver catálogo de productos" as UC16
  usecase "Buscar producto por nombre" as UC17
  usecase "Filtrar por categoría" as UC18
  usecase "Filtrar por material" as UC19
  usecase "Ver detalle de producto" as UC20
  usecase "Ver imágenes del producto" as UC21
  usecase "Paginar resultados" as UC22
}

C --> UC16
C --> UC17
C --> UC18
C --> UC19
C --> UC20
V --> UC16
V --> UC20

UC16 ..> UC22 : <<include>>
UC17 ..> UC16 : <<extend>>
UC18 ..> UC16 : <<extend>>
UC19 ..> UC16 : <<extend>>
UC20 ..> UC21 : <<include>>

note bottom of UC16
  Devuelve solo productos
  con activo = true
end note
@enduml
```

**Actores:**

- **Cliente:** Usuario autenticado que navega el catálogo.
- **Visitante:** Usuario no autenticado (si se habilita el acceso público al catálogo).

---

### CU-04: Gestión del Carrito de Compras

```plantuml
@startuml CU-04-Carrito
left to right direction
skinparam actorStyle awesome

actor "Cliente" as C
actor "Sistema Inventario" as SI <<system>>

rectangle "Sistema Accesorios D&M — Carrito" {
  usecase "Agregar producto al carrito" as UC23
  usecase "Modificar cantidad de ítem" as UC24
  usecase "Eliminar ítem del carrito" as UC25
  usecase "Ver carrito actual" as UC26
  usecase "Calcular total del carrito" as UC27
  usecase "Validar disponibilidad de stock" as UC28
  usecase "Aplicar precio vigente" as UC29
}

C --> UC23
C --> UC24
C --> UC25
C --> UC26
SI --> UC28

UC23 ..> UC28 : <<include>>
UC23 ..> UC29 : <<include>>
UC23 ..> UC27 : <<include>>
UC24 ..> UC28 : <<include>>
UC24 ..> UC27 : <<include>>
UC26 ..> UC27 : <<include>>

note right of UC28
  Consulta stock en tiempo real
  al Inventory Service
  Stock < cantidad → error 409
end note

note right of UC29
  El precio se captura al momento
  de agregar, no varía si cambia
  después
end note
@enduml
```

**Actores:**

- **Cliente:** Gestiona su carrito de compras activo.
- **Sistema Inventario:** Valida disponibilidad de stock en tiempo real.

---

### CU-05: Proceso de Compra y Gestión de Órdenes

```plantuml
@startuml CU-05-Ordenes
left to right direction
skinparam actorStyle awesome

actor "Cliente" as C
actor "Administrador" as A
actor "Pasarela de Pago" as PP <<external>>

rectangle "Sistema Accesorios D&M — Órdenes y Pagos" {
  usecase "Confirmar compra y crear orden" as UC30
  usecase "Ver resumen de compra" as UC31
  usecase "Procesar pago" as UC32
  usecase "Actualizar estado de orden" as UC33
  usecase "Consultar historial de órdenes" as UC34
  usecase "Cancelar orden" as UC35
  usecase "Registrar estado de pago" as UC36
  usecase "Descontar stock al confirmar" as UC37
}

C --> UC31
C --> UC30
C --> UC34
C --> UC35
A --> UC33
A --> UC36
PP --> UC32

UC30 ..> UC31 : <<include>>
UC30 ..> UC37 : <<include>>
UC30 ..> UC32 : <<extend>>
UC32 ..> UC36 : <<include>>
UC33 ..> UC36 : <<include>>

note right of UC37
  El Inventory Service descuenta
  el stock de cada producto
  al crear la orden
end note
@enduml
```

**Actores:**

- **Cliente:** Confirma la compra y puede consultar sus órdenes.
- **Administrador:** Gestiona el ciclo de vida de las órdenes.
- **Pasarela de Pago:** Actor externo que procesa el pago (MVP: sandbox).

---

### CU-06: Gestión de Inventario (Admin)

```plantuml
@startuml CU-06-Inventario
left to right direction
skinparam actorStyle awesome

actor "Administrador" as A
actor "Vendedor" as V

rectangle "Sistema Accesorios D&M — Inventario" {
  usecase "Ver stock de todos los productos" as UC38
  usecase "Ver stock de un producto" as UC39
  usecase "Registrar movimiento de inventario" as UC40
  usecase "Ver historial de movimientos" as UC41
  usecase "Registrar entrada de mercancía" as UC42
  usecase "Registrar salida por venta" as UC43
  usecase "Registrar ajuste de inventario" as UC44
}

A --> UC38
A --> UC39
A --> UC40
A --> UC41
V --> UC40

UC40 ..> UC42 : <<extend>>
UC40 ..> UC43 : <<extend>>
UC40 ..> UC44 : <<extend>>

note bottom of UC43
  La salida por venta ocurre
  automáticamente al confirmar
  una orden (RF-20)
end note
@enduml
```

---

## 2. Diagrama de Clases

```mermaid
classDiagram
    direction TB

    class Usuario {
        +Long id
        +String nombre
        +String correo
        +String passwordHash
        +Boolean activo
        +LocalDateTime fechaCreacion
        +Rol rol
        +login(correo, password) String
        +logout() void
    }

    class Rol {
        +Long id
        +String nombre
        +String descripcion
    }

    class Cliente {
        +Long id
        +String nombre
        +String correo
        +String telefono
        +LocalDateTime fechaRegistro
        +List~Carrito~ carritos
        +List~Pedido~ pedidos
    }

    class Categoria {
        +Long id
        +String nombre
        +String descripcion
        +Boolean estado
        +List~Producto~ productos
    }

    class Material {
        +Long id
        +String nombre
        +String descripcion
        +List~Producto~ productos
    }

    class Producto {
        +Long id
        +String sku
        +String nombre
        +String descripcion
        +BigDecimal precio
        +Integer stock
        +Boolean activo
        +LocalDateTime fechaCreacion
        +Categoria categoria
        +Material material
        +List~ImagenProducto~ imagenes
        +validarStock(cantidad) boolean
        +actualizarStock(cantidad, tipo) void
    }

    class ImagenProducto {
        +Long id
        +String urlImagen
        +Integer orden
        +Producto producto
    }

    class Carrito {
        +Long id
        +LocalDateTime fechaCreacion
        +EstadoCarrito estado
        +Cliente cliente
        +List~ItemCarrito~ items
        +calcularTotal() BigDecimal
        +agregarItem(producto, cantidad) void
        +eliminarItem(itemId) void
        +vaciar() void
    }

    class ItemCarrito {
        +Long id
        +Integer cantidad
        +BigDecimal precioUnitario
        +Carrito carrito
        +Producto producto
        +calcularSubtotal() BigDecimal
    }

    class Pedido {
        +Long id
        +String direccionEnvio
        +String telefonoContacto
        +BigDecimal total
        +LocalDateTime fechaPedido
        +Cliente cliente
        +EstadoPedido estadoActual
        +List~DetallePedido~ detalles
        +List~HistorialEstadoPedido~ historial
        +confirmar() void
        +cancelar() void
        +avanzarEstado(nuevoEstado, observacion) void
    }

    class DetallePedido {
        +Long id
        +Integer cantidad
        +BigDecimal precioUnitario
        +BigDecimal subtotal
        +Pedido pedido
        +Producto producto
    }

    class EstadoPedido {
        +Long id
        +String nombre
        +String descripcion
    }

    class HistorialEstadoPedido {
        +Long id
        +LocalDateTime fechaCambio
        +String observacion
        +Pedido pedido
        +EstadoPedido estado
    }

    class MovimientoInventario {
        +Long id
        +Integer cantidad
        +LocalDateTime fechaMovimiento
        +String referencia
        +Producto producto
        +TipoMovimiento tipoMovimiento
    }

    class TipoMovimiento {
        +Long id
        +String nombre
        +String descripcion
    }

    class MetodoPago {
        +Long id
        +String nombre
        +Boolean activo
    }

    %% Relaciones
    Usuario "1" --> "1" Rol : tiene
    Cliente "1" --> "0..*" Carrito : posee
    Cliente "1" --> "0..*" Pedido : realiza
    Carrito "1" --> "1..*" ItemCarrito : contiene
    ItemCarrito "1" --> "1" Producto : referencia
    Producto "1" --> "1" Categoria : pertenece a
    Producto "1" --> "1" Material : compuesto de
    Producto "1" --> "0..*" ImagenProducto : tiene
    Producto "1" --> "0..*" MovimientoInventario : registra
    MovimientoInventario "1" --> "1" TipoMovimiento : es de tipo
    Pedido "1" --> "1..*" DetallePedido : contiene
    Pedido "1" --> "1" EstadoPedido : tiene estado
    Pedido "1" --> "0..*" HistorialEstadoPedido : registra
    HistorialEstadoPedido "1" --> "1" EstadoPedido : referencia
    DetallePedido "1" --> "1" Producto : referencia
```

---

## 3. Diagramas de Secuencia

### DS-01: Flujo de Inicio de Sesión y Validación JWT

```mermaid
sequenceDiagram
    actor Cliente
    participant Frontend as Frontend (Angular)
    participant Gateway as API Gateway (NestJS)
    participant Security as Security Service
    participant DB as Base de Datos

    Cliente->>Frontend: Ingresa correo y contraseña
    Frontend->>Frontend: Valida formulario (campos requeridos)
    Frontend->>Gateway: POST /api/v1/auth/login {correo, password}

    Gateway->>Security: POST /internal/auth/login {correo, password}
    Security->>DB: SELECT empleado/cliente WHERE correo = ?
    DB-->>Security: Datos del usuario con hash

    alt Credenciales válidas
        Security->>Security: bcrypt.compare(password, hash)
        Security->>Security: Genera JWT RS256 {sub, email, roles, exp}
        Security-->>Gateway: 200 OK {accessToken, refreshToken, expiresIn}
        Gateway-->>Frontend: 200 OK {accessToken, refreshToken}
        Frontend->>Frontend: Almacena token en localStorage
        Frontend-->>Cliente: Redirige a /catalog
    else Credenciales inválidas
        Security-->>Gateway: 401 INVALID_CREDENTIALS
        Gateway-->>Frontend: 401 INVALID_CREDENTIALS
        Frontend-->>Cliente: Muestra mensaje de error
    end

    Note over Frontend,Gateway: En requests posteriores
    Cliente->>Frontend: Solicita recurso protegido
    Frontend->>Gateway: GET /api/v1/catalog/products<br/>Authorization: Bearer {token}
    Gateway->>Gateway: Valida JWT RS256 localmente<br/>(sin llamar a Security Service)

    alt Token válido
        Gateway->>Gateway: Extrae claims → headers X-User-*
        Gateway->>Security: Proxy request con headers internos
        Security-->>Gateway: Respuesta
        Gateway-->>Frontend: Respuesta al cliente
    else Token expirado
        Gateway-->>Frontend: 401 TOKEN_EXPIRED
        Frontend->>Frontend: Intenta refresh token
    end
```

---

### DS-02: Flujo de Agregar Producto al Carrito y Crear Orden

```mermaid
sequenceDiagram
    actor Cliente
    participant Frontend as Frontend (Angular)
    participant Gateway as API Gateway
    participant Payment as Payment Service
    participant Inventory as Inventory Service
    participant DB as Base de Datos

    Cliente->>Frontend: Selecciona producto y cantidad
    Frontend->>Gateway: POST /api/v1/cart/items<br/>{productoId, cantidad}
    Gateway->>Payment: POST /internal/cart/items

    Payment->>Inventory: GET /internal/stock/{productoId}
    Inventory->>DB: SELECT stock WHERE id = ?
    DB-->>Inventory: stock = 15

    alt Stock disponible (stock >= cantidad)
        Inventory-->>Payment: {stockDisponible: 15}
        Payment->>DB: INSERT item_carrito<br/>{cantidad, precioUnitario}
        Payment->>Payment: Recalcula total del carrito
        Payment-->>Gateway: 201 Created {carrito actualizado}
        Gateway-->>Frontend: 201 Created
        Frontend-->>Cliente: Carrito actualizado con total
    else Stock insuficiente
        Inventory-->>Payment: stock = 0
        Payment-->>Gateway: 409 INSUFFICIENT_STOCK
        Gateway-->>Frontend: 409 INSUFFICIENT_STOCK
        Frontend-->>Cliente: "Stock insuficiente para este producto"
    end

    Note over Cliente,DB: Proceso de confirmación de compra
    Cliente->>Frontend: Hace clic en "Confirmar compra"
    Frontend->>Gateway: POST /api/v1/orders
    Gateway->>Payment: POST /internal/orders

    Payment->>DB: SELECT carrito activo del usuario
    DB-->>Payment: items del carrito con precios

    loop Por cada ítem del carrito
        Payment->>Inventory: POST /internal/stock/reserve<br/>{productoId, cantidad}
        Inventory->>DB: UPDATE stock = stock - cantidad
        DB-->>Inventory: OK
        Inventory-->>Payment: Stock reservado
    end

    Payment->>DB: INSERT pedido con detalles
    Payment->>DB: UPDATE carrito.estado = 'procesado'
    Payment-->>Gateway: 201 Created {pedido}
    Gateway-->>Frontend: 201 Created {pedido}
    Frontend-->>Cliente: "Pedido #12345 creado exitosamente"
```

---

### DS-03: Flujo de Gestión de Producto por Administrador

```mermaid
sequenceDiagram
    actor Admin
    participant Frontend as Frontend Admin (Angular)
    participant Gateway as API Gateway
    participant Inventory as Inventory Service
    participant DB as Base de Datos

    Admin->>Frontend: Completa formulario de nuevo producto
    Frontend->>Frontend: Valida campos requeridos (SKU, nombre, precio, categoría)
    Frontend->>Gateway: POST /api/v1/catalog/products<br/>Authorization: Bearer {tokenAdmin}<br/>{sku, nombre, precio, categoriaId, materialId}

    Gateway->>Gateway: Valida JWT, extrae rol = ADMIN
    Gateway->>Inventory: POST /internal/catalog/products<br/>X-User-Roles: ADMIN

    Inventory->>DB: SELECT producto WHERE sku = ?
    DB-->>Inventory: null (SKU disponible)

    alt SKU disponible
        Inventory->>DB: SELECT categoria WHERE id = categoriaId
        DB-->>Inventory: Categoría válida

        Inventory->>DB: INSERT producto {sku, nombre, precio, stock=0, activo=true}
        DB-->>Inventory: producto creado con id

        alt Admin sube imagen
            Admin->>Frontend: Selecciona archivo de imagen
            Frontend->>Gateway: POST /api/v1/catalog/products/{id}/images<br/>multipart/form-data
            Gateway->>Inventory: POST /internal/catalog/products/{id}/images
            Inventory->>Inventory: Valida tipo y tamaño de archivo
            Inventory->>DB: INSERT imagen_producto {url, orden}
            Inventory-->>Gateway: 201 Created {imagen}
            Gateway-->>Frontend: Imagen guardada
        end

        Inventory-->>Gateway: 201 Created {producto completo}
        Gateway-->>Frontend: 201 Created
        Frontend-->>Admin: "Producto creado exitosamente"
    else SKU duplicado
        Inventory-->>Gateway: 409 PRODUCT_ALREADY_EXISTS
        Gateway-->>Frontend: 409 PRODUCT_ALREADY_EXISTS
        Frontend-->>Admin: "El SKU ya está registrado"
    end
```

---

## 4. Diagrama de Estados

### Estados del Pedido

```mermaid
stateDiagram-v2
    [*] --> CREATED : Cliente confirma compra\nSe descuenta stock

    CREATED --> CONFIRMED : Admin confirma pedido\nPago recibido
    CREATED --> CANCELLED : Cliente cancela\nAdmin cancela\nPago fallido

    CONFIRMED --> SHIPPED : Admin registra envío\nNúmero de guía asignado

    SHIPPED --> DELIVERED : Confirmación de entrega\nCliente recibe el pedido

    DELIVERED --> [*] : Proceso completado

    CANCELLED --> [*] : Stock restaurado

    note right of CREATED
        Estado inicial al crear la orden.
        Stock ya descontado.
        Pago aún pendiente.
    end note

    note right of CONFIRMED
        Pago verificado por Admin.
        Preparando para envío.
    end note

    note right of SHIPPED
        Producto en camino.
        Cliente puede rastrear.
    end note

    note right of CANCELLED
        Se restaura el stock
        de todos los ítems.
    end note
```

### Estados del Carrito

```mermaid
stateDiagram-v2
    [*] --> ACTIVO : Cliente agrega primer producto

    ACTIVO --> ACTIVO : Agregar/modificar/eliminar ítems\nTotal recalculado

    ACTIVO --> PROCESADO : Cliente confirma compra\nOrden creada

    ACTIVO --> ABANDONADO : Sin actividad > 7 días

    PROCESADO --> [*] : Orden generada exitosamente

    ABANDONADO --> ACTIVO : Cliente regresa y agrega item
    ABANDONADO --> [*] : Limpieza automática del sistema

    note right of ACTIVO
        Un usuario solo puede
        tener un carrito ACTIVO
        a la vez.
    end note
```

### Estados del Pago

```mermaid
stateDiagram-v2
    [*] --> PENDING : Orden creada

    PENDING --> PAID : Pago confirmado\npor pasarela o Admin

    PENDING --> FAILED : Pago rechazado\npor pasarela

    PENDING --> EXPIRED : Tiempo de pago\nsuperado (24h)

    PAID --> REFUNDED : Solicitud de devolución\naprobada por Admin

    FAILED --> PENDING : Cliente reintenta\nel pago

    EXPIRED --> [*] : Orden cancelada automáticamente

    PAID --> [*] : Ciclo completado

    REFUNDED --> [*] : Devolución procesada
```

---

## 5. Decisiones de Diseño Documentadas

| Decisión                                     | Alternativas Consideradas       | Justificación                                                                                                      |
| -------------------------------------------- | ------------------------------- | ------------------------------------------------------------------------------------------------------------------ |
| **JWT RS256 con clave pública en Gateway**   | JWT HS256, sesiones en servidor | RS256 permite validación local sin llamar al Security Service, reduciendo latencia y punto de fallo.               |
| **Arquitectura de microservicios**           | Monolito, arquitectura modular  | Permite escalar servicios independientemente; el equipo puede trabajar en paralelo por módulo.                     |
| **Soft delete** para productos               | Hard delete                     | Preserva integridad referencial con órdenes históricas; permite recuperar productos desactivados.                  |
| **Captura de precio al agregar al carrito**  | Precio dinámico en tiempo real  | Evita que cambios de precio durante la sesión alteren el total esperado por el cliente.                            |
| **Schema por bounded context en PostgreSQL** | Base de datos por servicio      | Simplifica el MVP manteniendo aislamiento lógico; facilita la migración futura a BD por servicio.                  |
| **Stack políglota (NestJS + Spring Boot)**   | Un solo lenguaje                | Aprovecha fortalezas de cada framework: NestJS para proxy/middleware, Spring Boot para lógica de negocio compleja. |
