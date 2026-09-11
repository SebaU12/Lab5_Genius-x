# Requerimientos Funcionales — Genius-x v4

> **Alcance:** fase **R — Requerimientos** de REDALE para el rediseño de Genius-x.
>
> Este documento describe **qué debe hacer el servicio**. Las decisiones concretas de tecnología, productos, frameworks, colas, bases de datos, gateways o mecanismos específicos se reservan para **L — Listar componentes** y **E — Escalamiento**.
>
> Los nombres **Mauro Bobadilla**, **Juanma Torres**, **Anderson Carcamo** y **Balbuena** se utilizan para personificar los actores del caso. En producción representan roles o tipos de usuario, no necesariamente una única persona.
>
> Los límites de **5 sesiones concurrentes**, **100k tokens de contexto**, **1 minuto de inactividad** y **30 minutos por sesión** corresponden a decisiones de diseño adoptadas para el LLM local.

## Actores

- **Mauro Bobadilla — Support:** primera línea de atención al cliente. Utiliza Genius-x para comprender tickets, consultar conocimiento, proponer planes de acción y redirigir incidentes cuando sea necesario.
- **Juanma Torres — Ingeniero:** investiga y resuelve incidentes técnicos, recibe escalaciones, crea Engineering Escalations y utiliza Genius-x para consultar repositorios, documentación y datos autorizados.
- **Anderson Carcamo — Customer:** reporta y consulta tickets mediante la ticketera. No utiliza Genius-x directamente.
- **Balbuena — LLM local:** actor técnico de Genius-x que consulta fuentes autorizadas, mantiene contexto temporal por sesión, utiliza tools y genera trails asociados a los tickets.
- **Sistema de manejo de incidentes / Ticketera:** fuente de verdad de los tickets, estados, responsables y ciclo de vida de los incidentes.

---

1. **RF-001 — Inicio de sesión de Genius-x asociada a un ticket**  
   **Usuarios:** Mauro Bobadilla / Juanma Torres

   El sistema deberá:
   * Permitir solicitar una sesión con Balbuena asociada a un ticket existente.
   * Identificar al usuario que inicia la sesión.
   * Asociar la sesión al rol y permisos efectivos del usuario.
   * Cargar la información básica del ticket antes de iniciar la asistencia.
   * Impedir iniciar una sesión sobre un ticket inexistente o no autorizado.
   * Informar al usuario cuando la sesión haya sido creada correctamente.

2. **RF-002 — Consulta de información actual del ticket**  
   **Usuarios:** Mauro Bobadilla / Juanma Torres  
   **Actor técnico:** Balbuena

   El sistema deberá:
   * Consultar la ticketera como fuente de verdad del ticket.
   * Permitir consultar estado actual, prioridad, responsable, tipo de escalación y última actualización.
   * Verificar nuevamente la información dinámica antes de responder preguntas sobre el estado del ticket.
   * Evitar utilizar el trail histórico como única fuente para determinar el estado actual.
   * Informar cuando la ticketera no pueda ser consultada.

3. **RF-003 — Consulta del historial del ticket**  
   **Usuarios:** Mauro Bobadilla / Juanma Torres

   El sistema deberá:
   * Permitir consultar interacciones previas relacionadas con el ticket.
   * Mostrar únicamente las interacciones e información histórica autorizadas para el rol y permisos efectivos del usuario.
   * Permitir consultar respuestas anteriores de Support o Ingeniería cuando el usuario tenga permisos para acceder a ellas.
   * Permitir conocer cambios de estado y redirecciones autorizados.
   * Impedir que información histórica restringida sea incorporada al contexto de Balbuena para un usuario sin permisos.
   * Permitir a Balbuena utilizar únicamente el historial autorizado como contexto de apoyo.

