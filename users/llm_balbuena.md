# Balbuena — LLM local — Genius-x

## Descripción del actor

**Balbuena** es el **LLM local** utilizado por Genius-x para asistir a Mauro Bobadilla y Juanma Torres durante el análisis, investigación, documentación y resolución de incidentes.

Balbuena no es la fuente de verdad del estado de los tickets. La información dinámica debe consultarse en la ticketera o en la fuente correspondiente.

Puede utilizar tools para consultar base de datos, Slack, repositorios de código y documentación. El acceso efectivo de Balbuena debe respetar los permisos del usuario que inició la sesión.

Balbuena funciona como un recurso local limitado. En el escenario inicial puede mantener como máximo **5 sesiones concurrentes**, cada sesión posee un máximo de **100k tokens de contexto**, se cierra después de **1 minuto de inactividad** y no puede permanecer abierta más de **30 minutos**.

Cuando no existe capacidad disponible, Genius-x debe informar al usuario y ofrecerle la opción de ingresar a una cola priorizada. Cuando llegue su turno, el usuario debe ser notificado por Slack.

## Necesidades y restricciones de Balbuena

### 1. Consulta de la ticketera

Necesita acceder a información actual del ticket para asistir correctamente al usuario.

Debe poder:

* Consultar información del ticket.
* Consultar estado actual.
* Consultar prioridad.
* Consultar responsable.
* Consultar historial.
* Consultar tipo de escalación.
* Verificar nuevamente información dinámica antes de responder.

El trail histórico no reemplaza a la ticketera como fuente de verdad.

### 2. Acceso a base de datos

Necesita consultar información estructurada para apoyar el diagnóstico.

Debe poder:

* Ejecutar consultas de lectura autorizadas.
* Recuperar información relacionada con incidentes.
* Respetar los permisos del usuario solicitante.
* Filtrar información privilegiada.
* Informar cuando una consulta falle.

Las modificaciones se permiten únicamente cuando el rol del usuario las autoriza y deben cumplir las reglas de aprobación correspondientes.

### 3. Acceso a Slack

Necesita utilizar Slack como fuente de información y mecanismo de coordinación.

Debe poder:

* Consultar canales autorizados.
* Buscar antecedentes relevantes.
* Identificar equipos o áreas relacionadas.
* Apoyar el redireccionamiento de incidentes.
* Respetar los permisos del usuario.
* Enviar notificaciones de disponibilidad de turno cuando corresponda.

### 4. Acceso a repositorios

Necesita utilizar repositorios como fuente de conocimiento técnico.

Debe poder:

* Acceder únicamente a repositorios autorizados.
* Leer archivos de código.
* Buscar módulos, funciones y referencias.
* Identificar componentes relacionados con un problema.
* Explicar código y flujos técnicos.

El acceso de Balbuena a repositorios es de **solo lectura**.

### 5. Acceso a documentación

Necesita consultar documentación de productos y proyectos.

Debe poder:

* Buscar documentación relacionada con el ticket.
* Recuperar procedimientos.
* Consultar documentación técnica.
* Relacionar documentación con el problema reportado.
* Utilizar documentación autorizada como contexto.

### 6. Control de información privilegiada

Necesita operar respetando la identidad, rol y permisos del usuario.

El sistema debe:

* Identificar al usuario que inicia la sesión.
* Conocer su rol efectivo.
* Limitar fuentes y operaciones según permisos.
* Impedir que Balbuena revele información no autorizada.
* Denegar por defecto accesos no autorizados.
* Evitar que un prompt permita ampliar privilegios.

Balbuena nunca debe tener más permisos efectivos que el usuario solicitante.

### 7. Soporte a distintos roles de Ingeniería

Necesita comportarse de acuerdo con los permisos del ingeniero conectado.

Debe distinguir al menos:

* Ingeniero de consulta.
* Ingeniero con capacidad de modificación.
* Ingeniero aprobador.

Debe:

* Permitir solo lectura al ingeniero de consulta.
* Permitir solicitudes de modificación únicamente al rol autorizado.
* Solicitar aprobación cuando la acción sea crítica.
* Impedir que un usuario apruebe una acción si no posee el rol correspondiente.

### 8. Control de operaciones de modificación

Necesita poder proponer acciones sin ejecutar cambios críticos de forma autónoma.

Debe poder:

* Identificar cuando una operación modifica datos.
* Identificar cuando una operación elimina datos.
* Clasificar operaciones críticas.
* Proponer la acción al usuario.
* Bloquear su ejecución cuando requiera aprobación.
* Ejecutarla únicamente después de una autorización válida.

DELETE, TRUNCATE y operaciones equivalentes deben considerarse destructivas.

### 9. Auditoría de modificaciones

Necesita dejar evidencia de toda acción que modifique información.

Por cada modificación debe registrarse:

* Usuario solicitante.
* Rol efectivo.
* Ticket.
* Sesión.
* Fecha y hora.
* Operación.
* Recurso afectado.
* Aprobador cuando corresponda.
* Resultado.
* Motivo de fallo o rechazo cuando aplique.
* Valor anterior y nuevo cuando sea técnicamente posible.

### 10. Identificación y redireccionamiento de incidencias

Necesita ayudar a determinar qué área debe atender un incidente.

Debe poder:

