# Parte 6 — Product Backlog en Jira

Sitio Jira: https://d7000934.atlassian.net/jira/software/projects/SCRUM/boards/1
Proyecto: **SCRUM** — "Mi equipo de software"

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
| 1 | Crear la épica: título, descripción, fecha de vencimiento y etiqueta del concepto | ✅ **SCRUM-5**, vence 2026-10-16, etiqueta `japones` | *Captura de la épica* — **pendiente: pegar aquí el pantallazo de SCRUM-5** |
| 2 | Crear los 2 features y vincularlos a la épica como hijos | ✅ **SCRUM-6**, **SCRUM-7**, ambos con Principal = SCRUM-5 | *Captura mostrando la jerarquía* — **pendiente: pegar aquí el pantallazo** |
| 3 | Crear las 4 historias de usuario con criterios de aceptación en la descripción | ✅ **SCRUM-8**, **SCRUM-9**, **SCRUM-10**, **SCRUM-11** | *Captura de cada historia* — **pendiente: pegar 4 pantallazos** |
| 4 | Vincular cada historia a su feature correspondiente | ✅ ver tabla de trazabilidad abajo | *Captura del árbol épica → feature → historia* — **pendiente: pegar pantallazo del Timeline/backlog mostrando la jerarquía** |
| 5 | Crear las 12 subtareas (3 por historia), cada una con su historia padre | ✅ **SCRUM-12** a **SCRUM-23** | *Captura de cada subtarea* — **pendiente: pegar pantallazos** |
| 6 | Asignar prioridad (Alta/Media/Baja) a cada historia de usuario | ✅ ver tabla de trazabilidad abajo | *Captura del backlog con prioridades* — **pendiente: pegar pantallazo del backlog** |
| 7 | Actualizar scrum_work_restaurante.md con los IDs de Jira | ✅ ver `scrum_work_restaurante.md` actualizado | *Archivo actualizado* |
| 8 | Captura del cronograma/timeline en Jira mostrando la épica completa | ⬜ pendiente | *Captura del timeline* — **pendiente: pegar pantallazo de la vista Timeline** |

### Cómo completar las capturas pendientes

Los 8 issues de la tabla ya existen de verdad en el tablero (no son un borrador — se crearon con la integración de Jira). Para cerrar el checklist solo falta entrar al tablero, abrir cada issue o vista y tomar el pantallazo:

1. Épica: https://d7000934.atlassian.net/browse/SCRUM-5
2. Jerarquía / features: https://d7000934.atlassian.net/browse/SCRUM-6 y https://d7000934.atlassian.net/browse/SCRUM-7 (o la vista Timeline del proyecto)
3. Historias: SCRUM-8, SCRUM-9, SCRUM-10, SCRUM-11 (abrir cada una)
4. Árbol épica→feature→historia: vista **Timeline** del proyecto (menú lateral izquierdo) con la épica SCRUM-5 expandida
5. Subtareas: abrir cada historia y expandir su sección "Subtareas"
6. Backlog con prioridades: vista **Backlog** del proyecto
7. (ya resuelto en este archivo)
8. Timeline completo: vista **Timeline**, con el rango de fechas ajustado para que se vea toda la épica

Pega cada captura debajo de su fila correspondiente (o como anexo al final) antes de entregar.

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

---

## Errores comunes al usar Jira (autoevaluación del equipo)

| ❌ Lo que no se debe hacer | ✅ Lo correcto | Cómo quedó en este backlog |
|---|---|---|
| Crear todas las tareas sueltas, sin épica ni jerarquía | Toda subtarea cuelga de una historia, y toda historia de un feature o de la épica | Cumplido — ver tabla de trazabilidad |
| Escribir la historia como tarea técnica | La historia describe valor para un rol; lo técnico va en la subtarea | Cumplido — las 4 historias usan formato Como/quiero/para |
| Dejar la descripción vacía | La descripción incluye contexto y criterios de aceptación verificables | Cumplido — todas las historias tienen Dado/Cuando/Entonces |
| Asignar una historia completa a una sola persona | Se asignan las subtareas; la historia es responsabilidad del equipo | Pendiente — asignación de subtareas se hace en la planeación del sprint (Parte 7) |
| Estimar los puntos de historia en horas | Los puntos miden complejidad relativa, no tiempo | Pendiente — puntos de historia se diligencian en la Parte 7 |
| Crear el backlog una vez y no volver a tocarlo | El backlog se refina en cada sprint | A tener en cuenta en iteraciones futuras |