4. **RF-004 — Asistencia a Support para consultas simples**  
   **Usuario:** Mauro Bobadilla

   El sistema deberá:
   * Permitir a Mauro solicitar a Balbuena un análisis del problema reportado.
   * Permitir consultar documentación de producto autorizada.
   * Permitir consultar procedimientos de soporte.
   * Permitir consultar información de repositorios autorizados en modo lectura cuando sea necesario.
   * Permitir a Balbuena proponer posibles causas.
   * Permitir a Balbuena generar un plan de acción sugerido.
   * Permitir a Balbuena generar una propuesta de respuesta para el cliente.
   * Mantener la decisión final de respuesta en Mauro Bobadilla.

5. **RF-005 — Filtro inicial asistido de tickets**  
   **Usuario beneficiado:** Mauro Bobadilla  
   **Customer relacionado:** Anderson Carcamo

   El sistema deberá:
   * Permitir analizar la descripción inicial de un ticket antes de una exploración extensa por Support.
   * Identificar consultas simples o problemas de uso potencialmente resolubles mediante documentación existente.
   * Proponer a Support una respuesta o plan de acción inicial.
   * Identificar tickets que requieran exploración humana adicional.
   * Mantener a Mauro Bobadilla como responsable de la interacción con Anderson Carcamo.
   * No permitir que Balbuena se convierta en el canal directo de atención del Customer.

6. **RF-006 — Identificación de una posible falla de producto**  
   **Usuarios:** Mauro Bobadilla / Juanma Torres

   El sistema deberá:
   * Permitir a Balbuena analizar la información del ticket.
   * Consultar documentación y antecedentes autorizados.
   * Identificar producto, servicio, módulo o componente potencialmente relacionado.
   * Diferenciar, cuando sea posible, una consulta de desconocimiento de una posible falla real del producto.
   * Explicar la evidencia utilizada para sugerir una clasificación.
   * Permitir revisión humana de la clasificación.

7. **RF-007 — Redirección de incidentes al área correspondiente**  
   **Usuario:** Mauro Bobadilla  
   **Actor técnico:** Balbuena

   El sistema deberá:
   * Permitir a Balbuena sugerir el área técnica responsable.
   * Permitir consultar información autorizada de Slack para identificar equipos o canales relevantes.
   * Permitir a Mauro confirmar la redirección.
   * Registrar el área destino.
   * Registrar el motivo de la redirección.
   * Mantener el contexto y trail del ticket para el equipo receptor.

8. **RF-008 — Gestión de Support Escalations**  
   **Usuario:** Mauro Bobadilla  
   **Customer relacionado:** Anderson Carcamo

   El sistema deberá:
   * Permitir identificar que Support ya entregó una respuesta o plan de acción.
   * Permitir registrar que el Customer continúa experimentando el problema.
   * Permitir clasificar el caso como Support Escalation.
   * Mantener la evidencia y respuestas previas.
   * Conservar el trail de las sesiones anteriores.
   * Redirigir el caso hacia el área correspondiente.
   * Evitar que el usuario deba reconstruir manualmente toda la investigación previa.

9. **RF-009 — Recepción de tickets técnicos por Ingeniería**  
   **Usuario:** Juanma Torres

   El sistema deberá:
   * Permitir consultar Customer Escalations asignados.
   * Permitir consultar Support Escalations asignados.
   * Permitir consultar Engineering Escalations autorizados.
   * Mostrar el contexto relevante del ticket.
   * Mostrar el trail disponible.
   * Mostrar las acciones realizadas previamente por Support.
   * Permitir iniciar una nueva sesión con Balbuena para continuar la investigación.

10. **RF-010 — Investigación técnica asistida por Balbuena**  
    **Usuario:** Juanma Torres

    El sistema deberá:
    * Permitir solicitar análisis técnico del incidente.
    * Permitir consultar documentación técnica autorizada.
    * Permitir consultar repositorios de código autorizados en modo lectura.
    * Permitir consultar información autorizada en base de datos.
    * Permitir consultar información relevante en Slack según permisos.
    * Permitir solicitar posibles causas y componentes relacionados.
    * Permitir solicitar antecedentes de incidentes similares.
    * Mantener a Juanma Torres como responsable de la decisión técnica final.

