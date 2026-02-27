
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

Proyecto en desarrollo — MVP v1.0
