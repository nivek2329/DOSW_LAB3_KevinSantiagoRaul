# Parte 6 — Product Backlog en Jira

Sitio Jira: https://d7000934.atlassian.net/jira/software/projects/SCRUM/boards/1
Proyecto: **SCRUM** — "Sushi Craft"

> Nota de alcance técnico: el proyecto de Jira usado (tipo *team-managed*, plan gratuito) no incluye un tipo de incidencia **Feature** nativo — esa jerarquía de 4 niveles (Épica > Feature > Historia > Subtarea) requiere Advanced Roadmaps (plan Premium). Como equivalente se usó el tipo **Tarea** para representar cada Feature, vinculada a la Épica como hija (campo *Principal*), y cada Historia se vinculó a su Feature correspondiente con un enlace **"relates to"** (visible en la sección *Incidencias enlazadas* de cada Historia) además de estar vinculada directamente a la Épica como *Principal* (restricción propia de la jerarquía de Jira: una Historia solo puede tener una Épica como padre directo). Esto se documenta aquí para que quede explícito en la sustentación.

---

## Tipos de issue en Jira y cuándo usar cada uno

| Tipo de issue | Qué representa | Ejemplo en Sushi Craft |
|---|---|---|
| Epic (Épica) | Objetivo grande que agrupa varios features | **SCRUM-5** — Gestión de pedidos del restaurante Sushi Craft |
| Feature (implementado como Tarea) | Capacidad del sistema dentro de una épica | **SCRUM-6** / **SCRUM-7** |
| Story (Historia) | Valor entregable a un rol | **SCRUM-8** a **SCRUM-11** |
| Subtask (Subtarea) | Trabajo técnico dentro de una historia | **SCRUM-12** a **SCRUM-23** |
| Bug | No se usa en este laboratorio | — |

## Campos obligatorios por tipo de issue

| Campo | Épica / Feature | Historia / Subtarea |
|---|---|---|
| Título | Nombre corto del objetivo o de la capacidad | Historia: formato Como/quiero/para. Subtarea: verbo técnico en infinitivo |
| Descripción | Qué agrupa, por qué existe y qué queda fuera | Contexto + HU + criterios Dado/Cuando/Entonces |
| Responsable | Sin asignar hasta la planeación del sprint | Se asigna en la planeación del sprint (Parte 7) |
| Prioridad | Alta/Media/Baja con justificación | La subtarea hereda la prioridad de su historia padre |
| Puntos de historia | No aplica | Solo en la historia; se diligencia en la Parte 7 |
| Vínculo padre | Feature → Épica | Historia → Épica (parent) + Historia → Feature ("relates to"). Subtarea → Historia (parent) |
| Etiqueta | Concepto del restaurante: `japones` | Módulo afectado: `carta`, `pedido`, `cocina` |

---

## Checklist de actividades en Jira

| # | Actividad en Jira | Estado | Evidencia en jira.md |
|---|---|---|---|
| 1 | Crear la épica: título, descripción, fecha de vencimiento y etiqueta del concepto | **SCRUM-5**, vence 2026-10-16, etiqueta `japones` |<img width="966" height="342" alt="image" src="https://github.com/user-attachments/assets/b615b256-c16f-4d06-af0f-a035dbffa748" />|
| 2 | Crear los 2 features y vincularlos a la épica como hijos | **SCRUM-6**, **SCRUM-7**, ambos con Principal = SCRUM-5 |<img width="954" height="557" alt="image" src="https://github.com/user-attachments/assets/b656a854-75f3-4722-8121-0a27709b4635" />|
| 3 | Crear las 4 historias de usuario con criterios de aceptación en la descripción | **SCRUM-8**, **SCRUM-9**, **SCRUM-10**, **SCRUM-11** |<img width="605" height="179" alt="image" src="https://github.com/user-attachments/assets/909fe633-3f11-42cc-83ff-c0b087e2d433" />|
| 4 | Vincular cada historia a su feature correspondiente | ver tabla de trazabilidad abajo |<img width="1576" height="901" alt="image" src="https://github.com/user-attachments/assets/4557e216-84ff-42a0-8e45-516cc4669e83" />|
| 5 | Crear las 12 subtareas (3 por historia), cada una con su historia padre | **SCRUM-12** a **SCRUM-23** |<img width="1251" height="484" alt="image" src="https://github.com/user-attachments/assets/bed7364e-cc6e-4069-ad28-c2857b71677a" />|
| 6 | Asignar prioridad (Alta/Media/Baja) a cada historia de usuario | ver tabla de trazabilidad abajo |<img width="1602" height="874" alt="image" src="https://github.com/user-attachments/assets/ab793f8c-edd9-4b06-819c-25890b683562" />|
| 7 | Captura del cronograma/timeline en Jira mostrando la épica completa | pendiente | <img width="1601" height="459" alt="image" src="https://github.com/user-attachments/assets/d0f00573-f9d3-4cc8-9567-0578f1df84d8" />|