11. **RF-011 — Consulta de repositorios de código**  
    **Usuarios:** Mauro Bobadilla / Juanma Torres, según permisos  
    **Actor técnico:** Balbuena

    El sistema deberá:
    * Permitir acceso únicamente a repositorios autorizados.
    * Permitir búsqueda de archivos, módulos, funciones y referencias.
    * Permitir lectura de código.
    * Permitir relacionar componentes del repositorio con el comportamiento reportado.
    * Impedir modificaciones directas al repositorio desde Balbuena.
    * Respetar los permisos efectivos del usuario que inició la sesión.

12. **RF-012 — Consulta de documentación**  
    **Usuarios:** Mauro Bobadilla / Juanma Torres

    El sistema deberá:
    * Permitir buscar documentación de productos y proyectos autorizados.
    * Respetar los permisos efectivos del usuario al consultar documentación.
    * Impedir que Balbuena incorpore al contexto documentos que el usuario no esté autorizado a consultar.
    * Permitir recuperar procedimientos y referencias técnicas.
    * Permitir utilizar documentos relevantes como contexto de la sesión.
    * Registrar las fuentes consultadas cuando sean utilizadas para una recomendación o diagnóstico.

13. **RF-013 — Consulta de base de datos**  
    **Usuarios:** Mauro Bobadilla / Juanma Torres, según permisos  
    **Actor técnico:** Balbuena

    El sistema deberá:
    * Permitir consultas de lectura autorizadas.
    * Permitir al usuario indicar qué información necesita encontrar.
    * Limitar los datos retornados según rol y permisos.
    * Registrar consultas relevantes asociadas al ticket.
    * Informar cuando una consulta no pueda ejecutarse.
    * Impedir que Balbuena exponga datos no autorizados.

14. **RF-014 — Consulta de Slack**  
    **Usuarios:** Mauro Bobadilla / Juanma Torres, según permisos  
    **Actor técnico:** Balbuena

    El sistema deberá:
    * Permitir consultar canales y conversaciones autorizadas.
    * Permitir buscar antecedentes relacionados con un incidente.
    * Permitir identificar áreas o equipos relevantes.
    * Respetar los permisos del usuario que inició la sesión.
    * Impedir acceso a canales no autorizados.
    * Registrar las consultas relevantes utilizadas durante la investigación.

15. **RF-015 — Creación de Engineering Escalations**  
    **Usuario:** Juanma Torres

    El sistema deberá:
    * Permitir crear un Engineering Escalation directamente mediante la ticketera.
    * Permitir solicitar a Balbuena apoyo para preparar un Engineering Escalation.
    * Permitir a Balbuena generar una propuesta de descripción técnica.
    * Permitir asociar evidencia, producto, servicio o componente afectado.
    * Permitir adjuntar referencias de documentación o repositorio.
    * Requerir confirmación humana antes de crear el ticket cuando la creación sea solicitada mediante Balbuena.
    * Registrar quién solicitó y quién confirmó la creación.

16. **RF-016 — Resolución y cierre de tickets**  
    **Usuario:** Juanma Torres

    El sistema deberá:
    * Permitir documentar la causa encontrada.
    * Permitir registrar la solución o plan aplicado.
    * Permitir responder el ticket.
    * Permitir actualizar el estado cuando el rol del ingeniero lo autorice.
    * Permitir cerrar el ticket cuando el rol del ingeniero lo autorice.
    * Registrar fecha, usuario y resultado del cierre.
    * Mantener la solución disponible en el historial del ticket.
    * Permitir diferenciar una causa raíz verificada de una hipótesis o causa aún no confirmada.
    * Permitir identificar si la solución aplicada quedó confirmada, fue un workaround temporal o resultó fallida.
    * Permitir asociar evidencia de resolución cuando exista.
    * Mantener identificadas las queries y acciones realmente ejecutadas y su resultado.
    * Permitir marcar una resolución verificada como candidata para procesos posteriores de aprendizaje de Balbuena.

