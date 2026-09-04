# SUSHI CRAFT
## Desarrollo y Operaciones de Software
### ANÁLISIS DE REQUERIMIENTOS

**Fecha:** 31/08/2026
**Proyecto:** Sushi Craft — Sistema de gestión de pedidos y cocina
**Link:** https://www.figma.com/make/xDeP6F13LfkYCX0nUykOzs/App-de-estudio-con-tarjetas?fullscreen=1&t=fmNmS3qTx8J8k4Jz-1&code-node-id=0-6
---

## Tabla de contenido

**Funcionales**
- [RF01 - Consultar carta digital](#rf01)
- [RF02 - Personalizar pedido](#rf02)
- [RF03 - Confirmar y enviar pedido al KDS](#rf03)
- [RF04 - Cambiar estado del pedido](#rf04)
- [RF05 - Cerrar cuenta y registrar pago](#rf05)
- [RF06 - Generar reporte de ventas](#rf06)
- [RF07\* - Dividir pedido en tandas](#rf07)
- [RF08\* - Bloquear platos por agotamiento de insumo](#rf08)

**No funcionales**
- [RNF01 - Seguridad](#rnf01)
- [RNF02 - Rendimiento](#rnf02)
- [RNF03 - Disponibilidad](#rnf03)
- [RNF04 - Usabilidad](#rnf04)
- [RNF05 - Auditabilidad](#rnf05)
- [RNF06\* - Confiabilidad](#rnf06)
- [RNF07\* - Cumplimiento legal](#rnf07)

\* Regla de negocio propia del concepto.

---

# REQUERIMIENTOS FUNCIONALES

<a id="rf01"></a>
## RF01 — Consultar carta digital

| Campo | Detalle |
|---|---|
| **Código** | RF01 |
| **Nombre** | Consultar carta digital |
| **Descripción** | Permite al cliente consultar la carta digital viendo el estado de disponibilidad de cada rollo, nigiri o plato caliente. |
| **Cómo se ejecutará** | El cliente accede a la carta desde la aplicación/tablet en la mesa o desde su celular vía QR; el sistema consulta el inventario en tiempo real y muestra el estado de cada plato. |
| **Actor principal** | Cliente |
| **Precondiciones** | El sistema debe estar operativo y el inventario de insumos debe estar actualizado. |

**Datos de entrada**

| Nombre | Descripción | Tipo de campo | Reglas / Aplicación | Obligatorio |
|---|---|---|---|---|
| Categoría | Filtro de tipo de plato (rollos, nigiri, calientes) | Selección | Filtra la lista mostrada | No |
| Mesa/Sesión | Identificador de la mesa o sesión del cliente | Texto/Código | Vincula la consulta a la cuenta abierta | Sí |

**Datos de salida**

| Nombre | Descripción | Tipo de campo | Reglas / Aplicación | Obligatorio |
|---|---|---|---|---|
| Lista de platos | Nombre, precio, foto y descripción de cada plato | Lista | Ordenada por categoría | Sí |
| Estado de disponibilidad | Disponible / Agotado | Etiqueta | Se calcula según stock de insumos | Sí |

**Flujo básico**

| Paso | Actor | Descripción | Excepciones |
|---|---|---|---|
| 1 | Cliente | Abre la carta digital desde su dispositivo | El QR/enlace no carga |
| 2 | Sistema | Consulta el inventario de insumos en tiempo real | Falla de conexión con el módulo de inventario |
| 3 | Sistema | Muestra cada plato con su estado (disponible/agotado) | — |

**Flujo alterno**

| Paso | Actor | Descripción | Excepciones |
|---|---|---|---|
| A1 | Cliente | Filtra por categoría (rollos, nigiri, calientes) | Categoría sin resultados |

**Reglas de negocio**

| No. | Descripción |
|---|---|
| 1 | Un plato sin stock suficiente de sus insumos se marca automáticamente como "Agotado" y no puede agregarse al pedido. |

---

<a id="rf02"></a>
## RF02 — Personalizar pedido

| Campo | Detalle |
|---|---|
| **Código** | RF02 |
| **Nombre** | Agregar, modificar y quitar ítems personalizados de un pedido |
| **Descripción** | Permite agregar, modificar y quitar ítems personalizados (proteína, base, vegetales, envoltura, salsa) de un pedido con la cuenta abierta. |
| **Cómo se ejecutará** | El cliente selecciona un plato base y elige sus componentes desde opciones predefinidas; el sistema recalcula el precio y agrega el ítem al pedido asociado a la cuenta abierta. |
| **Actor principal** | Cliente |
| **Precondiciones** | Debe existir una cuenta/mesa abierta; los insumos seleccionados deben estar disponibles. |

**Datos de entrada**

| Nombre | Descripción | Tipo de campo | Reglas / Aplicación | Obligatorio |
|---|---|---|---|---|
| Proteína | Tipo de proteína (salmón, atún, pollo, tofu, etc.) | Selección | Debe estar disponible en stock | Sí |
| Base | Arroz, ensalada, etc. | Selección | — | Sí |
| Vegetales | Lista de vegetales a incluir | Multiselección | Máximo definido por el plato | No |
| Envoltura | Nori, soya, papel de arroz | Selección | — | Sí |
| Salsa | Salsas de acompañamiento | Multiselección | — | No |
| Cantidad | Unidades del ítem | Numérico | Mayor a 0 | Sí |

**Datos de salida**

| Nombre | Descripción | Tipo de campo | Reglas / Aplicación | Obligatorio |
|---|---|---|---|---|
| Ítem del pedido | Resumen del ítem personalizado y su precio | Objeto | Se agrega a la cuenta abierta | Sí |
| Total actualizado | Nuevo total de la cuenta | Numérico | Recalculado en cada cambio | Sí |

**Flujo básico**

| Paso | Actor | Descripción | Excepciones |
|---|---|---|---|
| 1 | Cliente | Selecciona un plato y personaliza sus componentes | Componente sin stock |
| 2 | Sistema | Valida disponibilidad de cada insumo seleccionado | Insumo agotado durante la selección |
| 3 | Sistema | Agrega el ítem al pedido y recalcula el total | — |

**Flujo alterno**

| Paso | Actor | Descripción | Excepciones |
|---|---|---|---|
| A1 | Cliente | Modifica o quita un ítem ya agregado al pedido | Ítem ya enviado a cocina (no modificable) |

**Reglas de negocio**

| No. | Descripción |
|---|---|
| 1 | No se puede modificar o quitar un ítem que ya fue confirmado y enviado al KDS (RF03). |

---

<a id="rf03"></a>
## RF03 — Confirmar y enviar pedido al KDS

| Campo | Detalle |
|---|---|
| **Código** | RF03 |
| **Nombre** | Confirmar un pedido y enviarlo automáticamente al KDS |
| **Descripción** | Confirma un pedido y lo envía automáticamente al tablero de cocina (Kitchen Display System). |
| **Cómo se ejecutará** | El cliente o mesero confirma el pedido armado; el sistema lo transmite en tiempo real al KDS de cocina. |
| **Actor principal** | Cliente / Mesero |
| **Precondiciones** | El pedido debe tener al menos un ítem válido y disponible. |

**Datos de entrada**

| Nombre | Descripción | Tipo de campo | Reglas / Aplicación | Obligatorio |
|---|---|---|---|---|
| Pedido | Conjunto de ítems personalizados agregados | Objeto/Lista | Debe tener mínimo 1 ítem | Sí |
| Observaciones | Notas adicionales para cocina | Texto | Opcional | No |

**Datos de salida**

| Nombre | Descripción | Tipo de campo | Reglas / Aplicación | Obligatorio |
|---|---|---|---|---|
| Ticket de cocina | Pedido visible en el KDS con estado RECIBIDO | Objeto | Se envía en menos de 2 segundos (RNF02) | Sí |
| Confirmación al cliente | Aviso de pedido enviado | Notificación | — | Sí |

**Flujo básico**

| Paso | Actor | Descripción | Excepciones |
|---|---|---|---|
| 1 | Cliente/Mesero | Confirma el pedido armado | Pedido vacío |
| 2 | Sistema | Envía el pedido al KDS con estado RECIBIDO | Falla de comunicación con el KDS |
| 3 | Sistema | Notifica al cliente que el pedido fue enviado | — |

**Flujo alterno**

| Paso | Actor | Descripción | Excepciones |
|---|---|---|---|
| A1 | Sistema | Reintenta el envío si el KDS no responde | Reintentos agotados: se notifica al mesero |

**Reglas de negocio**

| No. | Descripción |
|---|---|
| 1 | El pedido debe llegar al tablero de cocina en menos de 2 segundos desde su confirmación (ver RNF02). |

---

<a id="rf04"></a>
## RF04 — Cambiar estado del pedido desde cocina

| Campo | Detalle |
|---|---|
| **Código** | RF04 |
| **Nombre** | Cambiar el estado de un pedido desde cocina |
| **Descripción** | Permite cambiar el estado de un pedido desde cocina siguiendo el flujo RECIBIDO → EN PREPARACIÓN → LISTO → ENTREGADO. |
| **Cómo se ejecutará** | El personal de cocina/mesero actualiza el estado del pedido directamente en el KDS conforme avanza su preparación. |
| **Actor principal** | Cocina / Mesero |
| **Precondiciones** | El pedido debe existir en el KDS con un estado previo válido. |

**Datos de entrada**

| Nombre | Descripción | Tipo de campo | Reglas / Aplicación | Obligatorio |
|---|---|---|---|---|
| Pedido | Identificador del pedido a actualizar | Código | Debe existir en el KDS | Sí |
| Nuevo estado | Estado siguiente en el flujo | Selección | Solo transiciones válidas de la secuencia | Sí |

**Datos de salida**

| Nombre | Descripción | Tipo de campo | Reglas / Aplicación | Obligatorio |
|---|---|---|---|---|
| Pedido actualizado | Pedido con su nuevo estado y hora del cambio | Objeto | Se registra para auditoría (RNF05) | Sí |

**Flujo básico**

| Paso | Actor | Descripción | Excepciones |
|---|---|---|---|
| 1 | Cocina | Selecciona el pedido en el KDS | Pedido no encontrado |
| 2 | Cocina | Cambia el estado al siguiente en la secuencia | Intento de saltar un estado |
| 3 | Sistema | Registra el cambio con usuario y hora | — |

**Flujo alterno**

| Paso | Actor | Descripción | Excepciones |
|---|---|---|---|
| A1 | Sistema | Rechaza transiciones fuera de secuencia (ej. de RECIBIDO a ENTREGADO) | — |

**Reglas de negocio**

| No. | Descripción |
|---|---|
| 1 | Los estados solo pueden avanzar en el orden RECIBIDO → EN PREPARACIÓN → LISTO → ENTREGADO, sin saltos. |
| 2 | Todo cambio de estado queda registrado con usuario y hora (ver RNF05). |

---

<a id="rf05"></a>
## RF05 — Cerrar cuenta y registrar pago

| Campo | Detalle |
|---|---|
| **Código** | RF05 |
| **Nombre** | Cerrar la cuenta de una mesa y registrar el pago |
| **Descripción** | Permite cerrar la cuenta de una mesa y registrar el pago correspondiente. |
| **Cómo se ejecutará** | El mesero o cajero selecciona la mesa, revisa el consumo total y registra el método y monto de pago para cerrar la cuenta. |
| **Actor principal** | Mesero / Cajero |
| **Precondiciones** | Todos los ítems del pedido deben estar en estado ENTREGADO. |

**Datos de entrada**

| Nombre | Descripción | Tipo de campo | Reglas / Aplicación | Obligatorio |
|---|---|---|---|---|
| Mesa | Mesa/cuenta a cerrar | Selección | Debe estar abierta | Sí |
| Método de pago | Efectivo, tarjeta, digital | Selección | — | Sí |
| Monto | Valor pagado | Numérico | Debe cubrir el total de la cuenta | Sí |

**Datos de salida**

| Nombre | Descripción | Tipo de campo | Reglas / Aplicación | Obligatorio |
|---|---|---|---|---|
| Comprobante de pago | Resumen del consumo y pago | Documento | Base para la factura electrónica (RNF07) | Sí |
| Mesa liberada | Estado de la mesa vuelve a disponible | Estado | — | Sí |

**Flujo básico**

| Paso | Actor | Descripción | Excepciones |
|---|---|---|---|
| 1 | Mesero/Cajero | Selecciona la mesa a cerrar | Mesa con ítems aún no entregados |
| 2 | Sistema | Calcula el total de la cuenta | — |
| 3 | Cajero | Registra el pago | Monto insuficiente |
| 4 | Sistema | Cierra la cuenta y libera la mesa | — |

**Flujo alterno**

| Paso | Actor | Descripción | Excepciones |
|---|---|---|---|
| A1 | Cajero | Divide el pago entre varios métodos | Suma de montos no coincide con el total |

**Reglas de negocio**

| No. | Descripción |
|---|---|
| 1 | No se puede cerrar una cuenta si existen ítems del pedido sin estado ENTREGADO. |
| 2 | Toda venta debe generar una factura electrónica válida ante la DIAN antes de cerrar la cuenta (ver RNF07\*). |

---

<a id="rf06"></a>
## RF06 — Generar reporte de ventas

| Campo | Detalle |
|---|---|
| **Código** | RF06 |
| **Nombre** | Generar el reporte de ventas del día por plato y por mesero |
| **Descripción** | Genera el reporte de ventas del día, desglosado por plato y por mesero. |
| **Cómo se ejecutará** | El administrador solicita el reporte del día; el sistema consolida las ventas cerradas y las agrupa por plato y por mesero. |
| **Actor principal** | Administrador |
| **Precondiciones** | Deben existir cuentas cerradas en el día consultado. |

**Datos de entrada**

| Nombre | Descripción | Tipo de campo | Reglas / Aplicación | Obligatorio |
|---|---|---|---|---|
| Fecha | Día a consultar | Fecha | Por defecto, el día actual | Sí |

**Datos de salida**

| Nombre | Descripción | Tipo de campo | Reglas / Aplicación | Obligatorio |
|---|---|---|---|---|
| Reporte por plato | Cantidad vendida e ingresos por plato | Tabla | — | Sí |
| Reporte por mesero | Ventas totales atendidas por cada mesero | Tabla | — | Sí |

**Flujo básico**

| Paso | Actor | Descripción | Excepciones |
|---|---|---|---|
| 1 | Administrador | Selecciona la fecha del reporte | Fecha sin ventas registradas |
| 2 | Sistema | Consolida las cuentas cerradas del día | — |
| 3 | Sistema | Muestra el reporte agrupado por plato y por mesero | — |

**Flujo alterno**

| Paso | Actor | Descripción | Excepciones |
|---|---|---|---|
| A1 | Administrador | Exporta el reporte (PDF/Excel) | Error en la generación del archivo |

**Reglas de negocio**

| No. | Descripción |
|---|---|
| 1 | Solo se consideran en el reporte las cuentas efectivamente cerradas y pagadas. |

---

<a id="rf07"></a>
## RF07\* — Dividir pedido en tandas

| Campo | Detalle |
|---|---|
| **Código** | RF07\* |
| **Nombre** | Dividir automáticamente un pedido de rolls en tandas |
| **Descripción** | Divide automáticamente un pedido de rolls en tandas de máximo 6 unidades para la estación del itamae (chef de sushi). |
| **Cómo se ejecutará** | Al confirmarse un pedido (RF03), el sistema agrupa los rolls en lotes de máximo 6 unidades antes de enviarlos a la estación del itamae. |
| **Actor principal** | Sistema (automático) |
| **Precondiciones** | El pedido confirmado debe contener rolls. |

**Datos de entrada**

| Nombre | Descripción | Tipo de campo | Reglas / Aplicación | Obligatorio |
|---|---|---|---|---|
| Pedido confirmado | Ítems de tipo roll del pedido | Lista | Proviene de RF03 | Sí |

**Datos de salida**

| Nombre | Descripción | Tipo de campo | Reglas / Aplicación | Obligatorio |
|---|---|---|---|---|
| Tandas | Lotes de máximo 6 rolls cada uno | Lista de lotes | Enviadas en orden a la estación del itamae | Sí |

**Flujo básico**

| Paso | Actor | Descripción | Excepciones |
|---|---|---|---|
| 1 | Sistema | Identifica los rolls del pedido confirmado | Pedido sin rolls |
| 2 | Sistema | Agrupa los rolls en tandas de máximo 6 unidades | Cantidad no múltiplo de 6 (última tanda parcial) |
| 3 | Sistema | Envía las tandas en orden a la estación del itamae | — |

**Flujo alterno**

| Paso | Actor | Descripción | Excepciones |
|---|---|---|---|
| A1 | Sistema | Si hay menos de 6 rolls, envía una sola tanda parcial | — |

**Reglas de negocio (propia del concepto)**

| No. | Descripción |
|---|---|
| 1 | Ninguna tanda enviada al itamae puede superar las 6 unidades de rolls. |

---

<a id="rf08"></a>
## RF08\* — Bloquear platos por agotamiento de insumo

| Campo | Detalle |
|---|---|
| **Código** | RF08\* |
| **Nombre** | Bloquear automáticamente en la carta los platos que dependan de un insumo de pesca agotado |
| **Descripción** | Bloquea automáticamente en la carta los platos que dependan del insumo de pesca (ej. salmón, atún, nori) agotado. |
| **Cómo se ejecutará** | Cada vez que el inventario de un insumo de pesca llega a cero, el sistema recorre la carta y marca como "Agotado" todos los platos que lo requieren. |
| **Actor principal** | Sistema (automático) |
| **Precondiciones** | Debe existir una relación configurada entre plato e insumo. |

**Datos de entrada**

| Nombre | Descripción | Tipo de campo | Reglas / Aplicación | Obligatorio |
|---|---|---|---|---|
| Insumo | Insumo de pesca cuyo stock cambia | Código | Dispara la validación al llegar a 0 | Sí |

**Datos de salida**

| Nombre | Descripción | Tipo de campo | Reglas / Aplicación | Obligatorio |
|---|---|---|---|---|
| Platos bloqueados | Lista de platos marcados como "Agotado" en la carta | Lista | Visible de inmediato en RF01 | Sí |

**Flujo básico**

| Paso | Actor | Descripción | Excepciones |
|---|---|---|---|
| 1 | Sistema | Detecta que el stock de un insumo de pesca llegó a 0 | Insumo no vinculado a ningún plato |
| 2 | Sistema | Identifica todos los platos que dependen de ese insumo | — |
| 3 | Sistema | Marca esos platos como "Agotado" en la carta digital | — |

**Flujo alterno**

| Paso | Actor | Descripción | Excepciones |
|---|---|---|---|
| A1 | Sistema | Al reponerse el stock, desbloquea automáticamente los platos afectados | — |

**Reglas de negocio (propia del concepto)**

| No. | Descripción |
|---|---|
| 1 | Un plato que use un insumo de pesca en 0 unidades no puede agregarse a ningún pedido nuevo hasta que se reponga el stock. |

---

# REQUERIMIENTOS NO FUNCIONALES

<a id="rnf01"></a>
## RNF01 — Seguridad

| Campo | Detalle |
|---|---|
| **Código** | RNF01 |
| **Categoría** | Seguridad |
| **Descripción** | Control de acceso por rol (cliente, mesero, cocina, administrador). |
| **Aplicación** | Cada rol solo puede ver y ejecutar las funcionalidades correspondientes a su perfil (ej. cocina no accede a reportes de ventas). |

---

<a id="rnf02"></a>
## RNF02 — Rendimiento

| Campo | Detalle |
|---|---|
| **Código** | RNF02 |
| **Categoría** | Rendimiento |
| **Descripción** | El tablero de cocina refleja un pedido nuevo en menos de 2 segundos. |
| **Aplicación** | Aplica al flujo de confirmación de pedido (RF03) y su envío al KDS. |

---

<a id="rnf03"></a>
## RNF03 — Disponibilidad

| Campo | Detalle |
|---|---|
| **Código** | RNF03 |
| **Categoría** | Disponibilidad |
| **Descripción** | El sistema opera durante todo el servicio sin reinicios (12 horas continuas). |
| **Aplicación** | Aplica a todos los módulos: carta digital, KDS, cuentas y reportes. |

---

<a id="rnf04"></a>
## RNF04 — Usabilidad

| Campo | Detalle |
|---|---|
| **Código** | RNF04 |
| **Categoría** | Usabilidad |
| **Descripción** | Un cliente nuevo completa su primer pedido en máximo 4 pantallas. |
| **Aplicación** | Aplica al flujo de consulta de carta (RF01) y personalización de pedido (RF02). |

---

<a id="rnf05"></a>
## RNF05 — Auditabilidad

| Campo | Detalle |
|---|---|
| **Código** | RNF05 |
| **Categoría** | Auditabilidad |
| **Descripción** | Todo cambio de estado de un pedido queda registrado con usuario y hora. |
| **Aplicación** | Aplica directamente al flujo de cambio de estado en cocina (RF04). |

---

<a id="rnf06"></a>
## RNF06\* — Confiabilidad

| Campo | Detalle |
|---|---|
| **Código** | RNF06\* |
| **Categoría** | Confiabilidad (regla de negocio propia del concepto) |
| **Descripción** | El bloqueo por agotamiento de stock se propaga a todos los clientes conectados en menos de 3 segundos. |
| **Aplicación** | Aplica al bloqueo automático de platos por insumo agotado (RF08\*), asegurando consistencia en tiempo real en todas las sesiones activas. |

---

<a id="rnf07"></a>
## RNF07\* — Cumplimiento legal

| Campo | Detalle |
|---|---|
| **Código** | RNF07\* |
| **Categoría** | Cumplimiento legal (regla de negocio propia del concepto) |
| **Descripción** | Toda venta debe generar una factura electrónica válida ante la DIAN antes de cerrar la cuenta. |
| **Aplicación** | Aplica directamente al cierre de cuenta y registro de pago (RF05). |

---

## ABREVIATURAS

| Abreviatura | Significado |
|---|---|
| RF | Requerimiento Funcional |
| RNF | Requerimiento No Funcional |
| KDS | Kitchen Display System (tablero digital de cocina) |
| DIAN | Dirección de Impuestos y Aduanas Nacionales (Colombia) |
| Itamae | Chef especializado en la preparación de sushi |

## HISTORIAL DE REVISIÓN

| Elaborado por | Aprobado por | Fecha | Descripción y Justificación de Cambios |
|---|---|---|---|
| Equipo DOSW | | DD/MM/AAAA | Versión inicial del documento, requerimientos funcionales y no funcionales de Sushi Craft. |

## PREGUNTAS DE ANÁLISIS
 
| ID | Pregunta de análisis |
|---|---|
| **a** | ¿Identifica algún requerimiento que deba detallarse más? ¿Cuál(es)? ¿Por qué? |
| **b** | ¿Existen requerimientos que se contradigan entre sí? ¿Cuál(es)? |
| **c** | Si tuviera que dar prioridad, ¿cuáles serían los 2 más importantes para una primera iteración? Justifique. |
| **d** | ¿Existe algún requerimiento que NO debería realizarse en el MVP? ¿Por qué? |
 
### a. Requerimientos que deben detallarse más
 
- **RF08\* (Bloquear platos por agotamiento de insumo):** falta especificar la relación entre platos e insumos (¿un plato puede tener varios insumos críticos? ¿todos deben agotarse o basta con uno?) y qué pasa con los pedidos que ya están en el KDS cuando el insumo se agota mientras se preparan.
- **RF07\* (Dividir pedido en tandas):** no queda claro si el límite de 6 unidades aplica por pedido completo, por mesa o por franja horaria, ni cómo se ordenan las tandas cuando llegan varios pedidos simultáneos a la estación del itamae.
- **RNF06\* (Confiabilidad — propagación en menos de 3 segundos):** falta definir el mecanismo técnico (ej. WebSockets, polling) y a qué clientes exactamente se propaga (¿solo a quienes tienen ese plato en su carrito, o a todos los dispositivos conectados?).
- **RNF01 (Seguridad):** falta detallar qué acciones específicas puede realizar cada rol (cliente, mesero, cocina, administrador), ya que "control de acceso por rol" es muy general.
### b. Requerimientos que se contradicen entre sí
 
Existe una tensión entre **RF04** y **RF07\***: RF04 establece que el estado de **un pedido** avanza de forma secuencial (RECIBIDO → EN PREPARACIÓN → LISTO → ENTREGADO), pero RF07\* divide ese mismo pedido en varias tandas independientes que se preparan por separado en la estación del itamae. Esto genera ambigüedad: si algunas tandas ya están LISTAS y otras siguen EN PREPARACIÓN, ¿cuál es el estado real del pedido completo? El documento debería aclarar si el estado se maneja por tanda o si existe una regla de agregación para el estado global del pedido.
 
### c. Los 2 requerimientos más importantes para la primera iteración
 
1. **RF02 — Personalizar pedido**
2. **RF03 — Confirmar y enviar pedido al KDS**
**Justificación:** estos dos requerimientos conforman el ciclo mínimo de valor del negocio: capturar lo que el cliente quiere comer y comunicarlo a cocina en tiempo real. Sin ellos no existe un sistema de pedidos funcional, independientemente de qué tan pulida esté la carta (RF01) o qué tan sofisticados sean los reportes (RF06). El resto de funcionalidades (cambio de estados, cierre de cuenta, reportes, reglas de negocio propias) dependen de que este flujo básico exista primero.
 
### d. Requerimiento que no debería ir en el MVP
 
**RF07\* (Dividir automáticamente un pedido de rolls en tandas de máximo 6 unidades)** no debería estar en el MVP. Es una optimización del flujo interno de la estación del itamae, no una funcionalidad indispensable para que el negocio opere: en una primera versión, la cocina puede organizar manualmente las tandas de preparación sin que el sistema lo automatice. Priorizarlo en el MVP consume esfuerzo de desarrollo que es mejor invertir en el ciclo básico de pedido–cocina–cobro (RF02, RF03, RF04, RF05).




