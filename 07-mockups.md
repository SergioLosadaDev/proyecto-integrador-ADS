# Entregable 7 — Mockups y Prototipo

**Proyecto:** Accesorios D&M — Sistema E-Commerce  
**Versión:** 1.0  
**Fecha:** 2026-05-18  
**Herramienta:** Wireframes ASCII (descripción para implementación en Figma/Balsamiq)

---

## 1. Flujo de Navegación General

```
                         ┌─────────────────┐
                         │   Landing /     │
                         │   Inicio        │
                         └────────┬────────┘
                                  │
               ┌──────────────────┼──────────────────┐
               │                  │                  │
               ▼                  ▼                  ▼
        ┌──────────┐       ┌──────────┐      ┌──────────────┐
        │  Login   │       │ Registro │      │  Catálogo    │
        └────┬─────┘       └────┬─────┘      │  (público)   │
             │                  │            └──────────────┘
             └──────────────────┘
                         │ (autenticado)
                         ▼
              ┌──────────────────────┐
              │    Catálogo          │
              │    (con carrito)     │
              └──────────┬───────────┘
                         │
            ┌────────────┼────────────┐
            ▼            ▼            ▼
     ┌────────────┐ ┌─────────┐ ┌──────────────┐
     │ Detalle    │ │ Carrito │ │  Mis Pedidos │
     │ Producto   │ └────┬────┘ └──────────────┘
     └────────────┘      │
                         ▼
                  ┌──────────────┐
                  │  Resumen de  │
                  │  Compra      │
                  └──────┬───────┘
                         │
                         ▼
                  ┌──────────────┐
                  │  Confirmación│
                  │  de Orden    │
                  └──────────────┘

     [Solo ADMIN]
     ┌───────────────────────────────┐
     │  Panel de Administración      │
     │  ├── Gestión de Productos     │
     │  ├── Gestión de Categorías    │
     │  ├── Control de Inventario    │
     │  └── Gestión de Órdenes       │
     └───────────────────────────────┘
```

---

## 2. Pantalla 1 — Página de Inicio / Login

```
╔══════════════════════════════════════════════════════════════════════╗
║                     ACCESORIOS D&M                                   ║
║              ✦ Joyería artesanal hecha con amor ✦                    ║
╠══════════════════════════════════════════════════════════════════════╣
║                                                                      ║
║   ┌──────────────────────────────────────────────────────────────┐   ║
║   │                                                              │   ║
║   │   [IMAGEN HERO: Colección de accesorios artesanales]         │   ║
║   │                                                              │   ║
║   │   "Descubre nuestra colección de accesorios artesanales"     │   ║
║   │                                                              │   ║
║   │            [  VER CATÁLOGO  ]   [  INICIAR SESIÓN  ]         │   ║
║   │                                                              │   ║
║   └──────────────────────────────────────────────────────────────┘   ║
║                                                                      ║
║   ┌──────────────────────────────┐                                   ║
║   │   INICIAR SESIÓN             │                                   ║
║   │   ─────────────────────────  │                                   ║
║   │                              │                                   ║
║   │   Correo electrónico         │                                   ║
║   │   [________________________] │                                   ║
║   │                              │                                   ║
║   │   Contraseña                 │                                   ║
║   │   [________________________] │                                   ║
║   │                       [👁]   │                                   ║
║   │                              │                                   ║
║   │   [      INICIAR SESIÓN    ] │                                   ║
║   │                              │                                   ║
║   │   ¿No tienes cuenta?         │                                   ║
║   │   [Crear cuenta gratis]      │                                   ║
║   │                              │                                   ║
║   │   [¿Olvidaste tu contraseña?]│                                   ║
║   └──────────────────────────────┘                                   ║
║                                                                      ║
╠══════════════════════════════════════════════════════════════════════╣
║  Collares    Pulseras    Aretes    Anillos    Materiales              ║
╚══════════════════════════════════════════════════════════════════════╝
```

**Elementos UI:**

- Header con logo "Accesorios D&M" y navegación principal.
- Hero image con CTA principal (Ver Catálogo).
- Card de login flotante con formulario compacto.
- Links de navegación a registro y recuperación de contraseña.
- Footer con categorías de productos.

**Interacciones:**