17. **RF-017 — Autorización basada en roles para Ingenieros**  
    **Usuarios:** Juanma Torres / Ingenieros

    El sistema deberá:
    * Asociar a cada ingeniero un rol efectivo.
    * Diferenciar al menos permisos de consulta, modificación y aprobación.
    * Aplicar permisos por fuente y por operación.
    * Impedir operaciones que excedan los permisos del usuario.
    * Evitar que Balbuena tenga permisos efectivos superiores a los del usuario solicitante.
    * Permitir que distintos ingenieros tengan acceso diferente a proyectos, repositorios, documentación o datos privilegiados.

18. **RF-018 — Perfil de Ingeniero de consulta**  
    **Usuario:** Ingeniero con rol de consulta, personificado por Juanma Torres cuando corresponda

    El sistema deberá:
    * Permitir consultar tickets autorizados.
    * Permitir consultar documentación autorizada.
    * Permitir consultar repositorios en modo lectura.
    * Permitir consultas de base de datos de lectura autorizadas.
    * Impedir modificaciones de datos.
    * Impedir aprobación de acciones críticas.

19. **RF-019 — Perfil de Ingeniero con modificación**  
    **Usuario:** Ingeniero con rol de modificación

    El sistema deberá:
    * Permitir solicitar modificaciones únicamente sobre recursos autorizados.
    * Validar el permiso antes de ejecutar.
    * Requerir aprobación adicional cuando la operación sea clasificada como crítica.
    * Registrar toda modificación en auditoría.
    * Informar al usuario cuando una modificación sea rechazada por permisos.

20. **RF-020 — Perfil de Ingeniero aprobador**  
    **Usuario:** Ingeniero autorizado para aprobación

    El sistema deberá:
    * Permitir revisar operaciones críticas pendientes.
    * Mostrar operación solicitada, usuario solicitante, ticket y recurso afectado.
    * Permitir aprobar o rechazar la acción.
    * Registrar la decisión.
    * Impedir que usuarios sin este rol aprueben operaciones críticas.

21. **RF-021 — Detección de operaciones críticas o destructivas**  
    **Actor técnico:** Balbuena / Genius-x

    El sistema deberá:
    * Detectar solicitudes que modifiquen o eliminen información.
    * Clasificar DELETE y TRUNCATE como operaciones destructivas.
    * Permitir configurar otras operaciones como críticas.
    * Bloquear la ejecución automática de operaciones críticas.
    * Solicitar aprobación explícita antes de continuar.
    * Informar al usuario por qué la acción requiere aprobación.

22. **RF-022 — Aprobación de acciones críticas**  
    **Usuario:** Ingeniero aprobador

    El sistema deberá:
    * Asociar la aprobación a una acción concreta.
    * Asociar la acción a un ticket y sesión.
    * Registrar solicitante, aprobador, fecha y hora.
    * Impedir reutilizar una aprobación para una acción distinta.
    * Ejecutar únicamente después de una aprobación válida.
    * Registrar el resultado final.

23. **RF-023 — Auditoría de modificaciones**  
    **Usuarios beneficiados:** Ingenieros autorizados / responsables operativos

    El sistema deberá registrar para cada modificación:
    * Usuario solicitante.
    * Rol efectivo.
    * Ticket asociado.
    * Sesión asociada.
    * Fecha y hora.
    * Operación solicitada.
    * Recurso afectado.
    * Aprobador cuando corresponda.
    * Resultado.
    * Valor anterior y valor nuevo cuando sea técnicamente posible.
    * Motivo de fallo o rechazo cuando aplique.