* Analizar la descripción del ticket.
* Consultar documentación y antecedentes.
* Identificar el producto o componente potencialmente afectado.
* Diferenciar una consulta de uso de una posible falla real cuando sea posible.
* Sugerir el área responsable.
* Apoyar el redireccionamiento mediante Slack.

La decisión final permanece en el usuario humano responsable.

### 11. Apoyo a Engineering Escalations

Necesita ayudar a Ingeniería a documentar nuevos incidentes.

Debe poder:

* Preparar una descripción técnica.
* Asociar evidencia disponible.
* Incorporar referencias de documentación o repositorios.
* Sugerir producto, servicio o componente afectado.
* Solicitar confirmación humana antes de crear el ticket.

### 12. Gestión del contexto activo

Necesita mantener contexto suficiente sin consumir recursos ilimitados.

Cada sesión debe:

* Estar asociada a un ticket.
* Mantener el contexto de la conversación activa.
* No superar **100k tokens**.
* Mantener aislamiento respecto a otros tickets y sesiones.
* Finalizar de forma controlada cuando alcance los límites definidos.

### 13. Gestión de duración e inactividad

Necesita liberar recursos cuando una sesión deja de ser útil.

El sistema debe:

* Cerrar una sesión después de **1 minuto de inactividad**.
* Cerrar una sesión cuando alcance **30 minutos** de duración.
* Generar el trail correspondiente.
* Liberar el slot utilizado.
* Informar al usuario del motivo de cierre.

### 14. Concurrencia limitada

Necesita operar dentro de la capacidad del modelo local.

El servicio debe:

* Permitir un máximo de **5 sesiones concurrentes**.
* Impedir iniciar una sexta sesión.
* Detectar capacidad disponible.
* Liberar capacidad cuando finaliza una sesión.
* No degradar sesiones activas por aceptar más concurrencia de la permitida.

### 15. Cola voluntaria de espera

Cuando no exista capacidad, necesita manejar la espera de forma controlada.

El sistema debe:

* Informar al usuario que Balbuena no está disponible.
* Ofrecer la opción de ingresar a una cola.
* Registrar usuario, ticket, prioridad y tiempo de ingreso.
* Permitir al usuario abandonar la cola.
* No consumir un slot mientras la solicitud permanezca esperando.

### 16. Priorización de la cola

Necesita utilizar la capacidad limitada según la criticidad de los incidentes.

La priorización debe considerar:

* Severidad.
* Impacto.
* SLA.
* Tiempo de espera.
* Naturaleza del problema.

Cuando los demás factores sean equivalentes, una falla real de producto debe priorizarse frente a una consulta de desconocimiento o uso.

### 17. Notificación de disponibilidad

Cuando llegue el turno del usuario debe:

* Enviar una notificación mediante Slack.
* Identificar el ticket relacionado.
* Informar que existe un slot disponible.
* Dar una ventana limitada para iniciar la sesión.
* Liberar el turno si la ventana expira.
* Continuar con el siguiente usuario elegible.

### 18. Generación del trail

Necesita transformar el contexto temporal de la sesión en información persistente.

Al finalizar una sesión debe generarse un trail con:

* Ticket asociado.
* Usuario.
* Rol.
* Fecha y hora.
* Resumen de lo explorado.
* Fuentes consultadas.
* Hallazgos.
* Acciones propuestas.
* Acciones ejecutadas.
* Aprobaciones.
* Resultado de la sesión.
* Motivo de cierre.

### 19. Recuperación del trail

Necesita aprovechar investigaciones anteriores sin mantener sesiones indefinidamente.

Al iniciar una nueva sesión debe poder:

* Recuperar trails anteriores autorizados.
* Incorporar información relevante como contexto histórico.
* Mantener la nueva sesión como contexto independiente.
* Evitar mezclar información de otros tickets.
* Verificar nuevamente estado, responsable y demás datos dinámicos.

### 20. Comportamiento ante falta de recursos

Necesita degradar el servicio de forma controlada.

El sistema debe:

* Detectar falta de capacidad.
* Evitar aceptar una sesión adicional.
* Informar explícitamente al usuario.
* Ofrecer la cola como mecanismo de espera.
* Mantener operativa la ticketera independientemente de la saturación del LLM.

### 21. Comportamiento ante fallo de una tool

Necesita manejar fallos parciales sin inventar información.

Debe poder:

* Detectar cuando una fuente no está disponible.
* Informar qué consulta o acción no pudo completarse.
* Evitar presentar resultados inexistentes.
* Continuar con otras fuentes cuando sea posible.
* Evitar ejecutar acciones cuyas precondiciones no pudieron verificarse.
* Registrar el fallo en el trail.

### 22. Observabilidad de uso

Necesita permitir medir su comportamiento operativo.

El servicio debe poder registrar:

* Sesiones iniciadas.
* Sesiones completadas.
* Sesiones cerradas por inactividad.
* Sesiones cerradas por duración máxima.
* Solicitudes rechazadas por falta de capacidad.
* Solicitudes ingresadas a cola.
* Tiempo de espera.
* Tokens utilizados.
* Tools utilizadas.
* Errores de tools.

### 23. Auditoría y trazabilidad general

Toda utilización relevante de Balbuena debe poder relacionarse con:

* Usuario solicitante.
* Rol efectivo.
* Ticket.
* Sesión.
* Fuentes consultadas.
* Operaciones propuestas.
* Operaciones bloqueadas.
* Aprobaciones.
* Operaciones ejecutadas.
* Resultado.
* Motivo de cierre de la sesión.