- Clic en "VER CATÁLOGO" → redirige a `/catalog` (sin login para vista pública).
- Clic en "INICIAR SESIÓN" → valida campos, llama API, redirige a `/catalog`.
- Error de credenciales → muestra alerta roja debajo del formulario.
- Clic en "Crear cuenta gratis" → redirige a `/register`.

---

## 3. Pantalla 2 — Formulario de Registro

```
╔══════════════════════════════════════════════════════════════════════╗
║  ACCESORIOS D&M          [Inicio] [Catálogo] [Iniciar sesión]        ║
╠══════════════════════════════════════════════════════════════════════╣
║                                                                      ║
║   ┌──────────────────────────────────────────────────────────────┐   ║
║   │               CREAR MI CUENTA                                │   ║
║   │   ─────────────────────────────────────────────────────────  │   ║
║   │                                                              │   ║
║   │   Nombre completo *                                          │   ║
║   │   [________________________________________________]         │   ║
║   │                                                              │   ║
║   │   Correo electrónico *                                       │   ║
║   │   [________________________________________________]         │   ║
║   │   ⚠ El correo será tu usuario para iniciar sesión            │   ║
║   │                                                              │   ║
║   │   Contraseña *                                               │   ║
║   │   [________________________________________________] [👁]    │   ║
║   │   ✓ Mínimo 8 caracteres    ✓ Al menos 1 número               │   ║
║   │   [████████░░] Seguridad de contraseña: Buena               │   ║
║   │                                                              │   ║
║   │   Confirmar contraseña *                                     │   ║
║   │   [________________________________________________] [👁]    │   ║
║   │                                                              │   ║
║   │   Teléfono (opcional)                                        │   ║
║   │   [+57 ___________________________________________]          │   ║
║   │                                                              │   ║
║   │   ☐ Acepto los términos y condiciones                        │   ║
║   │   ☐ Deseo recibir novedades y promociones                    │   ║
║   │                                                              │   ║
║   │            [       CREAR MI CUENTA       ]                   │   ║
║   │                                                              │   ║
║   │   ¿Ya tienes cuenta? [Iniciar sesión]                        │   ║
║   └──────────────────────────────────────────────────────────────┘   ║
║                                                                      ║
╚══════════════════════════════════════════════════════════════════════╝
```

**Validaciones en tiempo real:**

- Correo: formato válido (email regex).
- Contraseña: indicador de fortaleza (débil/media/buena/fuerte).
- Confirmar contraseña: coincidencia en tiempo real.
- Botón deshabilitado hasta que todos los campos requeridos y el checkbox de términos estén completos.

---

## 4. Pantalla 3 — Catálogo de Productos

```
╔══════════════════════════════════════════════════════════════════════╗
║  ACCESORIOS D&M    [Catálogo]  [🛒 Carrito (3)]  [Mi cuenta ▼]      ║
╠══════════════════════════════════════════════════════════════════════╣
║                                                                      ║
║  NUESTROS ACCESORIOS                          [🔍 Buscar...]         ║
║  ─────────────────                                                   ║
║                                                                      ║
║  ┌─────────────────────┐  ┌──────────────────────────────────────┐   ║
║  │ FILTROS             │  │                                      │   ║
║  │ ─────────────────   │  │  Ordenar por: [Relevancia ▼]         │   ║
║  │                     │  │  150 productos encontrados           │   ║
║  │ CATEGORÍA           │  │                                      │   ║
║  │ ☑ Todos             │  │  ┌──────────┐ ┌──────────┐ ┌──────┐  │   ║
║  │ ☐ Collares (42)     │  │  │ [IMG]    │ │ [IMG]    │ │[IMG] │  │   ║
║  │ ☐ Pulseras (38)     │  │  │          │ │          │ │      │  │   ║
║  │ ☐ Aretes (35)       │  │  │ Collar   │ │ Pulsera  │ │Aretes│  │   ║
║  │ ☐ Anillos (21)      │  │  │ Plata925 │ │ Tejida   │ │Plata │  │   ║
║  │ ☐ Sets (14)         │  │  │ $85.000  │ │ $45.000  │ │$60k  │  │   ║
║  │                     │  │  │[+ Carrito│ │[+ Carrito│ │[+Car]│  │   ║
║  │ MATERIAL            │  │  └──────────┘ └──────────┘ └──────┘  │   ║
║  │ ☑ Todos             │  │                                      │   ║
║  │ ☐ Plata 925 (58)    │  │  ┌──────────┐ ┌──────────┐ ┌──────┐  │   ║
║  │ ☐ Acero Inox (49)   │  │  │ [IMG]    │ │ [IMG]    │ │[IMG] │  │   ║
║  │ ☐ Dorado (28)       │  │  │          │ │          │ │      │  │   ║
║  │ ☐ Natural (15)      │  │  │ Anillo   │ │ Set Col+ │ │Pulser│  │   ║
║  │                     │  │  │ Minima.  │ │ Pulser.  │ │Dorad.│  │   ║
║  │ PRECIO              │  │  │ $35.000  │ │ $120.000 │ │$55k  │  │   ║
║  │ Min: $0             │  │  │[+ Carrito│ │[+ Carrito│ │[+Car]│  │   ║
║  │ Max: $500.000       │  │  └──────────┘ └──────────┘ └──────┘  │   ║
║  │ [════════════]      │  │                                      │   ║
║  │                     │  │  ← 1  2  3  ...  8  →               │   ║
║  │ [Limpiar filtros]   │  └──────────────────────────────────────┘   ║
║  └─────────────────────┘                                             ║
║                                                                      ║
╚══════════════════════════════════════════════════════════════════════╝
```