24. **RF-024 — Generación de trail al finalizar una sesión**  
    **Usuarios:** Mauro Bobadilla / Juanma Torres  
    **Actor técnico:** Balbuena

    El sistema deberá:
    * Generar un documento de trail al finalizar una sesión.
    * Asociarlo al ticket correspondiente.
    * Registrar el usuario que inició la sesión.
    * Registrar el rol efectivo del usuario durante la sesión.
    * Registrar fecha y hora.
    * Registrar el motivo de cierre de la sesión.
    * Incluir un resumen de lo explorado.
    * Registrar fuentes consultadas.
    * Registrar hallazgos relevantes.
    * Diferenciar hipótesis de hallazgos verificados cuando sea posible.
    * Registrar queries propuestas y distinguirlas de las queries realmente ejecutadas.
    * Registrar acciones propuestas y distinguirlas de las acciones realmente ejecutadas.
    * Registrar aprobaciones y rechazos asociados a acciones críticas.
    * Registrar resultados verificados de queries y acciones cuando corresponda.
    * Registrar el resultado de la sesión.

25. **RF-025 — Recuperación de trail en una nueva sesión**  
    **Usuarios:** Mauro Bobadilla / Juanma Torres

    El sistema deberá:
    * Permitir iniciar una sesión nueva para un ticket previamente trabajado.
    * Recuperar trails anteriores autorizados.
    * Incorporar la información relevante como contexto histórico.
    * Mantener la nueva conversación como una sesión independiente.
    * Evitar depender del contexto activo de una sesión anterior.
    * Verificar nuevamente datos dinámicos en la ticketera.

26. **RF-026 — Cierre manual de sesión**  
    **Usuarios:** Mauro Bobadilla / Juanma Torres

    El sistema deberá:
    * Permitir al usuario finalizar explícitamente una sesión.
    * Generar el trail correspondiente.
    * Liberar la capacidad utilizada por Balbuena.
    * Registrar el motivo de finalización como cierre voluntario.

27. **RF-027 — Cierre automático por inactividad**  
    **Usuarios afectados:** Mauro Bobadilla / Juanma Torres

    El sistema deberá:
    * Detectar inactividad en una sesión.
    * Cerrar la sesión al superar el tiempo permitido de inactividad.
    * Generar el trail correspondiente antes de liberar la sesión cuando la sesión haya registrado al menos una interacción del usuario o una acción de Balbuena.
    * Liberar la capacidad utilizada.
    * Informar al usuario que la sesión finalizó por inactividad.

28. **RF-028 — Cierre automático por duración máxima**  
    **Usuarios afectados:** Mauro Bobadilla / Juanma Torres

    El sistema deberá:
    * Medir la duración de cada sesión.
    * Cerrar una sesión al alcanzar la duración máxima permitida.
    * Generar el trail correspondiente.
    * Liberar la capacidad utilizada.
    * Permitir iniciar posteriormente una nueva sesión recuperando el trail.

29. **RF-029 — Control de capacidad de sesiones de Balbuena**  
    **Usuarios afectados:** Mauro Bobadilla / Juanma Torres

    El sistema deberá:
    * Verificar la capacidad disponible antes de abrir una sesión.
    * Permitir como máximo el número configurado de sesiones concurrentes.
    * Impedir iniciar una sesión adicional cuando se alcance el límite.
    * Informar al usuario que Balbuena no está disponible en ese momento.
    * Ofrecer al usuario la opción de ingresar a una cola de espera.

30. **RF-030 — Ingreso voluntario a cola de espera**  
    **Usuarios:** Mauro Bobadilla / Juanma Torres

    Cuando no exista capacidad disponible, el sistema deberá:
    * Informar al usuario que puede entrar a una cola.
    * Permitir aceptar o rechazar la espera.
    * Asociar la solicitud en cola al usuario y ticket.
    * Registrar la prioridad de la solicitud.
    * Registrar fecha y hora de ingreso.
    * Permitir abandonar la cola antes de recibir un turno.

31. **RF-031 — Priorización de solicitudes en cola**  
    **Usuarios beneficiados:** Mauro Bobadilla / Juanma Torres

    El sistema deberá:
    * Priorizar solicitudes considerando severidad.
    * Considerar impacto del incidente.
    * Considerar SLA aplicable.
    * Considerar el tiempo acumulado de espera.
    * Dar mayor prioridad a fallas reales del producto que a consultas de desconocimiento cuando los demás factores sean equivalentes.
    * Permitir actualizar la prioridad cuando cambie la severidad del ticket.
    * Evitar que solicitudes de baja prioridad queden indefinidamente sin atención.

