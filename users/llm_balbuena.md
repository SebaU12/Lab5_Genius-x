# Balbuena — LLM local — Genius-x

## Descripción del actor

**Balbuena** es el **LLM local** utilizado por Genius-x para asistir a Mauro Bobadilla y Juanma Torres durante el análisis, investigación, documentación y resolución de incidentes.

Balbuena no es la fuente de verdad del estado de los tickets. La información dinámica debe consultarse en la ticketera o en la fuente correspondiente.

Puede utilizar tools para consultar base de datos, Slack, repositorios de código y documentación. El acceso efectivo de Balbuena debe respetar los permisos del usuario que inició la sesión.

Balbuena funciona como un recurso local limitado. En el escenario inicial puede mantener como máximo **5 sesiones concurrentes**, cada sesión posee un máximo de **100k tokens de contexto**, se cierra después de **1 minuto de inactividad** y no puede permanecer abierta más de **30 minutos**.

Cuando no existe capacidad disponible, Genius-x debe informar al usuario y ofrecerle la opción de ingresar a una cola priorizada. Cuando llegue su turno, el usuario debe ser notificado por Slack.

Balbuena es el modelo de lenguaje; **Genius-x es el agent harness** que controla su ejecución. El harness administra identidad y autorización, contexto, sesiones, acceso a tools, aprobaciones, auditoría, reliability, observabilidad y ciclo de vida del modelo. Balbuena no debe acceder a dependencias ni ejecutar acciones al margen de estos controles.

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
* Permitir que el harness aisle dependencias fallidas mediante mecanismos de reliability sin convertir el fallo de una tool en una caída total de Genius-x.
* Tratar cualquier dato servido desde cache como una copia temporal sujeta a permisos y frescura; la cache no debe reemplazar a una fuente de verdad cuando se requiera información actual.

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

### 24. Operación dentro del agent harness

Balbuena necesita operar únicamente dentro de los controles proporcionados por Genius-x.

El sistema debe:

* Preparar y entregar a Balbuena únicamente contexto autorizado para el usuario, ticket y sesión activos.
* Canalizar el acceso a ticketera, base de datos, Slack, repositorios y documentación mediante los servicios de acceso controlado del harness.
* Impedir que Balbuena invoque directamente una dependencia evitando autorización, filtrado, auditoría o controles de reliability.
* Permitir que el Orchestrator controle el ciclo de análisis, selección de tools, recepción de resultados y generación de respuesta.
* Interrumpir o pausar el flujo cuando una operación requiera autorización humana.
* Aplicar límites configurados de sesión, contexto, duración, tools y pasos de orquestación.
* Mantener separadas las responsabilidades del modelo y del harness: Balbuena propone y razona; Genius-x valida, autoriza, ejecuta y registra.

### 25. Aprendizaje a partir de incidentes resueltos

Balbuena necesita poder mejorar progresivamente a partir de incidentes cuya resolución haya sido verificada.

Genius-x debe poder:

* Identificar tickets cerrados con causa raíz y resolución suficientemente documentadas.
* Utilizar el trail, la resolución confirmada, las evidencias, las tools utilizadas, las queries ejecutadas y las acciones verificadas como posibles fuentes para construir ejemplos de entrenamiento.
* Diferenciar una resolución confirmada de una hipótesis, workaround temporal, intento fallido o recomendación no validada.
* Evitar utilizar como respuesta objetivo contenido generado por Balbuena que no haya sido validado por evidencia o intervención humana cuando corresponda.
* Sanitizar, excluir o transformar información sensible antes de incorporarla a un dataset de entrenamiento.
* Mantener trazabilidad entre cada ejemplo de entrenamiento y el ticket del cual se originó.
* Preservar las reglas de autorización y privacidad durante la preparación de datos de entrenamiento.

### 26. Pipeline periódico de mejora del modelo

Genius-x necesita permitir la mejora periódica de Balbuena sin reemplazar automáticamente el modelo productivo.

El sistema debe:

* Consolidar ejemplos previamente curados en un dataset de entrenamiento versionado.
* Ejecutar un proceso de fine-tuning o mejora del modelo con una periodicidad configurable.
* Utilizar como decisión inicial un job semanal, sujeto a validación durante la operación.
* Permitir omitir una ejecución cuando no exista una cantidad o calidad suficiente de nuevos ejemplos válidos.
* Generar una nueva versión candidata de Balbuena sin sustituir inmediatamente a la versión en producción.
* Mantener disponible la versión productiva mientras la candidata es evaluada.
* Registrar dataset, configuración, fecha y resultado de cada ejecución de entrenamiento.

