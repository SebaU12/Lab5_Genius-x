# Ingeniero — Genius-x

## Descripción del usuario

**Ingeniero** es el actor encargado de investigar y resolver incidentes técnicos.

Puede recibir Customer Escalations y Support Escalations redirigidos hacia Ingeniería y también puede originar **Engineering Escalations** cuando el propio equipo técnico detecta un problema para sí mismo o para otra área.

Ingeniero utiliza al **LLM** como asistente técnico. El El LLM puede ayudarlo a consultar documentación, repositorios de código en modo lectura, información autorizada en base de datos, Slack y el trail del ticket.

Ingeniero representa al actor técnico de Ingeniería. En la operación real pueden existir varios ingenieros con **roles diferentes**. Algunos pueden únicamente consultar información, otros pueden solicitar modificaciones y un subconjunto autorizado puede aprobar operaciones críticas.

El LLM no debe otorgar más privilegios que los que posea el ingeniero que inició la sesión.

## Necesidades de Ingeniero

### 1. Recepción y análisis de tickets técnicos

Necesita acceder a los tickets que requieren intervención de Ingeniería.

Debe poder:

* Consultar Customer Escalations asignados.
* Consultar Support Escalations asignados.
* Consultar Engineering Escalations autorizados.
* Revisar descripción, estado, prioridad y evidencia.
* Consultar historial de atención.
* Revisar el trail disponible.
* Conocer qué acciones realizó Support previamente.
* Iniciar una sesión con el LLM asociada al ticket.

### 2. Investigación técnica con el LLM

Necesita utilizar al LLM como apoyo durante el diagnóstico.

Debe poder:

* Solicitar análisis técnico del ticket.
* Preguntar por posibles causas.
* Consultar documentación técnica autorizada.
* Consultar repositorios autorizados en modo lectura.
* Consultar información autorizada en base de datos.
* Consultar información técnica en Slack según permisos.
* Solicitar antecedentes de incidentes similares.
* Solicitar una síntesis de los hallazgos.

### 3. Exploración de repositorios

Necesita localizar rápidamente componentes relacionados con un fallo.

Debe poder:

* Solicitar búsqueda de módulos relacionados con una funcionalidad.
* Consultar archivos de código.
* Localizar funciones, endpoints, servicios o componentes relacionados.
* Pedir explicaciones sobre flujos técnicos.
* Identificar posibles áreas de fallo.

LLM solo utiliza los repositorios en **modo lectura** y no modifica código.

### 4. Consulta de documentación

Necesita utilizar documentación técnica y de producto como parte del diagnóstico.

Debe poder:

* Buscar documentación de proyectos autorizados.
* Consultar procedimientos.
* Relacionar documentación con el comportamiento reportado.
* Utilizar referencias documentales como contexto de la investigación.
* Registrar las fuentes relevantes utilizadas.

### 5. Consulta de base de datos

Necesita consultar datos que ayuden a encontrar la causa del problema.

Debe poder:

* Solicitar consultas de lectura.
* Indicar qué información desea encontrar.
* Consultar registros relacionados con el ticket.
* Recibir únicamente información permitida por su rol.
* Conocer cuando una consulta no pudo ejecutarse.

### 6. Control de acceso por rol

Necesita que Genius-x respete los privilegios asignados a cada ingeniero.

El sistema debe distinguir al menos:

* **Ingeniero de consulta:** puede consultar fuentes autorizadas, pero no modificar datos ni aprobar acciones críticas.
* **Ingeniero con modificación:** puede solicitar modificaciones sobre recursos autorizados.
* **Ingeniero aprobador:** puede revisar y aprobar o rechazar operaciones críticas.

Debe además:

* Aplicar permisos por proyecto, repositorio, documentación, Slack y datos.
* Denegar por defecto operaciones no autorizadas.
* Evitar que LLM amplíe los permisos del usuario.

### 7. Solicitud de modificaciones

Cuando su rol lo permita, necesita poder solicitar cambios necesarios para resolver un incidente.

Debe poder:

* Solicitar una modificación sobre un recurso autorizado.
* Conocer si la operación puede ejecutarse directamente o requiere aprobación.
* Recibir una explicación cuando la operación sea bloqueada por permisos.
* Mantener la modificación asociada al ticket y a la sesión.
* Consultar el resultado de la operación.

### 8. Aprobación de operaciones críticas

Cuando posea el rol de aprobador, necesita controlar acciones de alto riesgo.

Debe poder:

