# Documentación Final
## Sistema Integral para el Control y Venta de Autopartes, Lubricantes y Suministros Vehiculares


## CONTROL DE DOCUMENTO

| Campo | Información |
|-------|-------------|
| **Título del Proyecto** | Sistema Integral para el Control y Venta de Autopartes |
| **Versión del Documento** | [1.5] |
| **Fecha de Creación** | [30/08/2025] |
| **Última Actualización** | [26/09/2025] |
| **Autor(es)** | [Angel Ambrocio,Wilson Coc, Josue Car, X] |
| **Estado** | [En Desarrollo...] |

### Historial de Versiones

| Versión | Fecha | Autor | Descripción de Cambios |
|---------|-------|-------|------------------------|
| 1.0 | [DD/MM/AAAA] | [Nombre] | Versión inicial |
| 1.1 | [DD/MM/AAAA] | [Nombre] | Actualización de APIs |
| | | | |

---

## 📑 ÍNDICE

1. [Resumen Ejecutivo](#resumen-ejecutivo)
2. [Arquitectura General](#arquitectura-general)
3. [Documentación de Base de Datos](#documentación-de-base-de-datos)
4. [Documentación de Backend](#documentación-de-backend)
5. [Documentación de Frontend](#documentación-de-frontend)
6. [Documentación de Integración](#documentación-de-integración)
7. [Documentación de Despliegue](#documentación-de-despliegue)
8. [Documentación de Testing](#documentación-de-testing)
9. [Documentación de Mantenimiento](#documentación-de-mantenimiento)
10. [Anexos](#anexos)

---

## 1. RESUMEN EJECUTIVO

### 1.1 Propósito del Sistema
[Descripción breve del sistema de autopartes y sus objetivos principales]

### 1.2 Alcance del Proyecto
- **Módulos Implementados:**
  - Gestión de Inventario de Autopartes
  - Sistema de Ventas y Facturación
  - Control de Proveedores y Compras
  - Gestión de Clientes
  - Reportes y Analytics
  - [Otros módulos]

### 1.3 Tecnologías Utilizadas
- **Frontend:** [React/Vue/Angular + versión]
- **Backend:** [Node.js/Python/Java + framework]
- **Base de Datos:** [PostgreSQL/MySQL/MongoDB + versión]
- **Infraestructura:** [AWS/Azure/GCP]

### 1.4 Equipo de Desarrollo
| Equipos | Nombre |
|-----|--------|
| **1.** | Analisis Y Diseño |
| **2.** | Base De Datos |
| **3.** | Backend |
| **4.**  | Frontend |
| **5.**  | Documentacion,pruebas y QA |

---

## 2. ARQUITECTURA GENERAL

### 2.1 Arquitectura del Sistema

```mermaid
graph TB
    subgraph "Frontend Layer"
        UI[Web Application]
        Mobile[Mobile App]
    end
    
    subgraph "API Gateway"
        Gateway[API Gateway/Load Balancer]
    end
    
    subgraph "Backend Services"
        Auth[Authentication Service]
        Inventory[Inventory Service]
        Sales[Sales Service]
        Reports[Reports Service]
    end
    
    subgraph "Data Layer"
        DB[(PostgreSQL)]
        Cache[(Redis)]
        Files[File Storage]
    end
    
    UI --> Gateway
    Mobile --> Gateway
    Gateway --> Auth
    Gateway --> Inventory
    Gateway --> Sales
    Gateway --> Reports
    Auth --> DB
    Inventory --> DB
    Sales --> DB
    Reports --> DB
    Backend Services --> Cache
```

### 2.2 Patrones de Diseño Implementados
- **Arquitectura:** Microservicios/Monolito Modular
- **Patrones Backend:** Repository, Service Layer, Factory
- **Patrones Frontend:** Component-based, State Management
- **Patrones de Datos:** Active Record/Data Mapper

### 2.3 Principios de Arquitectura
- Separación de responsabilidades
- Escalabilidad horizontal
- Tolerancia a fallos
- Seguridad por capas

---

## 3. DOCUMENTACIÓN DE BASE DE DATOS

### 3.1 Modelo Entidad-Relación

```mermaid
erDiagram
    PRODUCTS ||--o{ INVENTORY : contains
    PRODUCTS ||--o{ SALE_ITEMS : sold_in
    CATEGORIES ||--o{ PRODUCTS : categorizes
    SUPPLIERS ||--o{ PRODUCTS : supplies
    CUSTOMERS ||--o{ SALES : makes
    SALES ||--o{ SALE_ITEMS : contains
    USERS ||--o{ SALES : processes
    
    PRODUCTS {
        int product_id PK
        string sku UK
        string name
        string description
        decimal price
        int category_id FK
        int supplier_id FK
        boolean active
        datetime created_at
        datetime updated_at
    }
    
    INVENTORY {
        int inventory_id PK
        int product_id FK
        int quantity
        int min_stock
        int max_stock
        string location
        datetime last_updated
    }
    
    SALES {
        int sale_id PK
        string invoice_number UK
        int customer_id FK
        int user_id FK
        decimal subtotal
        decimal tax
        decimal total
        string status
        datetime sale_date
    }
```

### 3.2 Diccionario de Datos

#### Tabla: PRODUCTS
| Campo | Tipo | Longitud | Nulo | Clave | Descripción |
|-------|------|----------|------|-------|-------------|
| product_id | INT | - | NO | PK | Identificador único del producto |
| sku | VARCHAR | 50 | NO | UK | Código SKU del producto |
| name | VARCHAR | 200 | NO | - | Nombre del producto |
| description | TEXT | - | SÍ | - | Descripción detallada |
| price | DECIMAL | 10,2 | NO | - | Precio unitario |
| category_id | INT | - | NO | FK | Referencia a categoría |
| supplier_id | INT | - | NO | FK | Referencia a proveedor |
| active | BOOLEAN | - | NO | - | Estado activo/inactivo |
| created_at | TIMESTAMP | - | NO | - | Fecha de creación |
| updated_at | TIMESTAMP | - | NO | - | Fecha de actualización |

### 3.3 Índices y Optimización
```sql
-- Índices principales
CREATE INDEX idx_products_sku ON products(sku);
CREATE INDEX idx_products_category ON products(category_id);
CREATE INDEX idx_inventory_product ON inventory(product_id);
CREATE INDEX idx_sales_date ON sales(sale_date);
CREATE INDEX idx_sales_customer ON sales(customer_id);
```

### 3.4 Procedimientos Almacenados
```sql
-- Ejemplo: Actualizar inventario después de venta
DELIMITER //
CREATE PROCEDURE UpdateInventoryAfterSale(
    IN p_product_id INT,
    IN p_quantity INT
)
BEGIN
    UPDATE inventory 
    SET quantity = quantity - p_quantity,
        last_updated = NOW()
    WHERE product_id = p_product_id;
END //
DELIMITER ;
```

### 3.5 Estrategia de Backup
- **Backup Completo:** Diario a las 2:00 AM
- **Backup Incremental:** Cada 6 horas
- **Retención:** 30 días para backups diarios, 12 meses para backups semanales
- **Ubicación:** [Especificar ubicación de backups]

---

## 4. DOCUMENTACIÓN DE BACKEND

### 4.1 Arquitectura de Servicios
### **Descripción**
API REST para plataforma de comercio electrónico del sector automotriz que conecta usuarios, proveedores y administradores para la compra y venta de herramientas, repuestos y productos automotrices.

### **Arquitectura**
- **Backend:** Node.js + Express
- **Base de datos:** PostgreSQL + Prisma ORM
- **Autenticación:** JWT + 2FA (TOTP)
- **Contenedores:** Docker + Docker Compose
- **Documentación:** Swagger
- **Testing:** Jest + Supertest


## Roles Principales
- **Usuario**: Puede buscar productos, comprar y ver comercios cercanos.
- **Proveedor**: Puede registrar su comercio, gestionar su catálogo y stock.
- **Administrador**: Gestiona usuarios, proveedores y el sistema en general.

## Requisitos Previos
- Node.js v18 o superior
- PostgreSQL (con usuario y base de datos creados)
- npm (incluido con Node.js)

## Instalación
1. Clona el repositorio e instala dependencias:
   ```sh
   git clone https://github.com/junajpu616/backend-umg.git
   cd backend-umg
   npm install
   ```

2. Copia el archivo de variables de entorno y edítalo:
   ```sh
   copy .env.example .env
   ```

3. Genera el cliente de Prisma y aplica migraciones:
   ```sh
   npm run prisma:generate
   npm run prisma:migrate
   ```

4. (Opcional) Ejecuta el seed para crear un usuario administrador:
   ```sh
   npm run seed
   ```

5. Inicia el servidor:
   ```sh
   npm start
   ```
   
## Alternativamente  con Docker
   
   ```bash
# Construir y ejecutar todos los servicios
docker-compose up --build

# O ejecutar en segundo plano
docker-compose up -d --build
```

## Uso
- La API expone endpoints para autenticación, gestión de usuarios, productos y proveedores.
- Soporta autenticación JWT y 2FA.
- Los proveedores pueden gestionar su catálogo y stock desde la plataforma.

### **URLs del proyecto**
- API: `http://localhost:3000`
- Documentación: `http://localhost:3000/api-docs`
- PostgreSQL: `localhost:5433`
- PgAdmin: `http://localhost:8080`
- 
## Notas
- El sistema está preparado para ser consumido tanto por aplicaciones web como móviles.
- Asegúrate de configurar correctamente las variables de entorno en el archivo `.env`.   

#### 4.1.1 Estructura de Directorios
```
backend-umg-main/
├── 📄 .dockerignore                    # Archivos ignorados en Docker build
├── 📄 .env                             # Variables de entorno (no en repo)
├── 📄 .env.example                     # Plantilla de variables de entorno
├── 📄 docker-compose.yml               # Orquestación de contenedores
├── 📄 dockerfile                       # Imagen Docker del backend
├── 📄 jest.config.js                   # Configuración de Jest
├── 📄 package.json                     # Dependencias y scripts NPM
├── 📄 README-DOCKER.md                 # Guía específica de Docker
├── 📄 README.md                        # Documentación principal
│
├── 📁 .idea/                           # Configuración de IDE
│   ├── .gitignore
│   └── vcs.xml
│
├── 📁 generated/                       # Cliente generado por Prisma
│   └── prisma/
│       ├── client.d.ts                 # Tipos TypeScript
│       ├── client.js                   # Cliente JavaScript
│       └── ... (otros archivos generados)
│
├── 📁 prisma/                          # Esquema y migraciones de BD
│   ├── 📄 schema.prisma                # Esquema de base de datos
│   ├── 📄 seed.js                      # Datos iniciales (admin)
│   │
│   └── 📁 migrations/                  # Historial de migraciones
│       ├── 📄 migration_lock.toml      # Lock de proveedor de BD
│       ├── 📁 20250813035250_first_migration/
│       │   └── migration.sql
│       ├── 📁 20250813041243_init/
│       │   └── migration.sql
│       ├── 📁 20250813041625_init/
│       │   └── migration.sql
│       ├── 📁 20250813042150_init/
│       │   └── migration.sql
│       ├── 📁 20250813042436_init/
│       │   └── migration.sql
│       ├── 📁 20250814030555_init/
│       │   └── migration.sql
│       ├── 📁 20250814031858_cambiar_precio_decimal/
│       │   └── migration.sql
│       ├── 📁 20250920200403_add_user/
│       │   └── migration.sql
│       ├── 📁 20250922003236_add_2fa/
│       │   └── migration.sql
│       ├── 📁 20250922025244_add_roles_and_providers/
│       │   └── migration.sql
│       ├── 📁 20250922031933_simplify_roles/
│       │   └── migration.sql
│       └── 📁 20250922035758_add_active_field/
│           └── migration.sql
│
├── 📁 scripts/                         # Scripts de inicialización
│   └── 📄 docker-init.sh               # Script para Docker
│
├── 📁 src/                             # Código fuente principal
│   ├── 📄 app.js                       # Configuración de Express
│   ├── 📄 server.js                    # Punto de entrada
│   ├── 📄 swagger.js                   # Configuración Swagger/OpenAPI
│   │
│   ├── 📁 config/                      # Configuraciones
│   │   └── 📄 prisma.js                # Cliente de Prisma
│   │
│   ├── 📁 controllers/                 # Lógica de negocio
│   │   ├── 📄 admin.controller.js      # Gestión de administradores
│   │   ├── 📄 auth.controller.js       # Autenticación básica
│   │   ├── 📄 product.controller.js    # Gestión de productos
│   │   └── 📄 twofa.controller.js      # Autenticación 2FA
│   │
│   ├── 📁 helpers/                     # Funciones auxiliares
│   │   └── 📄 funciones.js             # Utilidades varias
│   │
│   ├── 📁 middleware/                  # Middlewares de autenticación
│   │   ├── 📄 adminAuth.js             # Autorización de admin
│   │   ├── 📄 auth.js                  # Autenticación JWT
│   │   └── 📄 providerAuth.js          # Autorización de proveedor
│   │
│   └── 📁 routes/                      # Definición de endpoints
│       ├── 📄 admin.routes.js          # Rutas administrativas
│       ├── 📄 auth.routes.js           # Rutas de autenticación
│       └── 📄 product.routes.js        # Rutas de productos
│
└── 📁 tests/                           # Pruebas y testing
    ├── 📄 setup.js                     # Configuración global de tests
    ├── 📄 auth.test.js                 # Pruebas de autenticación
    ├── 📄 basic.test.js                # Pruebas básicas del sistema
    ├── 📄 performance.test.js          # Pruebas de rendimiento
    ├── 📄 products.test.js             # Pruebas de productos
    │
    └── 📁 helpers/                     # Utilidades para testing
        └── (archivos auxiliares para pruebas)            # Scripts de utilidad
```


### 4.2 APIs Principales

### **Resumen de Endpoints**

| **Categoría** | **Cantidad** | **Base URL** | **Descripción** |
|---------------|--------------|--------------|-----------------|
| 🔐 Autenticación | 4 endpoints | `/api/auth` | Login, registro, perfil, cambio de contraseña |
| 🔑 2FA | 4 endpoints | `/api/auth/2fa` | Configuración y verificación 2FA |
| 📦 Productos | 5 endpoints | `/api/products` | Catálogo público y gestión de proveedor |
| 👨‍💼 Admin | 3 endpoints | `/api/admin` | Gestión de usuarios y administradores |


### **API de Autenticación**

```http
POST /api/auth/register     # Registro (Usuario/Proveedor)
POST /api/auth/login        # Inicio de sesión
GET  /api/auth/me          # Perfil del usuario
POST /api/auth/change-password  # Cambiar contraseña
```


```javascript
// POST /api/auth/register - Registro de usuario/proveedor
{
  "name": "Juan Pérez",
  "email": "juan@example.com",
  "password": "password123",
  "role": "PROVEEDOR",
  "telefono": "12345678",
  "direccion": "Guatemala City",
  // Datos específicos para proveedores
  "nombreComercial": "Ferretería Central",
  "rfc": "RFC123456789",
  "latitud": 14.6349,
  "longitud": -90.5069
}

// POST /api/auth/login - Inicio de sesión
async function login(req, res) {
  const { email, password } = req.body;
  const user = await prisma.user.findUnique({
    where: { email },
    include: { proveedor: true }
  });

  if (!user) return res.status(401).json({ error: "Credenciales inválidas" });
  
  const ok = await bcrypt.compare(password, user.passwordHash);
  if (!ok) return res.status(401).json({ error: "Credenciales inválidas" });

  // Si 2FA está habilitado, retornar token temporal
  if (user.twoFactorEnabled) {
    const tmpToken = signTmpToken(user);
    return res.json({ requires2FA: true, tmpToken });
  }

  // Token JWT normal
  const token = jwt.sign(
    { id: user.id, email: user.email, role: user.role },
    process.env.JWT_SECRET,
    { expiresIn: "1d" }
  );

  res.json({ token, user, proveedor: user.proveedor });
}

// GET /api/auth/me - Perfil del usuario autenticado
// POST /api/auth/change-password - Cambiar contraseña
```

### **Autenticación 2FA**

```http
POST /api/auth/2fa/setup        # Configurar 2FA
POST /api/auth/2fa/enable       # Habilitar 2FA  
POST /api/auth/2fa/disable      # Deshabilitar 2FA
POST /api/auth/2fa/verify-login # Completar login con 2FA
```


```javascript
// POST /api/auth/2fa/setup - Configurar 2FA
async function setup2FA(req, res) {
  const userId = req.user.id;
  const user = await prisma.user.findUnique({ where: { id: userId } });

  if (user.twoFactorEnabled) {
    return res.status(400).json({ error: "2FA ya está habilitado" });
  }

  const secret = speakeasy.generateSecret({
    name: `UMG_PROYECT (${user.email})`,
    length: 20,
  });

  await prisma.user.update({
    where: { id: userId },
    data: { twoFactorTempSecret: secret.base32 },
  });

  const otpauth = secret.otpauth_url;
  const qrDataUrl = await qrcode.toDataURL(otpauth);

  return res.json({
    otpauthUrl: otpauth,
    qr: qrDataUrl,
    base32: secret.base32,
  });
}

// POST /api/auth/2fa/enable - Habilitar 2FA
// POST /api/auth/2fa/disable - Deshabilitar 2FA  
// POST /api/auth/2fa/verify-login - Completar login con código 2FA
```

### **Productos**

```http
GET  /api/products          # Lista pública de productos
GET  /api/products/:id      # Producto específico (autenticado)
POST /api/products          # Crear producto (solo proveedor)
PUT  /api/products/:id      # Actualizar producto (solo proveedor)
DELETE /api/products/:id    # Eliminar producto (solo proveedor)
```

```javascript
// GET /api/products - Lista pública con filtros
async function list(req, res) {
  const { proveedorId, categoria, busqueda } = req.query;
  const where = {
    activo: true,
    ...(proveedorId && { proveedorId: parseInt(proveedorId) }),
    ...(categoria && { categoria }),
    ...(busqueda && {
      OR: [
        { nombre: { contains: busqueda, mode: 'insensitive' } },
        { descripcion: { contains: busqueda, mode: 'insensitive' } }
      ]
    })
  };

  const productos = await prisma.producto.findMany({
    where,
    include: {
      proveedor: {
        select: {
          nombreComercial: true,
          latitud: true,
          longitud: true
        }
      }
    },
    orderBy: { id: "asc" }
  });
  res.json(productos);
}

// POST /api/products - Crear producto (solo proveedores)
async function create(req, res) {
  const { nombre, precio, stock, descripcion, categoria, imagenUrl } = req.body;

  const proveedor = await prisma.proveedor.findUnique({
    where: { userId: req.user.id }
  });

  if (!proveedor) {
    return res.status(403).json({ error: "Solo los proveedores pueden crear productos" });
  }

  const producto = await prisma.producto.create({
    data: {
      nombre, precio, stock: stock || 0, descripcion, categoria, imagenUrl,
      proveedorId: proveedor.id,
      activo: true
    }
  });
  res.status(201).json(producto);
}
// GET /api/products/:id - Producto específico (autenticado)
// PUT /api/products/:id - Actualizar producto (solo proveedor propietario)
// DELETE /api/products/:id - Desactivar producto (soft delete)
```

### **Administración**

```http
GET  /api/admin/users           # Listar usuarios (solo admin)
POST /api/admin/create-admin    # Crear administrador (solo admin)  
PUT  /api/admin/users/:id/status # Activar/desactivar usuario (solo admin)
```


```javascript
// GET /api/admin/users - Listar todos los usuarios
async function listUsers(req, res) {
  const users = await prisma.user.findMany({
    include: {
      proveedor: true
    }
  });

  const formattedUsers = users.map(user => ({
    id: user.id,
    name: user.name,
    email: user.email,
    role: user.role,
    telefono: user.telefono,
    direccion: user.direccion,
    createdAt: user.createdAt,
    ...(user.proveedor && {
      proveedor: {
        id: user.proveedor.id,
        nombreComercial: user.proveedor.nombreComercial,
        rfc: user.proveedor.rfc,
        latitud: user.proveedor.latitud,
        longitud: user.proveedor.longitud
      }
    })
  }));

  res.json(formattedUsers);
}

// POST /api/admin/create-admin - Crear nuevo administrador
async function createAdmin(req, res) {
  if (req.user.role !== 'ADMIN') {
    return res.status(403).json({ error: "No autorizado" });
  }

  const { name, email, password } = req.body;
  const passwordHash = await bcrypt.hash(password, 12);
  
  const admin = await prisma.user.create({
    data: { name, email, passwordHash, role: 'ADMIN' }
  });

  res.status(201).json({
    message: "Administrador creado exitosamente",
    admin: { id: admin.id, name: admin.name, email: admin.email, role: admin.role }
  });
}

// PUT /api/admin/users/:id/status - Activar/desactivar usuarios
```

#### 4.2.2 Documentación Swagger
![image](https://hackmd.io/_uploads/H1GVwxShxg.png)

![image](https://hackmd.io/_uploads/rJ2qDerngx.png)



### 4.3 Modelos de Datos
### **Entidades Principales**

#### **👤 User (Usuario Principal)**
```prisma
model User {
  id                Int       @id @default(autoincrement())
  name              String
  email             String    @unique
  password          String    // Hash con bcrypt
  role              Role      @default(USUARIO)
  telefono          String
  direccion         String
  twoFactorSecret   String?   // Para 2FA
  twoFactorEnabled  Boolean   @default(false)
  activo            Boolean   @default(true)
  createdAt         DateTime  @default(now())
  updatedAt         DateTime  @updatedAt
  
  proveedor         Proveedor?
}

enum Role {
  ADMIN
  PROVEEDOR
  USUARIO
}
```

#### **🏪 Proveedor (Datos Comerciales)**
```prisma
model Proveedor {
  id               Int       @id @default(autoincrement())
  nombreComercial  String
  rfc              String    @unique
  latitud          Decimal?  // Para geolocalización
  longitud         Decimal?
  activo           Boolean   @default(true)
  
  userId           Int       @unique
  user             User      @relation(fields: [userId], references: [id])
  productos        Producto[]
}
```

#### **📦 Producto (Catálogo)**
```prisma
model Producto {
  id           Int       @id @default(autoincrement())
  nombre       String
  descripcion  String
  precio       Decimal   @db.Decimal(10, 2)
  categoria    String
  stock        Int
  imagenUrl    String?
  activo       Boolean   @default(true)
  
  proveedorId  Int
  proveedor    Proveedor @relation(fields: [proveedorId], references: [id])
  
  @@index([categoria])
  @@index([proveedorId])
}
```

### **🔗 Relaciones**
- **User ↔ Proveedor**: Relación 1:1 (opcional)
- **Proveedor ↔ Producto**: Relación 1:N (un proveedor múltiples productos)

### **✨ Características del Modelo**
- ✅ **Roles diferenciados**: ADMIN, PROVEEDOR, USUARIO
- ✅ **Geolocalización**: Latitud/longitud para proveedores
- ✅ **2FA integrado**: Secret y habilitación en User
- ✅ **Soft delete**: Campo `activo` en lugar de eliminación física
- ✅ **Timestamps**: Campos automáticos de creación/actualización
- ✅ **Índices optimizados**: Para búsquedas frecuentes


### 4.6 Variables de Entorno
```bash
# Variables de entorno de ejemplo para Backend UMG
# Copia este archivo a .env y completa los valores

# Cadena de conexión a PostgreSQL
# Formato: postgresql://USUARIO:CONTRASENA@HOST:PUERTO/BASE?schema=public
DATABASE_URL="postgresql://USER:PASS@localhost:5432/DB_NAME?schema=public"

# Secreto para firmar JWT (cámbialo por uno fuerte en producción)
JWT_SECRET="cambia_esto_por_un_secreto_seguro"

# Puerto del servidor (opcional; por defecto 3000)
PORT=3000


```

---

## 5. DOCUMENTACIÓN DE FRONTEND

### 5.1 Arquitectura de UI

#### 5.1.1 Estructura de Componentes
```
ASII_project-master
├── .github
├── .husky
├── .next
├── .vscode
├── node_modules
├── public
└── src
    ├── app
    ├── componests
    ├── contexts
    ├── hooks
    ├── them
    └── utils




```

### 5.2 Componentes Principales

#### 5.2.1 Pruebas de interfaz (UI)
```jsx
//

Comprobé la alineación, tamaños, colores, fuentes y estilos de los elementos en pantalla, validando que se ajustaran al diseño esperado.

Revisé la responsividad de la aplicación, verificando cómo se adapta en diferentes resoluciones: desktop, tablet y móvil.

Validé la accesibilidad básica, asegurándome de que hubiera buen contraste de colores, etiquetas visibles en los inputs y que los textos fueran legibles.

![imagen_2025-09-26_192300897](https://hackmd.io/_uploads/HykN33Ehge.png)


```

#### 5.2.2 Productos cerca de ti
```jsx
//

El mapa de ubicación no funciona correctamente.

Al intentar mostrar la posición actual del usuario, no se carga la ubicación ni se marca en el mapa.

Esto impide que la funcionalidad de geolocalización cumpla su propósito en la aplicación.


![image](https://hackmd.io/_uploads/SyEXg6Vneg.png)


```

### 5.3 Validación de creación de cuentas:
```javascript
Probé la funcionalidad de crear nuevas cuentas y funciona correctamente.

Los datos se aceptan según los campos requeridos, las validaciones de inputs se muestran correctamente y el usuario puede completar el registro sin errores.

```

### 5.4 Botón de búsqueda:
```javascript
Detecté un bug en el botón de búsqueda: al presionarlo, el resultado esperado no se muestra correctamente y el botón parece quedarse en la misma posición sin realizar la acción.

Esto afecta la experiencia del usuario, ya que no se puede buscar correctamente.


![image](https://hackmd.io/_uploads/S17UWaEhee.png)

```

### 5.5 Guía de Estilos UI/UX

#### 5.5.1 Paleta de Colores
```css
:root {
    --primary-color: #2563eb;      /* Azul principal */
    --secondary-color: #64748b;    /* Gris secundario */
    --success-color: #059669;      /* Verde éxito */
    --warning-color: #d97706;      /* Naranja advertencia */
    --error-color: #dc2626;        /* Rojo error */
    --background-color: #f8fafc;   /* Fondo principal */
    --text-primary: #1e293b;       /* Texto principal */
    --text-secondary: #64748b;     /* Texto secundario */
}
```

#### 5.5.2 Componentes de Diseño
```css
/* Botones */
.btn-primary {
    background-color: var(--primary-color);
    color: white;
    padding: 0.5rem 1rem;
    border-radius: 0.375rem;
    border: none;
    cursor: pointer;
    transition: all 0.2s;
}

.btn-primary:hover {
    background-color: #1d4ed8;
    transform: translateY(-1px);
}

/* Cards */
.card {
    background: white;
    border-radius: 0.5rem;
    box-shadow: 0 1px 3px rgba(0, 0, 0, 0.1);
    padding: 1.5rem;
    margin-bottom: 1rem;
}
```

### 5.6 Responsive Design
```css
/* Mobile First Approach */
.product-grid {
    display: grid;
    grid-template-columns: 1fr;
    gap: 1rem;
}

@media (min-width: 768px) {
    .product-grid {
        grid-template-columns: repeat(2, 1fr);
    }
}

@media (min-width: 1024px) {
    .product-grid {
        grid-template-columns: repeat(3, 1fr);
    }
}

@media (min-width: 1280px) {
    .product-grid {
        grid-template-columns: repeat(4, 1fr);
    }
}
```

### 5.7 Flujos de Usuario Principales

#### 5.7.1 Flujo de Venta
1. **Selección de Cliente:** Buscar o crear cliente
2. **Agregar Productos:** Buscar y agregar productos al carrito
3. **Calcular Total:** Aplicar descuentos e impuestos
4. **Procesar Pago:** Seleccionar método de pago
5. **Generar Factura:** Crear documento fiscal
6. **Actualizar Inventario:** Reducir stock automáticamente

#### 5.7.2 Flujo de Gestión de Inventario
1. **Ver Inventario Actual:** Lista con filtros y búsqueda
2. **Agregar Producto:** Formulario con validaciones
3. **Actualizar Stock:** Entrada/salida de mercancía
4. **Alertas de Stock Bajo:** Notificaciones automáticas
5. **Reportes:** Generación de reportes de inventario

---

## 6. DOCUMENTACIÓN DE INTEGRACIÓN

### 6.1 Comunicación Frontend-Backend

#### 6.1.1 Interceptores HTTP
```javascript
// utils/httpClient.js
import axios from 'axios';

const httpClient = axios.create({
    baseURL: process.env.REACT_APP_API_URL,
    timeout: 10000
});

// Request interceptor
httpClient.interceptors.request.use(
    (config) => {
        const token = localStorage.getItem('token');
        if (token) {
            config.headers.Authorization = `Bearer ${token}`;
        }
        return config;
    },
    (error) => Promise.reject(error)
);

// Response interceptor
httpClient.interceptors.response.use(
    (response) => response,
    (error) => {
        if (error.response?.status === 401) {
            // Redirect to login
            window.location.href = '/login';
        }
        return Promise.reject(error);
    }
);
```

### 6.2 APIs Externas

#### 6.2.1 Integración con SUNAT
```javascript
// services/sunatService.js
class SunatService {
    async validateRUC(ruc) {
        try {
            const response = await fetch(`https://api.sunat.gob.pe/v1/ruc/${ruc}`);
            return response.json();
        } catch (error) {
            throw new Error('Error validating RUC');
        }
    }

    async sendInvoice(invoiceData) {
        // Envío de factura electrónica a SUNAT
        const xmlData = this.generateXML(invoiceData);
        // Implementar envío
    }
}
```

#### 6.2.2 Integración con Proveedores
```javascript
// services/supplierIntegration.js
class SupplierIntegration {
    async syncCatalog(supplierId) {
        // Sincronizar catálogo de productos del proveedor
        const catalog = await this.fetchSupplierCatalog(supplierId);
        await this.updateLocalCatalog(catalog);
    }

    async checkPriceUpdates() {
        // Verificar actualizaciones de precios
    }
}
```

### 6.3 WebSockets para Tiempo Real
```javascript
// services/websocketService.js
class WebSocketService {
    constructor() {
        this.socket = null;
    }

    connect() {
        this.socket = new WebSocket(process.env.REACT_APP_WS_URL);
        
        this.socket.onmessage = (event) => {
            const data = JSON.parse(event.data);
            this.handleMessage(data);
        };
    }

    handleMessage(data) {
        switch (data.type) {
            case 'STOCK_UPDATE':
                // Actualizar stock en tiempo real
                break;
            case 'NEW_SALE':
                // Notificar nueva venta
                break;
        }
    }
}
```

---

## 7. DOCUMENTACIÓN DE DESPLIEGUE

### 7.1 Ambientes

#### 7.1.1 Desarrollo (Development)
- **URL:** localhost
- **Base de Datos:** PostgreSQL local
- **Propósito:** Desarrollo y pruebas iniciales

#### 7.1.2 Pruebas (Staging)
- **URL:** https://autorepuestos-gt.onrender.com/
- **Base de Datos:** PostgreSQL en AWS RDS
- **Propósito:** Testing de integración y UAT

#### 7.1.3 Producción (Production)
- **URL:** https://autorepuestos-gt.onrender.com/
- **Base de Datos:** PostgreSQL en AWS RDS (Multi-AZ)
- **Propósito:** Sistema en vivo

### 7.2 Configuración de Infraestructura

#### 7.2.1 Docker Configuration
```dockerfile
# Dockerfile para Backend
# Multi-stage build para optimizar el tamaño de la imagen
FROM node:18-alpine AS base

# Instalar dependencias del sistema necesarias para Prisma
RUN apk add --no-cache openssl

# Crear directorio de trabajo
WORKDIR /app

# Copiar archivos de dependencias
COPY package*.json ./
COPY prisma ./prisma/

# Instalar dependencias
RUN npm ci --only=production && npm cache clean --force

# Generar cliente de Prisma
RUN npx prisma generate

# Etapa de producción
FROM node:18-alpine AS production

# Instalar dependencias del sistema
RUN apk add --no-cache openssl dumb-init

# Crear usuario no-root para seguridad
RUN addgroup -g 1001 -S nodejs
RUN adduser -S nodejs -u 1001

# Crear directorio de trabajo
WORKDIR /app

# Copiar dependencias desde la etapa base
COPY --from=base /app/node_modules ./node_modules
COPY --from=base /app/generated ./generated

# Copiar código fuente
COPY --chown=nodejs:nodejs . .

# Cambiar al usuario no-root
USER nodejs

# Exponer puerto
EXPOSE 3000

# Comando de inicio con dumb-init para manejo correcto de señales
ENTRYPOINT ["dumb-init", "--"]
CMD ["npm", "start"]
```

```dockerfile
# Dockerfile para Frontend
FROM node:18-alpine as builder

WORKDIR /app
COPY package*.json ./
RUN npm ci

COPY . .
RUN npm run build

FROM nginx:alpine
COPY --from=builder /app/build /usr/share/nginx/html
COPY nginx.conf /etc/nginx/nginx.conf

EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]
```

#### 7.2.2 Docker Compose
```yaml
# docker-compose.yml
version: '3.8'

services:
  # Servicio de PostgreSQL
  postgres:
    image: postgres:15-alpine
    container_name: backend-umg-postgres
    restart: unless-stopped
    environment:
      POSTGRES_DB: backend_umg
      POSTGRES_USER: postgres
      POSTGRES_PASSWORD: postgres123
    ports:
      - "5433:5432"
    volumes:
      - postgres_data:/var/lib/postgresql/data
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U postgres -d backend_umg"]
      interval: 10s
      timeout: 5s
      retries: 5
    networks:
      - backend-network

  # Servicio del Backend
  backend:
    build:
      context: .
      dockerfile: dockerfile
    container_name: backend-umg-api
    restart: unless-stopped
    depends_on:
      postgres:
        condition: service_healthy
    environment:
      DATABASE_URL: "postgresql://postgres:postgres123@postgres:5432/backend_umg?schema=public"
      JWT_SECRET: "tu_jwt_secreto_super_seguro_cambialo_en_produccion"
      PORT: 4000
      NODE_ENV: production
    ports:
      - "4000:4000"
    volumes:
      # Para desarrollo, puedes descomentar esta línea para hot reload
      # - .:/app
      # - /app/node_modules
      - ./prisma:/app/prisma
    networks:
      - backend-network
    command: >
      sh -c "
        echo 'Esperando a que PostgreSQL esté listo...' &&
        npx prisma migrate deploy &&
        echo 'Migraciones aplicadas exitosamente' &&
        npm start
      "

  # Servicio opcional para administración de PostgreSQL
  pgadmin:
    image: dpage/pgadmin4:latest
    container_name: backend-umg-pgadmin
    restart: unless-stopped
    environment:
      PGADMIN_DEFAULT_EMAIL: admin@admin.com
      PGADMIN_DEFAULT_PASSWORD: admin123
    ports:
      - "8080:80"
    depends_on:
      - postgres
    networks:
      - backend-network
    # profiles:
    #   - tools

# Volúmenes para persistencia de datos
volumes:
  postgres_data:
    driver: local

# Red para comunicación entre contenedores
networks:
  backend-network:
    driver: bridge
```

---

## 8. DOCUMENTACIÓN DE TESTING

### 8.1 PRUEBAS UNITARIAS E INTEGRACIÓN

### 8.1.1 Testing BACKEND

### **Framework utilizado**
```json
{
  "jest": "^30.1.3",
  "supertest": "^7.1.4",
  "@types/jest": "^30.0.0"
}
```

### **Configuración Jest**
```javascript
// jest.config.js
module.exports = {
  testEnvironment: 'node',
  coverageDirectory: 'coverage',
  testMatch: [
    '**/__tests__/**/*.test.js',
    '**/?(*.)+(spec|test).js'
  ],
  collectCoverageFrom: [
    'src/**/*.js',
    '!src/server.js',
    '!src/swagger.js',
    '!src/config/prisma.js'
  ],
  setupFilesAfterEnv: ['<rootDir>/tests/setup.js']
};
```

### 8.1.2 EVIDENCIA CON JEST/SUPERTEST
### **1. Configuración de Pruebas**

#### **Setup inicial (tests/setup.js)**
```javascript
const { prisma } = require('../src/config/prisma');

beforeAll(async () => {
  // Conectar a base de datos de pruebas
  await prisma.$connect();
});

afterAll(async () => {
  // Limpiar y desconectar
  await prisma.$disconnect();
});

// Helper para limpiar datos entre pruebas
global.cleanupDatabase = async () => {
  await prisma.producto.deleteMany({});
  await prisma.proveedor.deleteMany({});
  await prisma.user.deleteMany({
    where: { email: { not: 'admin@sistema.com' } }
  });
};
```


### **2. Pruebas de Autenticación**

#### **Registro de usuarios (auth.test.js)**
```javascript
const request = require('supertest');
const app = require('../src/app');

describe('POST /api/auth/register', () => {
  beforeEach(async () => {
    await global.cleanupDatabase();
  });

  test('✅ Debe registrar un nuevo usuario básico', async () => {
    const userData = {
      name: 'Test User',
      email: 'test@example.com',
      password: 'password123',
      role: 'USUARIO',
      telefono: '12345678',
      direccion: 'Ciudad de Guatemala'
    };

    const response = await request(app)
      .post('/api/auth/register')
      .send(userData)
      .expect(201);

    expect(response.body.token).toBeDefined();
    expect(response.body.user.email).toBe(userData.email);
    expect(response.body.user.role).toBe('USUARIO');
    expect(response.body.proveedor).toBeUndefined();
  });

  test('✅ Debe registrar un nuevo proveedor con datos completos', async () => {
    const proveedorData = {
      name: 'Comercial Test',
      email: 'proveedor@test.com',
      password: 'password123',
      role: 'PROVEEDOR',
      telefono: '87654321',
      direccion: 'Zona 10, Guatemala',
      // Datos específicos de proveedor
      nombreComercial: 'Comercial XYZ',
      rfc: 'RFC123456789',
      latitud: 14.6349,
      longitud: -90.5069
    };

    const response = await request(app)
      .post('/api/auth/register')
      .send(proveedorData)
      .expect(201);

    expect(response.body.token).toBeDefined();
    expect(response.body.user.role).toBe('PROVEEDOR');
    expect(response.body.proveedor).toBeDefined();
    expect(response.body.proveedor.nombreComercial).toBe(proveedorData.nombreComercial);
  });

  test('❌ Debe fallar con email duplicado', async () => {
    const userData = {
      name: 'Test',
      email: 'test@example.com',
      password: 'password123'
    };

    // Primer registro
    await request(app)
      .post('/api/auth/register')
      .send(userData)
      .expect(201);

    // Segundo registro (debería fallar)
    const response = await request(app)
      .post('/api/auth/register')
      .send(userData)
      .expect(409);

    expect(response.body.error).toContain('ya está registrado');
  });

  test('❌ Debe fallar sin datos de proveedor requeridos', async () => {
    const response = await request(app)
      .post('/api/auth/register')
      .send({
        name: 'Test',
        email: 'proveedor@test.com',
        password: 'password123',
        role: 'PROVEEDOR'
        // Faltan: nombreComercial, latitud, longitud
      })
      .expect(400);

    expect(response.body.error).toContain('nombreComercial');
  });
});

describe('POST /api/auth/login', () => {
  test('✅ Debe hacer login correctamente', async () => {
    const response = await request(app)
      .post('/api/auth/login')
      .send({
        email: 'admin@sistema.com',
        password: 'admin123'
      })
      .expect(200);

    expect(response.body.token).toBeDefined();
    expect(response.body.user.role).toBe('ADMIN');
  });

  test('✅ Debe requerir 2FA si está habilitado', async () => {
    // Primero habilitamos 2FA para un usuario de prueba
    // (este test requeriría setup adicional)
    
    const response = await request(app)
      .post('/api/auth/login')
      .send({
        email: 'user_with_2fa@test.com',
        password: 'password123'
      })
      .expect(200);

    expect(response.body.requires2FA).toBe(true);
    expect(response.body.tmpToken).toBeDefined();
  });
});
```

### **3. Pruebas de Productos (products.test.js)**

```javascript
describe('Products Management', () => {
  let proveedorToken;
  let usuarioToken;
  let proveedorId;

  beforeAll(async () => {
    // Crear proveedor
    const proveedorResponse = await request(app)
      .post('/api/auth/register')
      .send({
        name: 'Proveedor Test',
        email: 'proveedor@test.com',
        password: 'password123',
        role: 'PROVEEDOR',
        telefono: '12345678',
        direccion: 'Guatemala',
        nombreComercial: 'Test Store',
        rfc: 'RFC123',
        latitud: 14.6349,
        longitud: -90.5069
      });
    
    proveedorToken = proveedorResponse.body.token;
    proveedorId = proveedorResponse.body.proveedor.id;

    // Crear usuario normal
    const usuarioResponse = await request(app)
      .post('/api/auth/register')
      .send({
        name: 'Usuario Test',
        email: 'usuario@test.com',
        password: 'password123'
      });
    
    usuarioToken = usuarioResponse.body.token;
  });

  describe('GET /api/products (Público)', () => {
    test('✅ Debe obtener lista de productos sin autenticación', async () => {
      const response = await request(app)
        .get('/api/products')
        .expect(200);

      expect(Array.isArray(response.body)).toBe(true);
    });

    test('✅ Debe filtrar por proveedor', async () => {
      const response = await request(app)
        .get(`/api/products?proveedorId=${proveedorId}`)
        .expect(200);

      expect(Array.isArray(response.body)).toBe(true);
    });

    test('✅ Debe filtrar por categoría', async () => {
      const response = await request(app)
        .get('/api/products?categoria=herramientas')
        .expect(200);

      expect(Array.isArray(response.body)).toBe(true);
    });
  });

  describe('POST /api/products (Solo Proveedores)', () => {
    test('✅ Proveedor debe poder crear producto', async () => {
      const productData = {
        nombre: 'Llave inglesa 12"',
        descripcion: 'Llave inglesa profesional',
        precio: 25.50,
        categoria: 'herramientas',
        stock: 10,
        imagenUrl: 'https://example.com/image.jpg'
      };

      const response = await request(app)
        .post('/api/products')
        .set('Authorization', `Bearer ${proveedorToken}`)
        .send(productData)
        .expect(201);

      expect(response.body.nombre).toBe(productData.nombre);
      expect(parseFloat(response.body.precio)).toBe(productData.precio);
      expect(response.body.proveedorId).toBe(proveedorId);
    });

    test('❌ Usuario normal no debe poder crear producto', async () => {
      const response = await request(app)
        .post('/api/products')
        .set('Authorization', `Bearer ${usuarioToken}`)
        .send({
          nombre: 'Test',
          precio: 10,
          categoria: 'test'
        })
        .expect(403);

      expect(response.body.error).toContain('proveedor');
    });

    test('❌ Debe fallar sin campos requeridos', async () => {
      const response = await request(app)
        .post('/api/products')
        .set('Authorization', `Bearer ${proveedorToken}`)
        .send({
          // Falta nombre, precio, categoria
          descripcion: 'Test'
        })
        .expect(400);

      expect(response.body.error).toContain('requeridos');
    });
  });
});
```

### **4. Pruebas de Rendimiento (performance.test.js)**

```javascript
const request = require('supertest');

const BASE_URL = 'http://localhost:4000';

describe('⚡ Pruebas de Rendimiento', () => {

  test('✅ GET /api/products debe responder en menos de 2 segundos', async () => {
    const startTime = Date.now();
    
    const response = await request(BASE_URL)
      .get('/api/products')
      .expect(200);
    
    const duration = Date.now() - startTime;
    
    console.log(`⏱️ Tiempo de respuesta: ${duration}ms`);
    expect(duration).toBeLessThan(2000);
  });

  test('✅ POST /api/auth/login debe responder en menos de 2 segundos', async () => {
    const startTime = Date.now();
    
    const response = await request(BASE_URL)
      .post('/api/auth/login')
      .send({
        email: 'admin@sistema.com',
        password: 'admin123'
      })
      .expect(200);
    
    const duration = Date.now() - startTime;
    
    console.log(`⏱️ Tiempo de login: ${duration}ms`);
    expect(duration).toBeLessThan(2000);
  });

  test('✅ Múltiples requests deben mantener buen rendimiento', async () => {
    const requests = [];
    const numRequests = 5;

    for (let i = 0; i < numRequests; i++) {
      requests.push(
        request(BASE_URL)
          .get('/api/products')
          .expect(200)
      );
    }

    const startTime = Date.now();
    await Promise.all(requests);
    const totalDuration = Date.now() - startTime;

    console.log(`⚡ ${numRequests} requests paralelos: ${totalDuration}ms`);
    expect(totalDuration).toBeLessThan(5000);
  });
});
```


### 8.3 Automatización de Testing
```yaml
// Setup global para las pruebas
const { PrismaClient } = require('@prisma/client');

// Configuración para pruebas
process.env.NODE_ENV = 'test';
process.env.JWT_SECRET = 'test-jwt-secret-super-secure';

// Aumentar timeout para pruebas que involucran API
jest.setTimeout(10000);

// Configuración global de console para debugging
global.console = {
  ...console,
  log: jest.fn(),
  debug: jest.fn(),
  info: jest.fn(),
  warn: jest.fn(),
  error: jest.fn(),
};

// Setup que se ejecuta antes de todas las pruebas
beforeAll(async () => {
  console.log('Iniciando setup de pruebas...');
});

// Cleanup que se ejecuta después de todas las pruebas
afterAll(async () => {
  console.log('✅ Pruebas finalizadas');
});
```
---
![image](https://hackmd.io/_uploads/ByFz1-H3ll.png)
![image](https://hackmd.io/_uploads/BJnE1bH2ll.png)
![image](https://hackmd.io/_uploads/rk9SJbHneg.png)

---


## COLECCIÓN DE PRUEBAS POSTMAN
[https://Coleccion en Postman ](https://blue-space-779097.postman.co/workspace/My-Workspace~42d7317f-2e37-4e89-92cd-140e454a01fc/collection/18052337-121bc78c-55c5-423b-8741-1b866afe64f8?action=share&creator=18052337&active-environment=18052337-32d704a4-c088-4cbd-9574-c3bd70cb9442)
![image](https://hackmd.io/_uploads/SJMy-ZHngg.png)


### **Configuración del Environment**
```json
{
  "name": "Backend UMG Local",
  "values": [
    {
      "key": "base_url",
      "value": "http://localhost:3000",
      "enabled": true
    },
    {
      "key": "admin_email", 
      "value": "admin@sistema.com",
      "enabled": true
    },
    {
      "key": "admin_password",
      "value": "admin123",
      "enabled": true
    },
    {
      "key": "auth_token",
      "value": "",
      "enabled": true
    },
    {
      "key": "tmp_token",
      "value": "",
      "enabled": true
    },
    {
      "key": "provider_token",
      "value": "",
      "enabled": true
    }
  ]
}
```

##  RESULTADOS Y CONCLUSIONES

### **Comandos para Ejecutar Pruebas**

```bash
# Instalar dependencias de testing
npm install --save-dev jest supertest @types/jest

# Crear archivo de configuración Jest
echo 'module.exports = { testEnvironment: "node" };' > jest.config.js

# Ejecutar todas las pruebas
npm test

# Ejecutar con cobertura
npm test -- --coverage

# Ejecutar pruebas específicas
npm test -- --testNamePattern="auth"
```

## 9. DOCUMENTACIÓN DE MANTENIMIENTO

### 9.1 Troubleshooting Guide

#### 9.1.1 Problemas Comunes de Base de Datos
| Problema | Síntomas | Solución |
|----------|----------|----------|
| **Conexión lenta** | Consultas tardan >5s | Revisar índices, optimizar queries |
| **Deadlocks** | Errores de transacción | Revisar orden de locks, reducir tiempo de transacción |
| **Espacio en disco** | Error "disk full" | Limpiar logs, archivar datos antiguos |
| **Backup fallido** | Logs de error en backup | Verificar permisos, espacio disponible |

#### 9.1.2 Problemas de Backend
| Problema | Síntomas | Solución |
|----------|----------|----------|
| **Memory leak** | Uso de RAM creciente | Revisar event listeners, cerrar conexiones |
| **API timeout** | Respuestas lentas | Optimizar queries, implementar cache |
| **401 Unauthorized** | Errores de autenticación | Verificar JWT secret, renovar tokens |
| **500 Internal Error** | Errores del servidor | Revisar logs, validar datos de entrada |

#### 9.1.3 Problemas de Frontend
| Problema | Síntomas | Solución |
|----------|----------|----------|
| **Página en blanco** | White screen of death | Revisar console errors, bundle size |
| **Carga lenta** | Performance issues | Optimizar imágenes, code splitting |
| **Formularios no envían** | Submit no funciona | Validar network requests, CORS |
| **Datos desactualizados** | Cache stale | Implementar invalidación de cache |

### 9.2 Procedimientos de Actualización

#### 9.2.1 Actualización de Base de Datos
```sql
-- migrations/001_add_product_variants.sql
-- Migración: Agregar tabla de variantes de productos
CREATE TABLE product_variants (
    id SERIAL PRIMARY KEY,
    product_id INTEGER REFERENCES products(id),
    variant_name VARCHAR(100) NOT NULL,
    sku VARCHAR(50) UNIQUE NOT NULL,
    price DECIMAL(10,2) NOT NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Rollback script
-- DROP TABLE product_variants;
```

#### 9.2.2 Actualización de Backend
```bash
#!/bin/bash
# scripts/deploy-backend.sh

echo "Starting backend deployment..."

# 1. Backup current version
cp -r /app/current /app/backup-$(date +%Y%m%d-%H%M%S)

# 2. Stop application
pm2 stop autopartes-api

# 3. Update code
git pull origin main
npm ci --production

# 4. Run migrations
npm run migrate

# 5. Start application
pm2 start autopartes-api

# 6. Health check
sleep 10
curl -f http://localhost:3000/health || {
    echo "Health check failed, rolling back..."
    pm2 stop autopartes-api
    cp -r /app/backup-* /app/current
    pm2 start autopartes-api
    exit 1
}

echo "Deployment completed successfully"
```

#### 9.2.3 Actualización de Frontend
```bash
#!/bin/bash
# scripts/deploy-frontend.sh

echo "Starting frontend deployment..."

# 1. Build new version
npm run build

# 2. Backup current version
cp -r /var/www/html /var/www/backup-$(date +%Y%m%d-%H%M%S)

# 3. Deploy new version
cp -r build/* /var/www/html/

# 4. Clear CDN cache (if applicable)
aws cloudfront create-invalidation --distribution-id $CDN_ID --paths "/*"

echo "Frontend deployment completed"
```

### 9.3 Monitoreo y Alertas

#### 9.3.1 Métricas Clave
```javascript
// monitoring/metrics.js
const metrics = {
    // Performance metrics
    responseTime: 'avg_response_time_ms',
    throughput: 'requests_per_second',
    errorRate: 'error_rate_percentage',
    
    // Business metrics
    dailySales: 'daily_sales_count',
    inventoryTurnover: 'inventory_turnover_rate',
    customerSatisfaction: 'customer_satisfaction_score',
    
    // System metrics
    cpuUsage: 'cpu_usage_percentage',
    memoryUsage: 'memory_usage_percentage',
    diskSpace: 'disk_usage_percentage',
    databaseConnections: 'active_db_connections'
};
```

#### 9.3.2 Configuración de Alertas
```yaml
# alerting/rules.yml
groups:
  - name: autopartes-alerts
    rules:
      - alert: HighErrorRate
        expr: error_rate > 0.05
        for: 5m
        annotations:
          summary: "High error rate detected"
          description: "Error rate is {{ $value }}%"

      - alert: DatabaseDown
        expr: up{job="postgres"} == 0
        for: 1m
        annotations:
          summary: "Database is down"

      - alert: LowStock
        expr: products_low_stock > 10
        for: 15m
        annotations:
          summary: "Multiple products with low stock"
```

### 9.4 Responsables y Contactos

#### 9.4.1 Equipo de Soporte
| Rol | Nombre | Email | Teléfono | Responsabilidades |
|-----|--------|-------|----------|-------------------|
| **Tech Lead** | [Nombre] | tech.lead@company.com | +51-xxx-xxxx | Arquitectura, decisiones técnicas |
| **DevOps** | [Nombre] | devops@company.com | +51-xxx-xxxx | Infraestructura, despliegues |
| **DBA** | [Nombre] | dba@company.com | +51-xxx-xxxx | Base de datos, backups |
| **Frontend Lead** | [Nombre] | frontend@company.com | +51-xxx-xxxx | UI/UX, componentes |
| **Backend Lead** | [Nombre] | backend@company.com | +51-xxx-xxxx | APIs, lógica de negocio |

#### 9.4.2 Escalamiento de Incidentes
1. **Nivel 1 (Desarrollador):** Problemas menores, bugs conocidos
2. **Nivel 2 (Team Lead):** Problemas de integración, performance
3. **Nivel 3 (Arquitecto):** Problemas de arquitectura, decisiones críticas
4. **Nivel 4 (Management):** Problemas que afectan el negocio

### 9.5 Backup y Recuperación

#### 9.5.1 Procedimiento de Backup
```bash
#!/bin/bash
# scripts/backup.sh

DATE=$(date +%Y%m%d_%H%M%S)
BACKUP_DIR="/backups/$DATE"

# Create backup directory
mkdir -p $BACKUP_DIR

# Database backup
pg_dump -h $DB_HOST -U $DB_USER $DB_NAME > $BACKUP_DIR/database.sql

# Files backup
tar -czf $BACKUP_DIR/files.tar.gz /app/uploads

# Configuration backup
cp -r /app/config $BACKUP_DIR/

# Upload to S3
aws s3 sync $BACKUP_DIR s3://autopartes-backups/$DATE/

echo "Backup completed: $BACKUP_DIR"
```

#### 9.5.2 Procedimiento de Recuperación
```bash
#!/bin/bash
# scripts/restore.sh

BACKUP_DATE=$1

if [ -z "$BACKUP_DATE" ]; then
    echo "Usage: $0 <backup_date>"
    exit 1
fi

# Download from S3
aws s3 sync s3://autopartes-backups/$BACKUP_DATE/ /tmp/restore/

# Stop services
pm2 stop all

# Restore database
psql -h $DB_HOST -U $DB_USER -d $DB_NAME < /tmp/restore/database.sql

# Restore files
tar -xzf /tmp/restore/files.tar.gz -C /

# Restore configuration
cp -r /tmp/restore/config/* /app/config/

# Start services
pm2 start all

echo "Restore completed from backup: $BACKUP_DATE"
```

---

## 10. ANEXOS

### 10.1 Glosario de Términos
- **SKU:** Stock Keeping Unit - Código único de producto
- **API:** Application Programming Interface
- **CRUD:** Create, Read, Update, Delete
- **JWT:** JSON Web Token
- **RBAC:** Role-Based Access Control
- **CI/CD:** Continuous Integration/Continuous Deployment
- **ORM:** Object-Relational Mapping
- **SPA:** Single Page Application
- **REST:** Representational State Transfer
- **CORS:** Cross-Origin Resource Sharing

### 10.2 Referencias y Enlaces
- [Documentación de React](https://reactjs.org/docs)
- [Node.js Best Practices](https://github.com/goldbergyoni/nodebestpractices)
- [PostgreSQL Documentation](https://www.postgresql.org/docs/)
- [Docker Documentation](https://docs.docker.com/)
- [AWS Documentation](https://docs.aws.amazon.com/)

### 10.3 Configuraciones de Ejemplo

#### 10.3.1 Nginx Configuration
```nginx
# nginx.conf
server {
    listen 80;
    server_name autopartes.com;
    
    # Frontend
    location / {
        root /var/www/html;
        try_files $uri $uri/ /index.html;
    }
    
    # API
    location /api/ {
        proxy_pass http://backend:3000;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    }
    
    # Static files
    location /uploads/ {
        root /var/www;
        expires 1y;
        add_header Cache-Control "public, immutable";
    }
}
```

#### 10.3.2 PM2 Configuration
```json
{
  "apps": [{
    "name": "autopartes-api",
    "script": "./src/server.js",
    "instances": "max",
    "exec_mode": "cluster",
    "env": {
      "NODE_ENV": "production",
      "PORT": 3000
    },
    "error_file": "./logs/err.log",
    "out_file": "./logs/out.log",
    "log_file": "./logs/combined.log"
  }]
}
```

### 10.4 Checklist de Deployment
- [ ] Tests unitarios pasando
- [ ] Tests de integración pasando
- [ ] Tests E2E pasando
- [ ] Backup de base de datos realizado
- [ ] Variables de entorno configuradas
- [ ] Certificados SSL válidos
- [ ] Monitoreo configurado
- [ ] Alertas configuradas
- [ ] Documentación actualizada
- [ ] Equipo notificado del deployment

---

## 📞 CONTACTO Y SOPORTE

Para consultas sobre esta documentación o el sistema:

- **Email de Soporte:** soporte@autopartes.com
- **Slack Channel:** #autopartes-dev
- **Repositorio:** https://github.com/company/autopartes-system
- **Wiki:** https://wiki.company.com/autopartes

---

*Documento generado el [FECHA] - Versión [X.X]*
*© 2024 [Nombre de la Empresa] - Todos los derechos reservados*