**Elementos UI:**

- Sidebar izquierdo con filtros por categoría, material y rango de precio.
- Grid de productos (3 columnas desktop, 2 tablet, 1 mobile).
- Cada card: imagen, nombre, precio, botón "Agregar al carrito".
- Buscador en header que filtra en tiempo real.
- Paginación en la parte inferior.
- Contador en el ícono del carrito.

---

## 5. Pantalla 4 — Detalle de Producto

```
╔══════════════════════════════════════════════════════════════════════╗
║  ACCESORIOS D&M    [Catálogo]  [🛒 Carrito (3)]  [Mi cuenta ▼]      ║
╠══════════════════════════════════════════════════════════════════════╣
║                                                                      ║
║  Inicio > Catálogo > Collares > Collar de Plata 925                  ║
║                                                                      ║
║  ┌─────────────────────────────┐  ┌──────────────────────────────┐   ║
║  │                             │  │  Collar de Plata 925         │   ║
║  │   [IMAGEN PRINCIPAL         │  │  SKU: ACC-COL-001            │   ║
║  │    PRODUCTO - Grande]       │  │                              │   ║
║  │                             │  │  ★★★★★ (28 reseñas)          │   ║
║  │                             │  │                              │   ║
║  │                             │  │  $ 85.000 COP                │   ║
║  └─────────────────────────────┘  │  ~~$95.000~~ -11% descuento  │   ║
║                                   │                              │   ║
║  Miniaturas:                      │  Material: Plata 925         │   ║
║  [img1] [img2] [img3] [img4]      │  Categoría: Collares         │   ║
║                                   │                              │   ║
║                                   │  Stock disponible: 12 unds.  │   ║
║                                   │  ✅ En stock                  │   ║
║                                   │                              │   ║
║                                   │  Cantidad:                   │   ║
║                                   │  [−] [  2  ] [+]             │   ║
║                                   │                              │   ║
║                                   │  [   AGREGAR AL CARRITO  ]   │   ║
║                                   │                              │   ║
║                                   │  [♡ Agregar a favoritos]     │   ║
║                                   │                              │   ║
║                                   │  ─────────────────────────   │   ║
║                                   │  🚚 Envío a todo Colombia    │   ║
║                                   │  🔒 Pago seguro garantizado  │   ║
║                                   │  ↩  Devoluciones en 7 días   │   ║
║                                   └──────────────────────────────┘   ║
║                                                                      ║
║  DESCRIPCIÓN DEL PRODUCTO                                            ║
║  ─────────────────────────                                           ║
║  Collar artesanal elaborado en plata de ley 925 con acabado          ║
║  pulido. Cadena de 45 cm de largo con cierre de langosta.            ║
║  Ideal para uso diario o regalo especial...                          ║
║                                                                      ║
╚══════════════════════════════════════════════════════════════════════╝
```

---

## 6. Pantalla 5 — Carrito de Compras