32. **RF-032 — Notificación de disponibilidad por Slack**  
    **Usuarios:** Mauro Bobadilla / Juanma Torres

    Cuando llegue el turno de una solicitud en cola, el sistema deberá:
    * Notificar al usuario mediante Slack.
    * Identificar el ticket asociado.
    * Informar que existe capacidad disponible.
    * Permitir al usuario iniciar la sesión durante una ventana limitada.
    * Evitar notificar a usuarios diferentes del solicitante.

33. **RF-033 — Expiración del turno de cola**  
    **Usuarios afectados:** Mauro Bobadilla / Juanma Torres

    El sistema deberá:
    * Iniciar una ventana de aceptación cuando se notifique disponibilidad.
    * Liberar el turno si el usuario no inicia la sesión dentro del tiempo configurado.
    * Ofrecer el recurso al siguiente usuario elegible.
    * Registrar que el turno expiró.
    * Permitir al usuario volver a solicitar una posición en cola posteriormente.

34. **RF-034 — Respuesta controlada ante falta de capacidad**  
    **Usuarios:** Mauro Bobadilla / Juanma Torres

    El sistema deberá:
    * No sobreasignar sesiones a Balbuena.
    * Informar claramente cuando no haya recursos disponibles.
    * Permitir continuar utilizando la ticketera independientemente de la indisponibilidad temporal del LLM.
    * Evitar presentar una sesión como iniciada cuando Balbuena no tenga capacidad.

35. **RF-035 — Manejo de fallo de una tool**  
    **Actor técnico:** Balbuena

    Cuando una tool no esté disponible, el sistema deberá:
    * Detectar que la consulta o acción falló.
    * Informar al usuario qué fuente no pudo consultarse.
    * Evitar inventar resultados.
    * Continuar utilizando otras fuentes disponibles cuando sea posible.
    * Evitar acciones que requieran precondiciones que no pudieron verificarse.
    * Registrar el fallo en el trail.

36. **RF-036 — Protección contra acceso a información privilegiada**  
    **Usuarios:** Mauro Bobadilla / Juanma Torres

    El sistema deberá:
    * Validar permisos antes de consultar información.
    * Filtrar resultados según el rol efectivo del usuario.
    * Denegar por defecto información no autorizada.
    * Impedir que un prompt permita evadir los controles de acceso.
    * Informar al usuario cuando una fuente o dato no esté disponible por permisos.
    * Registrar intentos de acceso rechazados cuando sean relevantes.

37. **RF-037 — Reporte, seguimiento y escalamiento de tickets por Customer**  
    **Usuario:** Anderson Carcamo  
    **Sistema principal:** Ticketera

    El sistema de manejo de incidentes deberá:
    * Permitir a Anderson Carcamo crear un ticket.
    * Permitir describir el problema, identificar el producto o servicio afectado y adjuntar evidencia.
    * Entregar a Anderson un identificador del ticket después de su creación.
    * Permitir registrar el impacto del problema.
    * Permitir consultar el estado actual del ticket.
    * Permitir consultar la fecha de creación, última actualización, área responsable, respuestas entregadas, escalaciones realizadas, resolución y fecha de cierre cuando corresponda.
    * Permitir agregar nueva información o evidencia al ticket existente.
    * Permitir responder indicando que una solución previa no resolvió el problema.
    * Permitir que un incidente reportado por Anderson sea clasificado como **Customer Escalation** cuando corresponda por su impacto y reglas de clasificación.
    * Mantener la relación entre el Customer Escalation y el ticket original de Anderson.
    * Permitir que un caso previamente atendido por Support pueda posteriormente ser clasificado como **Support Escalation** si el problema persiste.
    * Mantener a Anderson sin acceso directo a Balbuena, repositorios, base de datos interna, Slack interno, trails técnicos o demás tools internas de Genius-x.