* Revisar la operación solicitada.
* Conocer el usuario solicitante.
* Conocer el ticket y recurso afectados.
* Aprobar o rechazar la acción.
* Registrar su decisión.
* Impedir que una aprobación sea reutilizada para una acción diferente.

DELETE, TRUNCATE y otras operaciones clasificadas como destructivas deben requerir aprobación explícita.

### 9. Auditoría de modificaciones

Necesita poder reconstruir cualquier acción que haya modificado información.

Cada modificación debe registrar como mínimo:

* Usuario solicitante.
* Rol efectivo.
* Ticket asociado.
* Sesión asociada.
* Fecha y hora.
* Operación solicitada.
* Recurso afectado.
* Usuario aprobador cuando corresponda.
* Resultado.
* Motivo de fallo o rechazo cuando aplique.
* Valor anterior y valor nuevo cuando sea técnicamente posible.

### 10. Creación de Engineering Escalations

Necesita reportar incidentes detectados desde Ingeniería.

Debe poder:

* Crear un Engineering Escalation directamente en la ticketera.
* Solicitar a LLM apoyo para preparar un Engineering Escalation.
* Generar una descripción técnica sugerida.
* Asociar evidencia.
* Asociar producto, servicio o componente afectado.
* Incorporar referencias de documentación o repositorio.
* Confirmar humanamente la creación cuando esta haya sido preparada por LLM.
* Mantener trazabilidad del origen del ticket.

### 11. Resolución y cierre del ticket

Necesita registrar la solución encontrada y finalizar el incidente.

Debe poder:

* Documentar la causa encontrada.
* Registrar la solución o plan aplicado.
* Responder el ticket.
* Actualizar el estado cuando su rol lo autorice.
* Cerrar el ticket cuando su rol lo autorice.
* Asociar evidencia de resolución.
* Mantener la solución disponible para futuras consultas.

### 12. Uso del trail técnico

Necesita retomar investigaciones sin comenzar desde cero.

Debe poder:

* Consultar el trail del ticket.
* Recuperar hallazgos anteriores.
* Conocer repositorios y documentación revisados.
* Conocer consultas de base de datos realizadas.
* Conocer hipótesis descartadas.
* Conocer acciones aprobadas o rechazadas.
* Utilizar el trail como contexto histórico de una sesión nueva.

### 13. Uso de sesiones y cola

Necesita acceder a LLM respetando la capacidad del LLM local.

Debe poder:

* Solicitar una sesión asociada a un ticket.
* Conocer cuando no exista capacidad.
* Elegir si desea ingresar a la cola priorizada.
* Ser priorizado según severidad, impacto, SLA y tiempo de espera.
* Recibir una notificación por Slack cuando exista un slot disponible.
* Reclamar su turno dentro de la ventana definida.
* Iniciar una nueva sesión recuperando el trail correspondiente.

### 14. Auditoría y trazabilidad técnica

Necesita que las acciones realizadas con apoyo del LLM puedan reconstruirse.

Debe poder conocerse:

* Ticket asociado.
* Usuario y rol que inició la sesión.
* Fecha y hora.
* Fuentes consultadas.
* Consultas ejecutadas.
* Acciones propuestas.
* Acciones bloqueadas.
* Aprobaciones realizadas.
* Resultado de las modificaciones.
* Respuesta final del ticket.
* Fecha y responsable del cierre.

### 15. Validación de resolución para aprendizaje

Necesita que las resoluciones técnicas verificadas puedan contribuir a mejorar futuras investigaciones del LLM sin convertir hipótesis o respuestas no confirmadas en conocimiento de entrenamiento.

Debe poder:

* Registrar claramente la causa raíz verificada cuando haya sido identificada.
* Registrar la solución o acción finalmente aplicada.
* Asociar evidencia que permita determinar que la solución resolvió el incidente cuando sea posible.
* Diferenciar una resolución confirmada de una hipótesis, workaround temporal o intento fallido.
* Mantener identificadas las queries y acciones que realmente fueron ejecutadas y tuvieron un resultado verificado.
* Permitir que las resoluciones verificadas sean consideradas como fuente para construir datasets de mejora del LLM.
* Mantener la información sensible sujeta a las mismas reglas de autorización, protección y sanitización antes de cualquier uso para entrenamiento.
* Mantener trazabilidad entre la resolución registrada y el ticket original.

Ingeniero no entrena ni promueve directamente nuevas versiones del modelo. Su responsabilidad continúa siendo resolver y documentar correctamente el incidente; Genius-x utiliza posteriormente esa información dentro de un pipeline controlado de curación, entrenamiento y evaluación.