```
╔══════════════════════════════════════════════════════════════════════╗
║  ACCESORIOS D&M    [Catálogo]  [🛒 Carrito (3)]  [Mi cuenta ▼]      ║
╠══════════════════════════════════════════════════════════════════════╣
║                                                                      ║
║   MI CARRITO (3 productos)                                           ║
║   ─────────────────────────                                          ║
║                                                                      ║
║  ┌────────────────────────────────────────┐  ┌─────────────────────┐ ║
║  │ PRODUCTO              PRECIO  CANT SUBTOTAL │  │  RESUMEN DE      │ ║
║  │ ──────────────────────────────────── │  │  COMPRA          │ ║
║  │                                      │  │  ─────────────── │ ║
║  │ [img] Collar Plata 925               │  │                  │ ║
║  │       SKU: ACC-COL-001       $85.000 │  │  Subtotal:       │ ║
║  │       [−][2][+]         $170.000 [🗑]│  │  $260.000        │ ║
║  │ ──────────────────────────────────── │  │                  │ ║
║  │ [img] Pulsera Tejida                 │  │  Descuentos:     │ ║
║  │       SKU: ACC-PUL-005       $45.000 │  │  - $10.000       │ ║
║  │       [−][1][+]          $45.000 [🗑]│  │                  │ ║
║  │ ──────────────────────────────────── │  │  Envío:          │ ║
║  │ [img] Aretes Plata                   │  │  $8.000          │ ║
║  │       SKU: ACC-ARE-012       $45.000 │  │  ─────────────── │ ║
║  │       [−][1][+]          $45.000 [🗑]│  │  TOTAL:          │ ║
║  │ ──────────────────────────────────── │  │  $ 258.000       │ ║
║  │                                      │  │                  │ ║
║  │ ¿Tienes un cupón? [_____________][OK]│  │  [PROCEDER AL    │ ║
║  │                                      │  │   PAGO ▶]        │ ║
║  │ [← SEGUIR COMPRANDO]                 │  │                  │ ║
║  └────────────────────────────────────── │  │  🔒 Pago seguro  │ ║
║                                          │  └─────────────────┘ ║
╚══════════════════════════════════════════════════════════════════════╝
```

---

## 7. Pantalla 6 — Resumen y Confirmación de Compra

```
╔══════════════════════════════════════════════════════════════════════╗
║  ACCESORIOS D&M    Paso: [1. Carrito] → [2. Datos] → [3. Pago]     ║
╠══════════════════════════════════════════════════════════════════════╣
║                                                                      ║
║  ┌─────────────────────────────────────┐  ┌────────────────────────┐ ║
║  │  DATOS DE ENVÍO                     │  │  TU PEDIDO             │ ║
║  │  ─────────────────────────────────  │  │  ────────────────────  │ ║
║  │                                     │  │                        │ ║
║  │  Nombre *                           │  │  Collar Plata (x2)     │ ║
║  │  [Ana María López _______________]  │  │  $170.000              │ ║
║  │                                     │  │                        │ ║
║  │  Dirección *                        │  │  Pulsera Tejida (x1)   │ ║
║  │  [Calle 45 # 22-10, Apt 301 ______] │  │  $45.000               │ ║
║  │                                     │  │                        │ ║
║  │  Ciudad *          Departamento *   │  │  Aretes Plata (x1)     │ ║
║  │  [Bogotá ________] [Cundinamar. __] │  │  $45.000               │ ║
║  │                                     │  │  ────────────────────  │ ║
║  │  Teléfono *                         │  │  Subtotal: $260.000    │ ║
║  │  [3001234567 ____________________]  │  │  Descuento: -$10.000   │ ║
║  │                                     │  │  Envío:     $8.000     │ ║
║  │  Instrucciones de entrega (opc.)    │  │  ────────────────────  │ ║
║  │  [________________________________] │  │  TOTAL: $258.000       │ ║
║  │                                     │  │                        │ ║
║  │  MÉTODO DE PAGO                     │  │                        │ ║
║  │  ─────────────────────────────────  │  │  ⚠ Al confirmar:       │ ║
║  │  ◉ Transferencia bancaria           │  │  - Se crea tu orden    │ ║
║  │    Nequi/Bancolombia                │  │  - Se reserva el stock │ ║
║  │  ○ Pago contra entrega              │  │  - Recibes confirmación│ ║
║  │    (+$5.000 recargo)                │  │    por correo          │ ║
║  │                                     │  │                        │ ║
║  │  [← REGRESAR]   [CONFIRMAR COMPRA ▶]│  │                        │ ║
║  └─────────────────────────────────────┘  └────────────────────────┘ ║
║                                                                      ║
╚══════════════════════════════════════════════════════════════════════╝
```