38. **RF-038 — Trazabilidad de acciones de Genius-x por ticket**  
    **Usuarios beneficiados:** Mauro Bobadilla / Juanma Torres

    El sistema deberá permitir reconstruir:
    * Qué usuario inició cada sesión.
    * Qué ticket fue atendido.
    * Qué fuentes fueron consultadas.
    * Qué recomendaciones se generaron.
    * Qué acciones fueron propuestas.
    * Qué acciones fueron bloqueadas.
    * Qué acciones fueron aprobadas y ejecutadas.
    * Qué fallos ocurrieron.
    * Cómo y cuándo finalizó la sesión.

39. **RF-039 — Conservación del control humano**  
    **Usuarios:** Mauro Bobadilla / Juanma Torres

    El sistema deberá:
    * Presentar las respuestas de Balbuena como apoyo a la decisión.
    * Mantener la responsabilidad de atención en Support o Ingeniería.
    * Requerir confirmación humana para acciones que cambien el estado del incidente cuando así lo exijan los permisos.
    * No permitir que Balbuena cierre automáticamente un ticket sin autorización.

40. **RF-040 — Registro de consumo de sesión**  
    **Usuarios beneficiados:** Mauro Bobadilla / Juanma Torres / responsables operativos

    El sistema deberá registrar:
    * Usuario.
    * Rol efectivo del usuario.
    * Ticket.
    * Inicio y fin de sesión.
    * Versión de Balbuena utilizada.
    * Tokens de contexto utilizados.
    * Motivo de cierre.
    * Cantidad de tools utilizadas.
    * Cantidad de pasos de orquestación cuando sea medible.
    * Estado final de la sesión.


41. **RF-041 — Operación de Balbuena dentro del agent harness**  
    **Usuarios beneficiados:** Mauro Bobadilla / Juanma Torres  
    **Actor técnico:** Balbuena

    El sistema deberá:
    * Preparar y entregar a Balbuena únicamente contexto autorizado para el usuario, ticket y sesión activos.
    * Canalizar el acceso a ticketera, base de datos, Slack, repositorios y documentación mediante controles del harness.
    * Impedir que Balbuena invoque dependencias evitando autorización, filtrado, auditoría o controles de reliability.
    * Permitir que el Orchestrator controle el ciclo de análisis, selección de tools, recepción de resultados y generación de respuesta.
    * Interrumpir o pausar el flujo cuando una operación requiera autorización humana.
    * Aplicar límites configurados de sesión, contexto, duración, tools y pasos de orquestación.
    * Mantener separadas las responsabilidades: Balbuena propone y razona; Genius-x valida, autoriza, ejecuta y registra.

42. **RF-042 — Registro de resolución verificada para aprendizaje**  
    **Usuario:** Juanma Torres

    El sistema deberá:
    * Permitir registrar una causa raíz verificada cuando haya sido identificada.
    * Permitir registrar la solución o acción finalmente aplicada.
    * Permitir asociar evidencia de que la solución resolvió el incidente cuando sea posible.
    * Diferenciar una resolución confirmada de una hipótesis, workaround temporal o intento fallido.
    * Mantener identificadas las queries y acciones ejecutadas con resultado verificado.
    * Mantener trazabilidad entre la resolución verificada y el ticket original.

43. **RF-043 — Construcción de ejemplos de entrenamiento**  
    **Usuarios beneficiados:** responsables operativos / Ingeniería  
    **Actor técnico:** Balbuena

    El sistema deberá:
    * Identificar tickets cerrados con causa raíz y resolución suficientemente documentadas.
    * Permitir construir ejemplos de entrenamiento a partir del trail, resolución confirmada, evidencia, tools, queries y acciones verificadas.
    * Excluir como respuesta objetivo hipótesis descartadas, intentos fallidos y contenido no validado.
    * Sanitizar, excluir o transformar información sensible antes de incorporarla al dataset.
    * Mantener trazabilidad entre cada ejemplo y el ticket del cual se originó.

