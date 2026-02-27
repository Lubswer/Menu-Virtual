# Menú Digital

> Sistema de menú digital con pre-órdenes mediante código QR para restaurantes y cafeterías independientes.

---

## Tabla de Contenidos

- [Contexto del Proyecto](#contexto-del-proyecto)
- [Usuario Objetivo](#usuario-objetivo)
- [Problema Principal](#problema-principal)
- [Objetivo del Producto](#objetivo-del-producto)
- [Alcance del MVP (Producto Mínimo Viable)](#alcance-del-mvp-versión-inicial)
- [Exclusiones](#exclusiones-fuera-del-alcance)
- [Flujo General del Sistema](#flujo-general-del-sistema)
- [Casos Excepcionales y Decisiones de Diseño](#casos-excepcionales-y-decisiones-de-diseño)
- [Riesgos Técnicos Iniciales](#riesgos-técnicos-iniciales)
- [Posibles Expansiones Futuras](#posibles-expansiones-futuras)

---

## Contexto del Proyecto

**¿Qué problema observé?**  
Los restaurantes pequeños y medianos enfrentan dificultades para actualizar su menú de forma rápida cuando cambian precios o disponibilidad, lo que genera confusión en clientes y retrasos en el proceso de pedido.

**¿En qué tipo de negocio ocurre?**  
Restaurantes pequeños independientes y cafeterías modernas con entre 10 y 20 mesas, que no cuentan con aplicación propia ni sistemas digitales avanzados.

**¿Por qué vale la pena resolverlo?**  
La falta de actualización inmediata del menú y la indecisión en caja generan filas, pérdida de tiempo y desorganización operativa. Una solución digital simple puede reducir tiempos de decisión y mejorar la experiencia tanto del cliente como del personal.

---

## Usuario Objetivo

| Característica | Descripción |
|---|---|
| **Tipo de negocio** | Restaurantes o cafeterías independientes con entre 10 y 20 mesas, sin aplicación propia y sin sistema digital de pedidos implementado |
| **Nivel tecnológico** | Bajo o medio nivel de digitalización; utilizan menú físico impreso y gestionan pedidos de forma manual |
| **Capacidad económica** | Presupuesto limitado; no desean pagar mensualidades elevadas ni implementar sistemas complejos |

**Lo que no es mi usuario:**

- Cadenas grandes con sistemas POS y aplicaciones propias ya integradas  
- Negocios informales o puestos temporales que no requieren digitalización del menú  
- Negocios que no pertenecen al sector alimenticio  

---

## Problema Principal

> El restaurante enfrenta retrasos en el proceso de atención en caja debido a la indecisión de los clientes y a la falta de actualización inmediata del menú, lo que genera filas y desorganización operativa.

---

## Objetivo del Producto

> El sistema permitirá a los clientes visualizar el menú actualizado y generar una pre-orden antes de llegar a caja, con el fin de reducir el tiempo de atención y optimizar el flujo de clientes.

---

## Alcance del MVP (Versión Inicial)

- Visualización de menú digital accesible mediante código QR  
- Panel básico de administración para actualizar platos, precios y disponibilidad  
- Opción para marcar productos como "agotados" en tiempo real  
- Generación de pre-orden por parte del cliente  
- Creación automática de código único para cada orden generada  
- Interfaz de búsqueda en caja mediante código de orden  
- Visualización de información básica del producto (nombre, precio, descripción breve e imagen opcional)  

---

## Exclusiones (Fuera del Alcance)

- No incluye sistema completo de inventario  
- No incluye pagos online en la primera versión  
- No reemplaza el sistema POS del negocio  
- No incluye integración contable  

---

## Flujo General del Sistema
```
Cliente escanea QR
        ↓
  Visualiza menú
        ↓
  Genera pre-orden
        ↓
Sistema genera código único de orden
        ↓
   Cliente va a caja
        ↓
   Caja valida orden
        ↓
   Se realiza pago
        ↓
Orden cambia a estado "Confirmada"
        ↓
Stock de productos se actualiza
```

---

---

## Casos Excepcionales y Decisiones de Diseño

### Caso 1 — Cliente genera orden y no realiza el pago

**Problema:**  
Un cliente genera una pre-orden pero no se presenta en caja para pagarla.

**Impacto:**

- Saturación de órdenes pendientes  
- Dificultad para identificar órdenes reales  
- Posible alteración temporal del stock disponible  
- Congestión en la búsqueda en caja  

**Solución:**

- Expiración automática de la pre-orden después de 15 minutos si no es confirmada  
- Al expirar, la orden cambia a estado `Cancelada` y se libera cualquier reserva temporal de stock  
- Temporizador visible para el cliente indicando el tiempo restante  

---

### Caso 2 — Generación masiva de órdenes (ataques o uso indebido)

**Problema:**  
Un usuario o bot genera múltiples pre-órdenes de forma intencional o automatizada.

**Impacto:**

- Aglomeración en la base de datos  
- Lentitud en el sistema  
- Dificultad para buscar órdenes reales en caja  
- Posible caída del servicio  

**Solución:**

- Captcha antes de confirmar una pre-orden  
- Registro temporal del usuario (nombre + número telefónico u otro identificador básico)  
- Limitación de órdenes por dispositivo/IP en un periodo determinado  
- Limpieza automática de órdenes expiradas en segundo plano  

---

## Riesgos Técnicos Iniciales

### 1. Lentitud en la búsqueda de órdenes

| | Detalle |
|---|---|
| **Riesgo** | El sistema podría volverse lento al buscar órdenes en caja, especialmente en horarios pico |
| **Impacto** | Aumento en tiempo de atención, filas más largas, pérdida del objetivo principal |
| **Causas posibles** | Falta de indexación en base de datos, acumulación de órdenes no eliminadas, consultas mal optimizadas |
| **Mitigación** | Indexar campo de código único · Limpieza automática de órdenes expiradas · Búsquedas directas por código |

---

### 2. Mala actualización de disponibilidad de productos

| | Detalle |
|---|---|
| **Riesgo** | El sistema podría no reflejar correctamente cuando un producto está agotado o disponible |
| **Impacto** | Órdenes con productos no disponibles, conflictos en caja, pérdida de confianza |
| **Causas posibles** | Falta de sincronización entre confirmación de pago y actualización de stock |
| **Mitigación** | Actualizar stock solo en estado `Confirmada` · Validación final en caja · Transacciones atómicas |

---

### 3. Duplicación de órdenes

| | Detalle |
|---|---|
| **Riesgo** | Una orden podría generarse más de una vez por errores del sistema o múltiples clics del usuario |
| **Impacto** | Inconsistencia en datos, cobros duplicados, confusión operativa |
| **Causas posibles** | Múltiples solicitudes simultáneas, problemas de red, múltiples clics del usuario |
| **Mitigación** | Deshabilitar botón tras primer clic · Códigos únicos irrepetibles · Idempotencia en backend · Transacciones en base de datos |

---

## Posibles Expansiones Futuras

- Integración con pagos online  
- Conexión de inventario con sistema POS (evita actualizaciones manuales)  
- Estadísticas de ventas  
- Multiusuario con roles  

---

## Autor

Desarrollado por **Lubswer**

Proyecto en desarrollo — MVP v1.0

