Para estructurar este proyecto bajo la metodología de **Agente Global (.agents)**, seguiremos una arquitectura modular. Este enfoque permite que cada "Skill" (Diseño, Código, Scraping) funcione como un módulo independiente dentro de tu ecosistema de automatización.

---

## 1. Estructura de la Habilidad Agente Global (`.agents`)

Primero, creamos la jerarquía de archivos para organizar el conocimiento y los recursos del agente.

```text
.agents/
├── SKILL.md                # Definición de capacidades y lógica del agente
├── scripts/                # Automatizaciones (bash/dart) para despliegue
├── ejemplos/               # Templates de UI y snippets de código
└── resources/              # Assets, logos y esquemas de Firestore
```

### SKILL.md (Fragmento de configuración)
```markdown
# Skill: Gestión de Papelería Pro
**Objetivo:** Automatizar el CRUD de productos y la interfaz de usuario.
**Stack:** Dart, Flutter, Firebase Firestore.
**Modo:** Agente de Generación de Código y Estructuras.
```

---

## 2. Prerrequisitos y Configuración del Entorno

Antes de codificar, debemos asegurar que las herramientas estén listas. Ejecuta estos comandos en tu terminal de **VS Code** o **Antigravity**:

### Verificación de Herramientas
```bash
# 1. Verificar Flutter
flutter --version

# 2. Instalar Firebase CLI (si no lo tienes)
curl -sL https://firebase.tools | upgrade=true bash

# 3. Login e inicialización
firebase login
```

### Configuración de Firebase en el Proyecto
1. Ve a [Firebase Console](https://console.firebase.google.com/).
2. Crea el proyecto **"proyectopapeleria"**.
3. Habilita **Firestore Database** en modo de prueba.
4. Registra tu app Android/iOS y descarga el archivo `google-services.json`.

---

## 3. Creación del Proyecto: `proyectopapeleria`

Crea el proyecto base y añade las dependencias necesarias en el `pubspec.yaml`.

```bash
flutter create proyectopapeleria
cd proyectopapeleria
flutter pub add firebase_core cloud_firestore
```

### Configuración de `pubspec.yaml`
Asegúrate de que tus dependencias luzcan así:
```yaml
dependencies:
  flutter:
    sdk: flutter
  firebase_core: ^latest_version
  cloud_firestore: ^latest_version
```

---

## 4. Implementación del Código (Skill de Código)

Estructuraremos el proyecto con una separación clara de responsabilidades.

### A. Modelo de Producto (`lib/models/producto_model.dart`)
```dart
class Producto {
  String id;
  String nombre;
  double precio;
  int stock;

  Producto({required this.id, required this.nombre, required this.precio, required this.stock});

  Map<String, dynamic> toMap() => {
    "nombre": nombre,
    "precio": precio,
    "stock": stock,
  };

  factory Producto.fromFirestore(Map<String, dynamic> data, String id) {
    return Producto(
      id: id,
      nombre: data['nombre'] ?? '',
      precio: (data['precio'] ?? 0.0).toDouble(),
      stock: data['stock'] ?? 0,
    );
  }
}
```

### B. Servicio Firestore (`lib/services/firebase_service.dart`)

```dart
import 'cloud_firestore/cloud_firestore.dart';
import '../models/producto_model.dart';

class FirebaseService {
  final CollectionReference _db = FirebaseFirestore.instance.collection('productos');

  // CREATE
  Future<void> addProducto(Producto p) => _db.add(p.toMap());

  // READ
  Stream<List<Producto>> getProductos() {
    return _db.snapshots().map((snap) =>
        snap.docs.map((doc) => Producto.fromFirestore(doc.data() as Map<String, dynamic>, doc.id)).toList());
  }

  // UPDATE
  Future<void> updateProducto(Producto p) => _db.doc(p.id).update(p.toMap());

  // DELETE
  Future<void> deleteProducto(String id) => _db.doc(id).delete();
}
```

---

## 5. Interfaz de Usuario (Skill de Diseño)

### C. Pantalla Principal (`lib/screens/home_screen.dart`)
Crearemos una interfaz limpia para gestionar el inventario de la papelería.

```dart
import 'package:flutter/material.dart';
import '../services/firebase_service.dart';
import '../models/producto_model.dart';

class HomeScreen extends StatelessWidget {
  final FirebaseService _service = FirebaseService();

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: Text("Papelería Inventario")),
      body: StreamBuilder<List<Producto>>(
        stream: _service.getProductos(),
        builder: (context, snapshot) {
          if (!snapshot.hasData) return Center(child: CircularProgressIndicator());
          final productos = snapshot.data!;
          return ListView.builder(
            itemCount: productos.length,
            itemBuilder: (context, index) {
              final p = productos[index];
              return ListTile(
                title: Text(p.nombre),
                subtitle: Text("Precio: \$${p.precio} - Stock: ${p.stock}"),
                trailing: IconButton(
                  icon: Icon(Icons.delete, color: Colors.red),
                  onPressed: () => _service.deleteProducto(p.id),
                ),
                onTap: () => _mostrarDialogo(context, producto: p),
              );
            },
          );
        },
      ),
      floatingActionButton: FloatingActionButton(
        child: Icon(Icons.add),
        onPressed: () => _mostrarDialogo(context),
      ),
    );
  }

  void _mostrarDialogo(BuildContext context, {Producto? producto}) {
    // Aquí implementarías un AlertDialog con TextFields para Nombre, Precio y Stock
    // llamando a _service.addProducto o _service.updateProducto
  }
}
```

---

## 6. Secuencia Lógica de Ejecución

1.  **Inicialización:** El Agente `.agents` verifica que `flutter doctor` sea exitoso.
2.  **Conectividad:** Se ejecuta `firebase login` y se vincula el ID del proyecto.
3.  **Scaffolding:** Se crean las carpetas `models`, `services` y `screens`.
4.  **Inyección de Código:** Se pueblan los archivos `.dart` proporcionados arriba.
5.  **Prueba:** Se ejecuta `flutter run` para verificar la conexión en tiempo real con Firestore.

> **Nota de Configuración:** Recuerda inicializar Firebase en tu `main.dart` usando:
> ```dart
> await Firebase.initializeApp(options: DefaultFirebaseOptions.currentPlatform);
> ```