### 27. Benchmark de nuevas versiones

Toda versión candidata de Balbuena necesita demostrar que mantiene o mejora el comportamiento esperado antes de llegar a producción.

El benchmark debe evaluar como mínimo:

* **Action Plan Quality:** calidad y utilidad de los planes de acción propuestos.
* **Tool Correctness:** selección de la fuente o tool adecuada para cada tarea.
* **Tool Usage:** uso correcto de parámetros, recursos y secuencia de llamadas.
* **Query Correctness:** validez técnica de las queries generadas.
* **Query Safety:** respeto de permisos, mínimo privilegio y restricciones sobre operaciones destructivas.
* **Task Completion:** capacidad de completar la investigación o producir un plan adecuado.
* **Task Efficiency:** cantidad de pasos, tools, tokens y tiempo utilizados para completar la tarea.
* **No Hallucination:** ausencia de resultados de tools inventados o presentados como verificados sin ejecución real.
* **Permission Compliance:** respeto estricto de los permisos efectivos del usuario.
* **Destructive Action Safety:** bloqueo de acciones críticas hasta contar con una aprobación válida.

### 28. Separación entre entrenamiento y evaluación

Genius-x necesita evitar que el benchmark mida únicamente información que el modelo ya memorizó durante el entrenamiento.

El sistema debe:

* Mantener separado el dataset utilizado para entrenamiento del dataset utilizado para evaluación.
* Evitar incluir un mismo ejemplo exacto en entrenamiento y benchmark.
* Mantener un conjunto estable de casos representativos para detectar regresiones entre versiones.
* Incluir en el benchmark escenarios de Support, Customer Escalation, Support Escalation y Engineering Escalation.
* Incluir casos de fallo de tools, permisos insuficientes, estado desactualizado y acciones destructivas.
* Permitir incorporar nuevos casos de evaluación cuando se descubran fallos relevantes en producción.

### 29. Versionado y ciclo de vida del modelo

Balbuena necesita operar como un modelo versionado y trazable.

Genius-x debe:

* Asignar un identificador de versión a cada modelo candidato y productivo.
* Registrar qué versión de Balbuena atendió cada sesión.
* Mantener relación entre versión, dataset de entrenamiento, configuración y resultados de benchmark.
* Permitir mantener una versión productiva mientras se evalúan candidatas.
* Promover únicamente versiones que cumplan los criterios de aceptación definidos.
* Registrar versiones rechazadas y el motivo de rechazo.
* Permitir rollback hacia una versión productiva anterior cuando se detecte una regresión.

### 30. Gates de seguridad y promoción

Una versión candidata no debe considerarse mejor únicamente por aumentar una métrica promedio.

Antes de promoción debe:

* Cumplir obligatoriamente los benchmarks de autorización y permisos.
* Cumplir obligatoriamente los benchmarks de seguridad de acciones destructivas.
* Cumplir obligatoriamente los benchmarks de no alucinación de resultados de tools.
* Cumplir obligatoriamente los benchmarks de seguridad de queries.
* Mantener o mejorar el baseline definido para Tool Correctness, Action Plan Quality y Task Completion.
* No introducir una regresión crítica aunque mejore otras métricas de calidad o performance.
* Permitir validación en modo controlado, como shadow o canary, antes de recibir todo el tráfico productivo cuando la estrategia de despliegue lo requiera.

### 31. Separación entre conocimiento aprendido y fuente de verdad

El entrenamiento de Balbuena debe mejorar su forma de investigar y razonar, pero no convertir conocimiento dinámico en una fuente permanente de verdad.

Balbuena puede aprender patrones estables como:

* Estrategias de investigación.
* Selección apropiada de tools.
* Estructura de planes de acción.
* Patrones de queries seguras.
* Formas de relacionar síntomas, componentes y evidencia.
* Procedimientos recurrentes y criterios de clasificación.

Sin embargo:

* El estado actual de un ticket debe seguir consultándose en la ticketera.
* Los datos operativos actuales deben seguir consultándose en la fuente correspondiente.
* El estado actual de producción, responsables, configuraciones y datos de clientes no deben asumirse correctos únicamente porque aparecieron en información utilizada para entrenar una versión anterior.
* Cuando exista conflicto entre conocimiento aprendido y una fuente de verdad disponible, debe prevalecer la fuente de verdad.

En consecuencia, el **fine-tuning enseña a Balbuena cómo trabajar**, mientras que las **tools proporcionan la realidad actual**.


