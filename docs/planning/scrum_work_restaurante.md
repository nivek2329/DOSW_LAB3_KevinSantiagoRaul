# Desglose del Trabajo — Sushi Craft

## Épica

**Jira: [SCRUM\-5](https://d7000934.atlassian.net/browse/SCRUM-5)**

**Gestión de pedidos del restaurante Sushi Craft**
Agrupa todo el ciclo de vida de un pedido (RF01–RF08): consulta de carta, armado del roll, envío a cocina en tandas, control de estados, bloqueo por insumo agotado, cierre de cuenta y reportes. No cabe en un solo sprint.

## Feature 1 — Armado y confirmación del pedido por el cliente

**Jira: [SCRUM\-6](https://d7000934.atlassian.net/browse/SCRUM-6)** (implementado como tipo Tarea, vinculado a SCRUM\-5)

Cubre: **RF01** (consultar carta), **RF02** (personalizar ítems), **RF03** (confirmar y enviar al KDS), **RF07\*** (dividir en tandas).

### HU1 — Personalizar y agregar un roll al pedido

**Jira: [SCRUM\-8](https://d7000934.atlassian.net/browse/SCRUM-8)** — **Puntos de historia: 8** (Planning Poker con el equipo; [video de la sesión](https://drive.google.com/file/d/1XOTunURc89yTYYyYIG1nfml5uBxizaMK/view?usp=sharing))

**Como** cliente, **quiero** seleccionar proteína, base, vegetales, envoltura y salsa para armar mi roll, **para** agregarlo a mi pedido con el precio correcto.

**Criterios de aceptación:**

- Dado que consulto la carta digital (RF01) y un insumo está disponible, cuando elijo proteína, base, envoltura y salsa para mi roll, entonces el sistema agrega el ítem personalizado a mi cuenta abierta y recalcula el total (RF02).
- Dado que intento seleccionar un componente agotado, cuando confirmo la selección, entonces el sistema lo bloquea y no permite agregarlo al pedido (RF02, regla de negocio No. 1).

**Subtareas:**

1. [SCRUM\-12](https://d7000934.atlassian.net/browse/SCRUM-12) — Crear el endpoint `POST /pedidos/{id}/items` para registrar el ítem personalizado (RF02)
2. [SCRUM\-13](https://d7000934.atlassian.net/browse/SCRUM-13) — Modelar la relación plato–insumo para validar disponibilidad en tiempo real (RF01, RF02)
3. [SCRUM\-14](https://d7000934.atlassian.net/browse/SCRUM-14) — Implementar el recálculo automático del total de la cuenta al agregar/quitar un ítem (RF02)

**Prioridad: Alta** — sin esto no existe carta ni pedido funcional; es la base del MVP.

### HU2 — Confirmar el pedido y enviarlo al KDS en tandas

**Jira: [SCRUM\-9](https://d7000934.atlassian.net/browse/SCRUM-9)** — **Puntos de historia: 8**

**Como** mesero, **quiero** confirmar el pedido del cliente para que se envíe automáticamente al tablero de cocina dividido en tandas de máximo 6 rolls, **para** que el itamae reciba lotes manejables.

**Criterios de aceptación:**

- Dado que un pedido confirmado contiene más de 6 rolls, cuando se envía al KDS, entonces el sistema lo agrupa en tandas secuenciales de máximo 6 unidades (RF07\*).
- Dado que el pedido fue enviado al KDS, cuando el itamae lo recibe, entonces llega en menos de 2 segundos con estado RECIBIDO y el pedido ya no admite modificaciones (RF03, RNF02).

**Subtareas:**

1. [SCRUM\-15](https://d7000934.atlassian.net/browse/SCRUM-15) — Crear el endpoint `POST /pedidos/{id}/confirmar` que dispara el envío al KDS (RF03)
2. [SCRUM\-16](https://d7000934.atlassian.net/browse/SCRUM-16) — Implementar el agrupamiento de rolls en tandas de máximo 6 unidades (RF07\*)
3. [SCRUM\-17](https://d7000934.atlassian.net/browse/SCRUM-17) — Implementar el mecanismo de reintento ante falla de comunicación con el KDS (RF03, flujo alterno)

**Prioridad: Alta** — sin esto no se cumple la regla distintiva del concepto (tandas de 6) ni el flujo carta → cocina.

## Feature 2 — Gestión de cocina, inventario y cierre de cuenta

**Jira: [SCRUM\-7](https://d7000934.atlassian.net/browse/SCRUM-7)** (implementado como tipo Tarea, vinculado a SCRUM\-5)

Cubre: **RF04** (cambiar estado desde cocina), **RF08\*** (bloqueo automático por insumo agotado). *(RF05 y RF06 quedan documentados como backlog futuro — ver nota final.)*

### HU3 — Actualizar el estado de un pedido en el KDS

**Jira: [SCRUM\-10](https://d7000934.atlassian.net/browse/SCRUM-10)** — **Puntos de historia: 5**

**Como** cocina (itamae), **quiero** cambiar el estado de un pedido siguiendo RECIBIDO → EN PREPARACIÓN → LISTO → ENTREGADO, **para** que el salón sepa en qué punto va cada orden.

**Criterios de aceptación:**

- Dado que un pedido está en un estado válido, cuando cambio al siguiente estado de la secuencia, entonces el sistema lo registra con usuario y hora (RF04, RNF05).
- Dado que intento saltar un estado (ej. de RECIBIDO a ENTREGADO), cuando confirmo el cambio, entonces el sistema rechaza la transición (RF04, flujo alterno).

**Subtareas:**

1. [SCRUM\-18](https://d7000934.atlassian.net/browse/SCRUM-18) — Crear el endpoint `PATCH /pedidos/{id}/estado` con validación de transiciones (RF04)
2. [SCRUM\-19](https://d7000934.atlassian.net/browse/SCRUM-19) — Implementar la máquina de estados RECIBIDO → EN PREPARACIÓN → LISTO → ENTREGADO (RF04)
3. [SCRUM\-20](https://d7000934.atlassian.net/browse/SCRUM-20) — Registrar usuario y hora de cada cambio de estado para auditoría (RNF05)

**Prioridad: Alta** — indispensable para el ciclo completo del MVP (carta → pedido → cocina → pago).

### HU4 — Bloquear automáticamente platos sin insumo disponible

**Jira: [SCRUM\-11](https://d7000934.atlassian.net/browse/SCRUM-11)** — **Puntos de historia: 21**

**Como** administrador, **quiero** que el sistema marque como agotados los platos que dependen de un insumo de pesca en cero, **para** evitar que se ordenen platos que no se pueden preparar.

**Criterios de aceptación:**

- Dado que el stock de un insumo de pesca (salmón, atún, nori) llega a 0, cuando el sistema lo detecta, entonces marca automáticamente como "Agotado" todos los platos que lo requieren y lo propaga a todos los clientes conectados en menos de 3 segundos (RF08\*, RNF06\*).
- Dado que un plato está marcado como agotado, cuando un cliente o mesero intenta agregarlo, entonces el sistema bloquea la acción hasta que se reponga el stock (RF08\*).

**Subtareas:**

1. [SCRUM\-21](https://d7000934.atlassian.net/browse/SCRUM-21) — Crear el servicio que vincula cada plato con sus insumos de pesca (RF08\*)
2. [SCRUM\-22](https://d7000934.atlassian.net/browse/SCRUM-22) — Implementar la regla de bloqueo/desbloqueo automático de platos según stock (RF08\*)
3. [SCRUM\-23](https://d7000934.atlassian.net/browse/SCRUM-23) — Propagar el cambio de disponibilidad en tiempo real a las sesiones activas (RNF06\*)

**Prioridad: Media** — regla distintiva del concepto, pero el MVP puede operar inicialmente con actualización manual mientras se prioriza el flujo base del pedido.

**Nota:** el 21 supera el umbral habitual de Planning Poker (\>13 normalmente indica que conviene partir la historia). Vale la pena que el equipo valore dividir esta historia en el refinamiento del backlog antes del Sprint 1, dado el alcance de infraestructura en tiempo real que implica.

## Resumen de la jerarquía

| Nivel | Cantidad | Detalle | IDs de Jira |
| --- | --- | --- | --- |
| Épica | 1 | Gestión de pedidos del restaurante Sushi Craft | SCRUM\-5 |
| Feature | 2 | Armado y confirmación del pedido / Gestión de cocina, inventario y cierre de cuenta | SCRUM\-6, SCRUM\-7 |
| Historia de Usuario | 4 | HU1 (RF01,RF02), HU2 (RF03,RF07\*), HU3 (RF04), HU4 (RF08\*,RNF06\*) | SCRUM\-8, SCRUM\-9, SCRUM\-10, SCRUM\-11 |
| Subtarea | 12 | 3 por cada historia | SCRUM\-12 a SCRUM\-23 |

**Nota de alcance:** RF05 (cerrar cuenta y pago), RF06 (reporte de ventas) y los RNF de seguridad, disponibilidad, usabilidad y cumplimiento legal (RNF01, RNF03, RNF04, RNF07) ya están documentados en `requirements.md` pero no se desglosaron en historias para este sprint — quedan en el backlog para una iteración posterior, priorizando primero el ciclo mínimo carta → pedido → cocina.

**Nota técnica sobre Jira:** el proyecto SCRUM (plan gratuito, team\-managed) no tiene tipo de incidencia "Feature" nativo; se usó el tipo **Tarea** como equivalente, vinculada a la épica como hija. Cada Historia se vinculó a su Épica como *Principal* (restricción de Jira) y a su Feature correspondiente mediante un enlace **"relates to"**. Detalle completo en `jira.md`.
