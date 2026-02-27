# PROYECTO: Menú Digital

---

## 1. Contexto del Proyecto

### Descripción breve del entorno donde surge la idea

### ¿Qué problema observé?
Los restaurantes pequeños y medianos enfrentan dificultades para actualizar su menú de forma rápida cuando cambian precios o disponibilidad, lo que genera confusión en clientes y retrasos en el proceso de pedido.

### ¿En qué tipo de negocio ocurre?
Restaurantes pequeños independientes y cafeterías modernas con entre 10 y 20 mesas, que no cuentan con aplicación propia ni sistemas digitales avanzados.

### ¿Por qué vale la pena resolverlo?
Porque la falta de actualización inmediata del menú y la indecisión en caja generan filas, pérdida de tiempo y desorganización operativa. Una solución digital simple puede reducir tiempos de decisión y mejorar la experiencia tanto del cliente como del personal.

---

## 2. Usuario Objetivo

### Tipo de negocio
Restaurantes o cafeterías independientes con entre 10 y 20 mesas, sin aplicación propia y sin sistema digital de pedidos implementado.

### Nivel tecnológico actual
No necesariamente debe haber incursionado directamente al ámbito tecnológico.  
Negocios con bajo o medio nivel de digitalización, que actualmente utilizan menú físico impreso y gestionan pedidos de forma manual.

### Capacidad económica aproximada
Negocios con presupuesto limitado que no desean pagar mensualidades elevadas ni implementar sistemas complejos que requieran capacitación extensa.

### Lo que NO es mi usuario
- Cadenas grandes con sistemas POS y aplicaciones propias ya integradas.
- Negocios informales o puestos temporales que no requieren digitalización del menú.
- Negocios que no pertenecen al sector alimenticio.

---

## 3. Problema Principal

El restaurante enfrenta retrasos en el proceso de atención en caja debido a la indecisión de los clientes y a la falta de actualización inmediata del menú, lo que genera filas y desorganización operativa.

---

## 4. Objetivo del Producto

El sistema permitirá a los clientes visualizar el menú actualizado y generar una pre-orden antes de llegar a caja, con el fin de reducir el tiempo de atención y optimizar el flujo de clientes.

---

## 5. Alcance del MVP (Versión Inicial)

- Visualización de menú digital accesible mediante código QR.
- Panel básico de administración para actualizar platos, precios y disponibilidad.
- Opción para marcar productos como “agotados” en tiempo real.
- Generación de pre-orden por parte del cliente.
- Creación automática de código único para cada orden generada.
- Interfaz de búsqueda en caja mediante código de orden.
- Visualización de información básica del producto (nombre, precio, descripción breve e imagen opcional).

---

## 6. Exclusiones (Fuera del Alcance)

- No incluye sistema completo de inventario.
- No incluye pagos online en la primera versión.
- No reemplaza el sistema POS del negocio.
- No incluye integración contable.

---

## 7. Flujo General del Sistema

1. Cliente escanea QR.  
2. Visualiza menú.  
3. Genera pre-orden.  
4. El sistema genera un código único de orden.  
5. Cliente va a caja.  
6. Caja valida orden.  
7. Se realiza pago.  
8. Orden cambia a estado confirmada.  
9. Internamente el stock de productos baja una vez confirmada la orden.

---

## 8. Casos Excepcionales y Decisiones de Diseño

### Cliente genera orden y no realiza el pago

**Problema:**  
Un cliente genera una pre-orden pero no se presenta en caja para pagarla.

**Impacto:**
- Saturación de órdenes pendientes.
- Dificultad para identificar órdenes reales.
- Posible alteración temporal del stock disponible.
- Congestión en la búsqueda en caja.

**Decisión de Diseño / Solución:**
- Implementar expiración automática de la pre-orden después de 15 minutos si no es confirmada.
- Al expirar:
  - La orden cambia a estado "Cancelada".
  - Se libera cualquier reserva temporal de stock.
- Mostrar al cliente un temporizador visible indicando el tiempo restante para completar el pago.

---

### Problema: Ataques sobre múltiples órdenes

**Impacto:**  
Aglomeración en base de datos, dificultad para buscar una orden.

**Solución:**  
Implementaciones de captcha, registro temporal de usuario antes de realizar una pre-orden.

---

### Generación masiva de órdenes (ataques o uso indebido)

**Problema:**  
Un usuario o bot genera múltiples pre-órdenes de forma intencional o automatizada.

**Impacto:**
- Aglomeración en la base de datos.
- Lentitud en el sistema.
- Dificultad para buscar órdenes reales en caja.
- Posible caída del servicio.

**Decisión de Diseño / Solución:**
Se implementarán las siguientes medidas de seguridad:

- Captcha antes de confirmar una pre-orden.
- Registro temporal del usuario (nombre + número telefónico u otro identificador básico).
- Limitación de generación de órdenes por dispositivo/IP en un periodo determinado.
- Limpieza automática de órdenes expiradas en segundo plano.

**Estas decisiones permiten:**
- Mantener el sistema ligero y eficiente.
- Evitar abuso sin complicar demasiado la experiencia del cliente.
- Proteger la base de datos.
- Garantizar rapidez en caja (objetivo principal del producto).

---

## 9. Riesgos Técnicos Iniciales

### 1️⃣ Lentitud en la búsqueda de órdenes

**Riesgo:**  
El sistema podría volverse lento al momento de buscar órdenes en caja, especialmente en horarios pico.

**Impacto:**
- Aumento en el tiempo de atención.
- Filas más largas.
- Mala experiencia del cliente.
- Pérdida del objetivo principal del producto (agilizar procesos).

**Posibles causas:**
- Falta de indexación en base de datos.
- Acumulación de órdenes no eliminadas.
- Consultas mal optimizadas.

**Mitigación inicial:**
- Indexar el campo de código único de orden.
- Implementar limpieza automática de órdenes expiradas.
- Usar búsquedas directas por código en lugar de listados generales.

---

### 2️⃣ Mala actualización de disponibilidad de productos

**Riesgo:**  
El sistema podría no reflejar correctamente cuando un producto está agotado o disponible.

**Impacto:**
- Clientes generan órdenes con productos no disponibles.
- Conflictos en caja.
- Pérdida de confianza en el sistema.

**Posibles causas:**
- Falta de sincronización entre confirmación de pago y actualización de stock.
- Errores en lógica de negocio.

**Mitigación inicial:**
- Actualizar stock únicamente cuando la orden cambia a estado "Confirmada".
- Implementar validación final en caja antes de confirmar.
- Manejar operaciones de actualización de stock como transacciones atómicas.

---

### 3️⃣ Duplicación de órdenes

**Riesgo:**  
Una orden podría generarse más de una vez por errores del sistema o por múltiples clics del usuario.

**Impacto:**
- Inconsistencia en datos.
- Cobros duplicados.
- Confusión operativa.

**Posibles causas:**
- Falta de control ante múltiples solicitudes simultáneas.
- Problemas de red que repiten la solicitud.
- Usuario presiona varias veces el botón de confirmar.

**Mitigación inicial:**
- Deshabilitar botón de confirmación después del primer clic.
- Generar códigos únicos irrepetibles.
- Implementar validaciones de idempotencia en el backend.
- Uso de transacciones en base de datos.

---

## 10. Posibles Expansiones Futuras

- Integración con pagos online.
- Conexión de inventario con sistema POS (Evita actualizaciones manuales).
- Estadísticas de ventas.
- Multiusuario con roles.
