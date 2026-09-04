# Desglose del Trabajo — Sushi Craft

## Épica

**Gestión de pedidos del restaurante Sushi Craft**
Agrupa todo el ciclo de vida de un pedido (RF01–RF08): consulta de carta, armado del roll, envío a cocina en tandas, control de estados, bloqueo por insumo agotado, cierre de cuenta y reportes. No cabe en un solo sprint.

---

## Feature 1 — Armado y confirmación del pedido por el cliente

Cubre: **RF01** (consultar carta), **RF02** (personalizar ítems), **RF03** (confirmar y enviar al KDS), **RF07\*** (dividir en tandas).

### HU1 — Personalizar y agregar un roll al pedido
**Como** cliente, **quiero** seleccionar proteína, base, vegetales, envoltura y salsa para armar mi roll, **para** agregarlo a mi pedido con el precio correcto.

**Criterios de aceptación:**
- Dado que consulto la carta digital (RF01) y un insumo está disponible, cuando elijo proteína, base, envoltura y salsa para mi roll, entonces el sistema agrega el ítem personalizado a mi cuenta abierta y recalcula el total (RF02).
- Dado que intento seleccionar un componente agotado, cuando confirmo la selección, entonces el sistema lo bloquea y no permite agregarlo al pedido (RF02, regla de negocio No. 1).

**Subtareas:**
1. Crear el endpoint `POST /pedidos/{id}/items` para registrar el ítem personalizado (RF02)
2. Modelar la relación plato–insumo para validar disponibilidad en tiempo real (RF01, RF02)
3. Implementar el recálculo automático del total de la cuenta al agregar/quitar un ítem (RF02)

**Prioridad: Alta** — sin esto no existe carta ni pedido funcional; es la base del MVP.

---

### HU2 — Confirmar el pedido y enviarlo al KDS en tandas
**Como** mesero, **quiero** confirmar el pedido del cliente para que se envíe automáticamente al tablero de cocina dividido en tandas de máximo 6 rolls, **para** que el itamae reciba lotes manejables.

**Criterios de aceptación:**
- Dado que un pedido confirmado contiene más de 6 rolls, cuando se envía al KDS, entonces el sistema lo agrupa en tandas secuenciales de máximo 6 unidades (RF07\*).
- Dado que el pedido fue enviado al KDS, cuando el itamae lo recibe, entonces llega en menos de 2 segundos con estado RECIBIDO y el pedido ya no admite modificaciones (RF03, RNF02).

**Subtareas:**
1. Crear el endpoint `POST /pedidos/{id}/confirmar` que dispara el envío al KDS (RF03)
2. Implementar el agrupamiento de rolls en tandas de máximo 6 unidades (RF07\*)
3. Implementar el mecanismo de reintento ante falla de comunicación con el KDS (RF03, flujo alterno)

**Prioridad: Alta** — sin esto no se cumple la regla distintiva del concepto (tandas de 6) ni el flujo carta → cocina.

---

## Feature 2 — Gestión de cocina, inventario y cierre de cuenta

Cubre: **RF04** (cambiar estado desde cocina), **RF08\*** (bloqueo automático por insumo agotado). *(RF05 y RF06 quedan documentados como backlog futuro  ver nota final.)*

### HU3 — Actualizar el estado de un pedido en el KDS
**Como** cocina (itamae), **quiero** cambiar el estado de un pedido siguiendo RECIBIDO → EN PREPARACIÓN → LISTO → ENTREGADO, **para** que el salón sepa en qué punto va cada orden.

**Criterios de aceptación:**
- Dado que un pedido está en un estado válido, cuando cambio al siguiente estado de la secuencia, entonces el sistema lo registra con usuario y hora (RF04, RNF05).
- Dado que intento saltar un estado (ej. de RECIBIDO a ENTREGADO), cuando confirmo el cambio, entonces el sistema rechaza la transición (RF04, flujo alterno).

**Subtareas:**
1. Crear el endpoint PATCH /pedidos/{id}/estado con validación de transiciones (RF04)
2. Implementar la máquina de estados RECIBIDO → EN PREPARACIÓN → LISTO → ENTREGADO (RF04)
3. Registrar usuario y hora de cada cambio de estado para auditoría (RNF05)

**Prioridad: Alta** — indispensable para el ciclo completo del MVP (carta → pedido → cocina → pago).

---

### HU4 — Bloquear automáticamente platos sin insumo disponible
**Como** administrador, **quiero** que el sistema marque como agotados los platos que dependen de un insumo de pesca en cero, **para** evitar que se ordenen platos que no se pueden preparar.

**Criterios de aceptación:**
- Dado que el stock de un insumo de pesca (salmón, atún, nori) llega a 0, cuando el sistema lo detecta, entonces marca automáticamente como "Agotado" todos los platos que lo requieren y lo propaga a todos los clientes conectados en menos de 3 segundos (RF08\*, RNF06\*).
- Dado que un plato está marcado como agotado, cuando un cliente o mesero intenta agregarlo, entonces el sistema bloquea la acción hasta que se reponga el stock (RF08\*).

**Subtareas:**
1. Crear el servicio que vincula cada plato con sus insumos de pesca (RF08\*)
2. Implementar la regla de bloqueo/desbloqueo automático de platos según stock (RF08\*)
3. Propagar el cambio de disponibilidad en tiempo real a las sesiones activas (RNF06\*)

**Prioridad: Media** — regla distintiva del concepto, pero el MVP puede operar inicialmente con actualización manual mientras se prioriza el flujo base del pedido.

---

## Resumen de la jerarquía

| Nivel | Cantidad | Detalle |
|---|---|---|
| Épica | 1 | Gestión de pedidos del restaurante Sushi Craft |
| Feature | 2 | Armado y confirmación del pedido / Gestión de cocina, inventario y cierre de cuenta |
| Historia de Usuario | 4 | HU1 (RF01,RF02), HU2 (RF03,RF07\*), HU3 (RF04), HU4 (RF08\*,RNF06\*) |
| Subtarea | 12 | 3 por cada historia |

**Nota de alcance:** RF05 (cerrar cuenta y pago), RF06 (reporte de ventas) y los RNF de seguridad, disponibilidad, usabilidad y cumplimiento legal (RNF01, RNF03, RNF04, RNF07) ya están documentados en requirements.md pero no se desglosaron en historias para este sprint  quedan en el backlog para una iteración posterior, priorizando primero el ciclo mínimo carta → pedido → cocina.
