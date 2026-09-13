# Customer — Genius-x

## Descripción del usuario

**Customer** es el cliente que experimenta un problema relacionado con un producto o servicio de la empresa.

Customer utiliza la **ticketera o sistema de manejo de incidentes** para reportar y consultar sus problemas. No utiliza directamente Genius-x, el LLM ni las tools internas.

Su reporte puede originar un Customer Escalation o puede ser atendido inicialmente por Support. Si Support entrega una respuesta o plan de acción y Customer informa posteriormente que el problema continúa, el caso puede convertirse en **Support Escalation**.

Genius-x participa de manera interna como apoyo a Support e Ingeniería. La indisponibilidad temporal del El LLM no debe impedir que Customer pueda seguir reportando o consultando tickets en la ticketera.

## Necesidades de Customer

### 1. Reporte de incidentes

Necesita informar un problema que está experimentando.

Debe poder:

* Crear un ticket.
* Describir el problema.
* Identificar el producto o servicio afectado.
* Adjuntar información relevante.
* Adjuntar evidencia cuando corresponda.
* Indicar el impacto del problema.
* Recibir un identificador del ticket.

### 2. Consulta del estado del ticket

Necesita conocer el progreso de su problema.

Debe poder consultar:

* Estado actual.
* Última actualización.
* Área responsable.
* Solicitudes de información pendientes.
* Respuesta entregada.
* Resolución.
* Estado de cierre.

La información mostrada debe provenir de la ticketera como fuente de verdad.

### 3. Atención inicial por Support

Necesita recibir una respuesta cuando el problema pueda resolverse desde la primera línea de atención.

El proceso debe permitir:

* Que Support revise el problema.
* Que Support solicite información adicional.
* Que Support utilice Genius-x como apoyo interno.
* Que Customer reciba una solución o plan de acción revisado por Support.
* Que la respuesta quede registrada en el ticket.

### 4. Análisis inicial asistido internamente

Necesita que los casos simples puedan ser identificados rápidamente.

Genius-x puede apoyar internamente a Support para:

* Analizar la descripción inicial.
* Identificar documentación conocida relacionada.
* Sugerir una respuesta o plan de acción.
* Indicar si el caso requiere exploración adicional.

LLM no se convierte por ello en el canal directo de atención de Customer.

### 5. Customer Escalation

Necesita que un incidente con impacto directo en negocio pueda ser tratado como Customer Escalation.

El sistema debe permitir:

* Identificar el caso como Customer Escalation.
* Mantener la relación con Customer.
* Registrar impacto y prioridad.
* Mantener trazabilidad.
* Derivarlo al área responsable cuando sea necesario.

### 6. Support Escalation

Necesita informar que el problema continúa después de haber recibido una respuesta de Support.

Debe poder:

* Actualizar el ticket existente.
* Informar que la solución propuesta no resolvió el problema.
* Adjuntar nueva evidencia.
* Conservar el historial previo.
* Permitir que el caso sea clasificado como Support Escalation.
* Evitar tener que repetir toda la información ya registrada.

### 7. Protección de información interna

Necesita acceder únicamente a información correspondiente a su incidente.

El sistema debe:

* Evitar mostrar información interna de Ingeniería.
* Evitar mostrar información privilegiada.
* No exponer consultas internas de base de datos.
* No exponer repositorios.
* No exponer conversaciones internas de Slack.
* No exponer trails internos que contengan información no destinada al Customer.

### 8. Independencia respecto a LLM

Necesita que su proceso de atención continúe aunque el LLM esté saturado o temporalmente indisponible.

El sistema debe:

* Permitir crear tickets independientemente de la capacidad del LLM.
* Permitir consultar tickets independientemente de la capacidad del LLM.
* Mantener Genius-x como herramienta interna para Support e Ingeniería.
* No requerir que Customer espere una sesión del LLM para registrar un incidente.

### 9. Trazabilidad de atención

Necesita que la evolución de su ticket sea consistente y verificable.

Debe poder conocerse:

* Fecha de creación.
* Cambios de estado.
* Solicitudes de información.
* Respuestas entregadas.
* Escalamientos realizados.
* Resolución final.
* Fecha de cierre.