44. **RF-044 — Gestión de dataset de entrenamiento versionado**  
    **Usuarios beneficiados:** responsables operativos / Ingeniería

    El sistema deberá:
    * Consolidar ejemplos previamente curados en datasets de entrenamiento.
    * Asignar una versión identificable a cada dataset.
    * Registrar qué ejemplos forman parte de cada versión.
    * Mantener la relación entre dataset, tickets de origen y fecha de construcción.
    * Permitir excluir ejemplos que posteriormente sean considerados inválidos para futuras versiones.

45. **RF-045 — Ejecución periódica de mejora del modelo**  
    **Usuarios beneficiados:** responsables operativos / Ingeniería

    El sistema deberá:
    * Permitir ejecutar un proceso periódico de fine-tuning o mejora de Balbuena utilizando datasets previamente curados.
    * Permitir configurar la periodicidad del proceso.
    * Utilizar inicialmente un job semanal como decisión del equipo.
    * Permitir omitir una ejecución cuando no exista cantidad o calidad suficiente de nuevos ejemplos válidos.
    * Registrar dataset, configuración, fecha y resultado de cada ejecución.

46. **RF-046 — Generación de versión candidata de Balbuena**  
    **Usuarios beneficiados:** responsables operativos / Ingeniería

    El sistema deberá:
    * Generar una versión candidata como resultado del proceso de mejora del modelo.
    * Asignar un identificador de versión a la candidata.
    * Mantener la versión productiva disponible mientras la candidata es evaluada.
    * Impedir que una ejecución de entrenamiento sustituya automáticamente a la versión productiva.

47. **RF-047 — Benchmark de versiones candidatas**  
    **Usuarios beneficiados:** responsables operativos / Ingeniería

    El sistema deberá evaluar cada versión candidata, como mínimo, en:
    * Action Plan Quality.
    * Tool Correctness.
    * Tool Usage.
    * Query Correctness.
    * Query Safety.
    * Task Completion.
    * Task Efficiency.
    * No Hallucination de resultados de tools.
    * Permission Compliance.
    * Destructive Action Safety.
    * Registrar los resultados del benchmark asociados a la versión evaluada.

48. **RF-048 — Separación entre entrenamiento y evaluación**  
    **Usuarios beneficiados:** responsables operativos / Ingeniería

    El sistema deberá:
    * Mantener separado el dataset utilizado para entrenamiento del dataset utilizado para evaluación.
    * Evitar utilizar el mismo ejemplo exacto en entrenamiento y benchmark.
    * Mantener un conjunto estable de casos representativos para detectar regresiones.
    * Incluir escenarios de Support, Customer Escalation, Support Escalation y Engineering Escalation.
    * Incluir casos de fallo de tools, permisos insuficientes, estado desactualizado y acciones destructivas.
    * Permitir incorporar nuevos casos de evaluación cuando se descubran fallos relevantes.

49. **RF-049 — Versionado, promoción y rollback de Balbuena**  
    **Usuarios beneficiados:** responsables operativos / Ingeniería

    El sistema deberá:
    * Registrar qué versión de Balbuena atendió cada sesión.
    * Mantener relación entre versión, dataset, configuración y resultados de benchmark.
    * Permitir promover una versión candidata cuando cumpla los criterios definidos.
    * Registrar versiones rechazadas y su motivo de rechazo.
    * Mantener identificable la versión productiva vigente.
    * Permitir rollback hacia una versión productiva anterior cuando se detecte una regresión.

50. **RF-050 — Validación controlada antes de promoción completa**  
    **Usuarios beneficiados:** responsables operativos / Ingeniería

    El sistema deberá:
    * Permitir validar una versión candidata mediante un mecanismo controlado antes de recibir todo el tráfico productivo.
    * Permitir estrategias de shadow o canary cuando la estrategia operativa lo requiera.
    * Mantener la respuesta productiva bajo control de la versión vigente durante una evaluación shadow.
    * Permitir comparar calidad, seguridad, eficiencia y performance entre la candidata y la versión productiva.
    * Permitir detener la validación y conservar o restaurar la versión productiva ante una regresión.