---

## Tabla de trazabilidad (Épica → Feature → Historia → Subtarea)

### Feature SCRUM-6 — Armado y confirmación del pedido por el cliente

**SCRUM-8** — Como cliente, quiero personalizar y agregar un roll a mi pedido... — Prioridad: **Alta** — Etiqueta: `pedido`
- SCRUM-12 — Crear el endpoint POST /pedidos/{id}/items para registrar el ítem personalizado
- SCRUM-13 — Modelar la relación plato-insumo para validar disponibilidad en tiempo real
- SCRUM-14 — Implementar el recálculo automático del total de la cuenta al agregar/quitar un ítem

**SCRUM-9** — Como mesero, quiero confirmar el pedido y enviarlo al KDS en tandas... — Prioridad: **Alta** — Etiqueta: `cocina`
- SCRUM-15 — Crear el endpoint POST /pedidos/{id}/confirmar que dispara el envío al KDS
- SCRUM-16 — Implementar el agrupamiento de rolls en tandas de máximo 6 unidades
- SCRUM-17 — Implementar el mecanismo de reintento ante falla de comunicación con el KDS

### Feature SCRUM-7 — Gestión de cocina, inventario y cierre de cuenta

**SCRUM-10** — Como cocina (itamae), quiero actualizar el estado de un pedido en el KDS... — Prioridad: **Alta** — Etiqueta: `cocina`
- SCRUM-18 — Crear el endpoint PATCH /pedidos/{id}/estado con validación de transiciones
- SCRUM-19 — Implementar la máquina de estados RECIBIDO → EN PREPARACIÓN → LISTO → ENTREGADO
- SCRUM-20 — Registrar usuario y hora de cada cambio de estado para auditoría

**SCRUM-11** — Como administrador, quiero bloquear automáticamente platos sin insumo disponible... — Prioridad: **Media** — Etiqueta: `carta`
- SCRUM-21 — Crear el servicio que vincula cada plato con sus insumos de pesca
- SCRUM-22 — Implementar la regla de bloqueo/desbloqueo automático de platos según stock
- SCRUM-23 — Propagar el cambio de disponibilidad en tiempo real a las sesiones activas

### Justificación de la decisión de planeación

<img width="1555" height="528" alt="image" src="https://github.com/user-attachments/assets/192794fb-b1b7-4109-b2bd-c3457439615a" />

Para el Sprint 1 se seleccionaron las historias SCRUM-10 (5 pts), SCRUM-8 (8 pts) y SCRUM-9 (8 pts), con un total de 21 puntos de historia. Se dejó fuera SCRUM-11 (21 pts) por dos razones: primero, su prioridad es Media frente a la prioridad Alta de las otras tres, por lo que el criterio de priorización (Alta antes que Media) la ubica después en el orden de trabajo; segundo, sus 21 puntos por sí solos igualan la capacidad completa que ya cubren las otras tres historias juntas, lo que indica que la historia es demasiado grande para una sola iteración y debería dividirse en subtareas o historias más pequeñas antes de planearla en un sprint futuro, en vez de meterla completa y arriesgar no terminarla.
En cuanto a la asignación de responsables, las 9 subtareas de las 3 historias incluidas se repartieron así: Kevin Ángel quedó con las 3 subtareas de SCRUM-8 (SCRUM-12, 13, 14) más una de SCRUM-10 (SCRUM-18), y Santiago García con las 3 subtareas de SCRUM-9 (SCRUM-15, 16, 17) más las otras dos de SCRUM-10 (SCRUM-19, 20). Se buscó que cada historia quedara, en lo posible, concentrada en una sola persona, para que quien la trabaje mantenga el contexto técnico completo de esa funcionalidad en vez de fragmentarlo entre los dos; la carga final quedó balanceada en 4 subtareas para uno y 5 para el otro.

---
