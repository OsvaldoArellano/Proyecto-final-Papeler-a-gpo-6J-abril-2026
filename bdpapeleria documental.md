Te voy a dar un ejemplo claro y bien estructurado usando MongoDB, que es ideal para una papelería porque trabaja con **colecciones y documentos (JSON)**.

---

# 📦 1. Base de datos

Nombre de la base de datos:

```
papeleriaDB
```

---

# 📁 2. Colecciones y documentos

## 🧾 Colección: `productos`

Ejemplo de documento:

```json
{
  "_id": "P001",
  "nombre": "Cuaderno profesional",
  "categoria": "Útiles escolares",
  "precio": 85.50,
  "stock": 120,
  "marca": "Scribe",
  "descripcion": "Cuaderno de 100 hojas raya",
  "fecha_ingreso": "2026-04-20",
  "activo": true
}
```

### 🔎 Atributos y tipos:

* `_id`: String
* `nombre`: String
* `categoria`: String
* `precio`: Decimal
* `stock`: Int
* `marca`: String
* `descripcion`: String
* `fecha_ingreso`: Date
* `activo`: Boolean

---

## 👤 Colección: `clientes`

Ejemplo:

```json
{
  "_id": "C001",
  "nombre": "Juan",
  "apellido": "Pérez",
  "telefono": "6561234567",
  "correo": "juan@email.com",
  "direccion": "Col. Centro, Juárez",
  "fecha_registro": "2026-04-10"
}
```

### 🔎 Tipos:

* `_id`: String
* `nombre`: String
* `apellido`: String
* `telefono`: String
* `correo`: String
* `direccion`: String
* `fecha_registro`: Date

---

## 🛒 Colección: `ventas`

Ejemplo:

```json
{
  "_id": "V001",
  "cliente_id": "C001",
  "fecha": "2026-04-25",
  "total": 150.00,
  "metodo_pago": "efectivo",
  "productos": [
    {
      "producto_id": "P001",
      "cantidad": 2,
      "precio_unitario": 75.00
    }
  ]
}
```

### 🔎 Tipos:

* `_id`: String
* `cliente_id`: String (relación)
* `fecha`: Date
* `total`: Decimal
* `metodo_pago`: String
* `productos`: Array de objetos

  * `producto_id`: String
  * `cantidad`: Int
  * `precio_unitario`: Decimal

---

## 📦 Colección: `proveedores`

Ejemplo:

```json
{
  "_id": "PR001",
  "nombre": "Distribuidora Escolar SA",
  "telefono": "6569876543",
  "correo": "ventas@distribuidora.com",
  "direccion": "Av. Tecnológico, Juárez",
  "productos_surtidos": ["Cuadernos", "Plumas", "Colores"]
}
```

### 🔎 Tipos:

* `_id`: String
* `nombre`: String
* `telefono`: String
* `correo`: String
* `direccion`: String
* `productos_surtidos`: Array de Strings

---

## 📊 Colección: `categorias`

Ejemplo:

```json
{
  "_id": "CAT001",
  "nombre": "Arte y dibujo",
  "descripcion": "Materiales para dibujo y pintura",
  "activo": true
}
```

### 🔎 Tipos:

* `_id`: String
* `nombre`: String
* `descripcion`: String
* `activo`: Boolean

---

# 🔗 Relaciones (importante)

Aunque MongoDB no usa relaciones como SQL, aquí se simulan con IDs:

* `ventas.cliente_id` → clientes
* `ventas.productos.producto_id` → productos

---

# 💡 Extra (mejora recomendada)

Podrías agregar:

* `usuarios` (empleados/admin)
* `inventario_movimientos` (entradas/salidas)
* `promociones`

---

Si quieres, puedo ayudarte a:

* Convertir esto a **SQL (MySQL/PostgreSQL)**
* Crear el **script para insertarlo automáticamente**
* O adaptarlo a tu app en **Flutter**

Solo dime 👍