---

## 8. Pantalla 7 — Panel de Administración: Gestión de Productos

```
╔══════════════════════════════════════════════════════════════════════╗
║  ACCESORIOS D&M — PANEL ADMIN     [🔔 5]  [Admin ▼]      [Salir]   ║
╠══════════════╦═══════════════════════════════════════════════════════╣
║              ║  GESTIÓN DE PRODUCTOS                                 ║
║  NAVEGACIÓN  ║  ────────────────────                                 ║
║  ──────────  ║                                                       ║
║              ║  [+ NUEVO PRODUCTO]    Buscar: [______________] [🔍]  ║
║  📊 Dashboard║                                                       ║
║  📦 Productos║  ┌──────────────────────────────────────────────────┐ ║
║  🏷 Categorías  │ SKU     │ Producto       │ Precio  │Stock│Estado  │ ║
║  📋 Inventario  │──────────────────────────────────────────────────│ ║
║  🛒 Órdenes  ║  │ACC-001  │Collar Plata 925│ $85.000 │ 12  │✅ Activo│ ║
║  👥 Clientes ║  │         │                │         │     │[✏][🗑]  │ ║
║  ⚙ Config    ║  │──────────────────────────────────────────────────│ ║
║              ║  │ACC-002  │Pulsera Tejida  │ $45.000 │  5  │✅ Activo│ ║
║              ║  │         │                │         │     │[✏][🗑]  │ ║
║              ║  │──────────────────────────────────────────────────│ ║
║              ║  │ACC-003  │Aretes Dorados  │ $60.000 │  0  │⚠ Sin st.│ ║
║              ║  │         │(STOCK AGOTADO) │         │     │[✏][🗑]  │ ║
║              ║  │──────────────────────────────────────────────────│ ║
║              ║  │ACC-004  │Anillo Plata Min│ $35.000 │ 20  │✅ Activo│ ║
║              ║  │         │                │         │     │[✏][🗑]  │ ║
║              ║  │──────────────────────────────────────────────────│ ║
║              ║  │ACC-005  │Collar Vintage  │ $95.000 │  8  │⛔ Inact.│ ║
║              ║  │         │                │         │     │[✏][▶]  │ ║
║              ║  └──────────────────────────────────────────────────┘ ║
║              ║  Mostrando 5 de 142 productos  [← 1 2 3 ... 15 →]    ║
╚══════════════╩═══════════════════════════════════════════════════════╝
```

---

## 9. Pantalla 8 — Formulario de Creación/Edición de Producto (Modal Admin)

```
╔══════════════════════════════════════════════════════════════════╗
║  ✕                  CREAR NUEVO PRODUCTO                         ║
╠══════════════════════════════════════════════════════════════════╣
║                                                                  ║
║  SKU *                          Nombre *                         ║
║  [ACC-________]                 [__________________________]     ║
║                                                                  ║
║  Descripción                                                     ║
║  [__________________________________________________________]    ║
║  [__________________________________________________________]    ║
║                                                                  ║
║  Precio (COP) *       Categoría *          Material *            ║
║  [$_____________]     [Collares      ▼]    [Plata 925    ▼]      ║
║                                                                  ║
║  Stock inicial        Estado                                     ║
║  [   0        ]       ◉ Activo  ○ Inactivo                       ║
║                                                                  ║
║  IMÁGENES DEL PRODUCTO                                           ║
║  ─────────────────────                                           ║
║  ┌──────────┐  ┌──────────┐  ┌──────────┐  ┌──────────────┐     ║
║  │ [IMG 1]  │  │ [IMG 2]  │  │ [IMG 3]  │  │ + Agregar    │     ║
║  │ ★Principal│  │          │  │          │  │   imagen     │     ║
║  │ [🗑]      │  │ [🗑]      │  │ [🗑]      │  └──────────────┘     ║
║  └──────────┘  └──────────┘  └──────────┘                       ║
║  ⓘ La primera imagen es la imagen principal del catálogo         ║
║  Formatos: JPG, PNG, WebP. Máx: 2MB por imagen.                  ║
║                                                                  ║
╠══════════════════════════════════════════════════════════════════╣
║              [CANCELAR]          [GUARDAR PRODUCTO]              ║
╚══════════════════════════════════════════════════════════════════╝
```

