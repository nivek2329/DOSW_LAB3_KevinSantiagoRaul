# Laboratorio 3: Definición de Requerimientos, UML y Agile Scrum con Jira
**Asignatura:** Desarrollo y Operaciones de Software (DOSW)  
**Periodo Académico:** 2026-1  
**Repositorio:** DOSW_LAB3_KevinSantiagoRaul  

---

##  Integrantes del Equipo
* **Kevin Ángel** 
* **Santiago García** 
* **Raúl Morales** 

---

##  Objetivo del Laboratorio
Aplicar de manera integral las herramientas de definición, modelado y análisis de requerimientos de software (diagramas de contexto C4 Nivel 1 y diagramas de casos de uso UML 2.5), diseño de experiencia de usuario (Brand Kit, mockups de alta fidelidad y flujos de navegación accesibles), y herramientas de planeación y gestión ágil utilizando el framework Scrum y Jira Software Cloud.

---

## Concepto de Restaurante Elegido
* **Concepto:** **Restaurante Japonés & Barra de Sushi de Autor — *"Sushi Craft"***
* **Descripción del Concepto:** Restaurante inventado enfocado en alta gastronomía japonesa contemporánea. Cuenta con una barra de sushi artesanal con rolls personalizados armados a gusto del comensal (selección de proteína, base de arroz/quinoa, vegetales, envoltura y salsa teriyaki/ponzu de la casa), nigiris de pesca fresca y estación caliente de ramen y tempura.
* **Reglas de Negocio Propias del Concepto:**
  1. **01 (Preparación en Barra por Tandas / Lote Máximo):** Para garantizar la frescura y la temperatura óptima del arroz artesanal (*shari*) y los cortes de pescado, los rolls armados a pedido en la barra de sushi se preparan en **tandas de máximo 6 unidades por lote**. Si una orden en mesa supera las 6 unidades, el sistema las divide automáticamente en tandas de preparación secuenciales para la estación del *Itamae* (sushichef).
  2. **02 (Trazabilidad y Bloqueo por Pesca del Día Agotada):** Si el stock diario de pesca fresca (salmón noruego, atún rojo o anguila unagi) o alga nori llega a cero (0), todos los rolls y platos que dependan de ese insumo se marcan automáticamente como **NO DISPONIBLES** en la carta digital en tiempo real, bloqueando su adición a cualquier comanda.

---

## Bloque A: Preguntas Teóricas sobre Git y GitHub

### 1. ¿Qué es un Pull Request en GitHub?
Un **Pull Request ** es un mecanismo colaborativo fundamental en plataformas de control de versiones como GitHub que permite a un desarrollador proponer la integración de los cambios realizados en una rama de trabajo (usualmente una rama de funcionalidad o *feature branch*) hacia una rama principal o de integración (como develop o main).

El Pull Request proporciona un entorno dedicado para:
* **Revisión de Código:** Los pares revisan la calidad, legibilidad, cumplimiento de estándares y arquitectura del código propuesto.
* **Discusión Técnica:** Se pueden dejar comentarios línea por línea, solicitar cambios o sugerir optimizaciones.
* **Validación Automatizada:** Permite ejecutar pruebas unitarias, análisis estático de código (linters) y comprobaciones de integración continua antes de fusionar.
* **Trazabilidad:** Deja un registro histórico inmutable de por qué se hizo un cambio, quién lo aprobó y qué discusiones se suscitaron.

### 2. ¿Cómo se crea un Pull Request en GitHub?
El procedimiento estándar para crear un Pull Request consta de los siguientes pasos:
1. **Crear y trabajar en una rama secundaria:** Desde la rama base actualizada (develop), se crea una rama específica para la tarea:
   
   git checkout develop
   git pull origin develop
   git checkout -b feature/DOSW-01-project-structure
   
2. **Realizar commits y enviar la rama al repositorio remoto:**
   
   git add .
   git commit -m "feat: implementar estructura inicial del proyecto Maven y documentacion"
   git push origin feature/DOSW-01-project-structure
   
3. **Abrir el Pull Request en GitHub:**
   * Navegar a la página principal del repositorio en GitHub.
   * Seleccionar la pestaña **"Pull Requests"** y hacer clic en el botón verde **"New Pull Request"**.
   * Configurar la rama de destino (**base**) como develop y la rama de origen (**compare**) como la rama de la funcionalidad (feature/DOSW-...).
   * Redactar un título descriptivo y diligenciar la descripción detallando los cambios introducidos, problemas resueltos y criterios de aceptación cumplidos.
   * Asignar **Revisores (Reviewers)** del equipo y vincular los issues de Jira correspondientes.
   * Hacer clic en **"Create Pull Request"**.

### 3. ¿Cómo se aprueba un Pull Request en GitHub?
El proceso formal de revisión y aprobación involucra:
1. **Inspección de Cambios:** El revisor asignado accede a la pestaña **"Files changed"** del Pull Request, analizando el diff de líneas agregadas, modificadas o eliminadas.
2. **Ejecución y Verificación Local / CI:** El revisor comprueba que los linters y pruebas automáticas pasen satisfactoriamente.
3. **Emitir el Veredicto de Revisión:**
   * Hacer clic en el botón **"Review changes"** en la esquina superior derecha.
   * Redactar una retroalimentación constructiva.
   * Seleccionar una de las tres opciones disponibles:
     * **Comment:** Envía comentarios generales sin aprobar ni bloquear.
     * **Request changes:** Solicita correcciones obligatorias antes de permitir el merge.
     * **Approve:** Aprueba formalmente los cambios al verificar que cumplen con los estándares y requerimientos.
4. **Merge y Cierre:** Una vez obtenidas las aprobaciones requeridas , se realiza el **Squash and merge** o **Merge pull request** hacia develop y se elimina la rama de funcionalidad remota y local.

### 4. Bibliografía (Norma APA 7ma Edición)
* Chacon, S., & Straub, B. (2014). *Pro Git* (2nd ed.). Apress. https://git-scm.com/book/en/v2
* GitHub Docs. (2024). *About pull requests*. GitHub Documentation. https://docs.github.com/en/pull-requests/collaborating-with-pull-requests/proposing-changes-to-your-work-with-pull-requests/about-pull-requests
* Sommerville, I. (2019). *Ingeniería de Software* (10.ª ed.). Pearson Educación.
* Rubin, K. S. (2012). *Essential Scrum: A Practical Guide to the Most Popular Agile Process*. Addison-Wesley Professional.