---

## 10. Pantalla 9 — Mis Pedidos (Cliente)

```
╔══════════════════════════════════════════════════════════════════════╗
║  ACCESORIOS D&M    [Catálogo]  [🛒]  [Mi cuenta ▼ (Ana M.)]  [↩]   ║
╠══════════════════════════════════════════════════════════════════════╣
║                                                                      ║
║  MI CUENTA > MIS PEDIDOS                                             ║
║  ────────────────────────                                            ║
║                                                                      ║
║  ┌────────────────────────────────────────────────────────────────┐  ║
║  │  Pedido #00124       10 mayo 2026       Total: $258.000        │  ║
║  │  ─────────────────────────────────────────────────────────     │  ║
║  │  Estado: ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━      │  ║
║  │          [✓ CREADO] [✓ CONFIRMADO] [ENVIADO] [ ENTREGADO]      │  ║
║  │          2026-05-10  2026-05-11    (pendiente)                 │  ║
║  │                                                                │  ║
║  │  3 productos: Collar x2, Pulsera x1, Aretes x1               │  ║
║  │                              [VER DETALLE] [↩ CANCELAR]       │  ║
║  └────────────────────────────────────────────────────────────────┘  ║
║                                                                      ║
║  ┌────────────────────────────────────────────────────────────────┐  ║
║  │  Pedido #00098       22 abril 2026       Total: $85.000        │  ║
║  │  ─────────────────────────────────────────────────────────     │  ║
║  │  Estado: ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━      │  ║
║  │          [✓ CREADO] [✓ CONFIRMADO] [✓ ENVIADO] [✓ ENTREGADO]  │  ║
║  │          2026-04-22  2026-04-22    2026-04-23   2026-04-25     │  ║
║  │                                                                │  ║
║  │  1 producto: Collar Plata 925 x1                              │  ║
║  │                              [VER DETALLE] [⭐ CALIFICAR]      │  ║
║  └────────────────────────────────────────────────────────────────┘  ║
║                                                                      ║
╚══════════════════════════════════════════════════════════════════════╝
```

---

## 11. Guía de Implementación de Mockups

### Paleta de Colores Sugerida

| Elemento                          | Color           | Hex       |
| --------------------------------- | --------------- | --------- |
| Color primario (CTAs, links)      | Dorado champán  | `#C9A84C` |
| Color secundario (header, footer) | Negro elegante  | `#1A1A1A` |
| Fondo principal                   | Blanco cálido   | `#FAFAF8` |
| Texto principal                   | Gris oscuro     | `#333333` |
| Error / Alerta                    | Rojo suave      | `#E53E3E` |
| Éxito / Confirmación              | Verde esmeralda | `#38A169` |
| Advertencia (stock bajo)          | Naranja         | `#DD6B20` |

### Tipografía

| Uso                  | Fuente                      | Tamaño  |
| -------------------- | --------------------------- | ------- |
| Títulos y logo       | Playfair Display (serif)    | 24–36px |
| Subtítulos           | Playfair Display            | 18–24px |
| Cuerpo de texto      | Inter / Roboto (sans-serif) | 14–16px |
| Labels de formulario | Inter                       | 12–14px |
| Precios              | Inter Bold                  | 18–22px |

### Principios UX del Diseño

1. **Claridad visual:** Los productos son el protagonista; las imágenes deben ser grandes y de alta calidad.
2. **Flujo de compra simplificado:** Máximo 3 pasos del carrito a la confirmación del pedido.
3. **Feedback inmediato:** Cada acción (agregar al carrito, error, éxito) tiene respuesta visual en < 200ms.
4. **Mobile-first:** El diseño parte de 375px y escala hacia pantallas más grandes.
5. **Confianza:** Badges de pago seguro, política de devoluciones y datos de contacto visibles.
