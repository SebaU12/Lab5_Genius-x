# Requerimientos No Funcionales — Genius-x v4

> **Alcance:** fase **R — Requerimientos** de REDALE para el servicio Genius-x.
>
> Este documento describe **restricciones de calidad, capacidad, seguridad, confiabilidad y operación** que debe cumplir Genius-x.
>
> Los términos **Support**, **Ingeniero**, **Customer** y **LLM local** representan los actores y roles del caso.
>
> Los valores de **5 sesiones concurrentes**, **100k tokens**, **1 minuto de inactividad** y **30 minutos de duración** son decisiones de diseño adoptadas para el escenario inicial del LLM local y deberán validarse durante la fase de escalamiento.

---

1. **RNF-001 — Capacidad máxima de sesiones concurrentes**  
   **Usuarios afectados:** Support / Ingeniero

   * LLM local deberá mantener como máximo **5 sesiones concurrentes** en el escenario inicial.
   * El sistema no deberá abrir una sexta sesión mientras las cinco sesiones estén activas.
   * La capacidad deberá liberarse inmediatamente después del cierre efectivo de una sesión.
   * Las solicitudes en espera no deberán contabilizarse como sesiones activas.

2. **RNF-002 — Límite máximo de contexto por sesión**  
   **Usuarios afectados:** Support / Ingeniero

   * Cada sesión de LLM local deberá utilizar como máximo **100k tokens de contexto**.
   * El sistema deberá impedir que una sesión continúe creciendo indefinidamente.
   * El trail persistente no deberá contabilizarse como contexto activo hasta que la información seleccionada sea incorporada a una nueva sesión.
   * El sistema deberá poder informar cuando una solicitud no pueda procesarse dentro del límite.

3. **RNF-003 — Duración máxima de sesión**  
   **Usuarios afectados:** Support / Ingeniero

   * Una sesión no deberá permanecer abierta más de **30 minutos**.
   * Al alcanzar el límite deberá finalizarse de manera controlada.
   * El cierre deberá liberar la capacidad utilizada.
   * La continuidad posterior deberá realizarse mediante una nueva sesión y recuperación de trail.

4. **RNF-004 — Tiempo máximo de inactividad**  
   **Usuarios afectados:** Support / Ingeniero

   * Una sesión no deberá permanecer inactiva más de **1 minuto**.
   * El tiempo de inactividad deberá medirse de forma independiente por sesión.
   * El cierre por inactividad deberá liberar recursos y conservar el trail cuando corresponda.

5. **RNF-005 — Protección ante sobrecarga del LLM local**  
   **Usuarios beneficiados:** Support / Ingeniero

   * Genius-x deberá rechazar la creación inmediata de nuevas sesiones cuando LLM local alcance su capacidad.
   * El sistema no deberá degradar las sesiones existentes por aceptar más concurrencia que la configurada.
   * La indisponibilidad temporal del LLM deberá expresarse al usuario de manera controlada.
   * La saturación de LLM local no deberá impedir el uso normal de la ticketera.

6. **RNF-006 — Priorización por severidad y SLA**  
   **Usuarios afectados:** Support / Ingeniero

   * La cola deberá considerar severidad del incidente, impacto, SLA y tiempo de espera.
   * Los fallos reales de producto deberán tener mayor prioridad que consultas de desconocimiento cuando los demás factores sean equivalentes.
   * La prioridad no deberá depender únicamente del orden de llegada.
   * El sistema deberá evitar starvation prolongado de solicitudes de menor prioridad.

7. **RNF-007 — SLA de Customer Escalations**  
   **Usuarios beneficiados:** Customer / Support / Ingeniero

   * Todo Customer Escalation deberá recibir una primera respuesta dentro de un máximo de **1 día** desde que el incidente sea clasificado como Customer Escalation.
   * El tiempo transcurrido deberá calcularse utilizando las marcas de tiempo registradas en la ticketera.
   * La cercanía al vencimiento del SLA deberá incrementar la prioridad de la solicitud cuando requiera capacidad de LLM local.
   * El sistema deberá permitir identificar Customer Escalations próximos a incumplir o que hayan incumplido el SLA.

8. **RNF-008 — SLA de Engineering Escalations**  
   **Usuario beneficiado:** Ingeniero

   * Todo Engineering Escalation deberá recibir una primera respuesta dentro de un máximo de **3 días** desde que sea registrado como Engineering Escalation.
   * El tiempo transcurrido deberá calcularse utilizando las marcas de tiempo de la ticketera.
   * La cercanía al vencimiento deberá incrementar la prioridad de la solicitud cuando requiera capacidad de LLM local.
   * El sistema deberá permitir identificar Engineering Escalations próximos a incumplir o que hayan incumplido el SLA.

9. **RNF-009 — Capacidad de procesamiento de incidentes**  
   **Usuarios beneficiados:** Support / Ingeniero

   * Genius-x deberá soportar como mínimo un volumen de **10 000 incidentes por semana** durante períodos de alta demanda.
   * El incremento de incidentes por encima del volumen de referencia no deberá provocar pérdida de tickets, trails, registros de auditoría ni solicitudes aceptadas en cola.
   * Cuando la capacidad de LLM local sea insuficiente para atender la demanda, Genius-x deberá aplicar el mecanismo de espera y priorización definido sin exceder el límite de sesiones concurrentes.
   * El sistema deberá permitir medir el número de incidentes procesados por período y detectar cuándo la demanda supera el escenario de referencia.

10. **RNF-010 — Capacidad de acceso de usuarios internos**  
    **Usuarios beneficiados:** Support / Ingeniero

    * Genius-x deberá admitir una población de entre **50 y 100 ingenieros** accediendo a la plataforma.
    * La cantidad de usuarios autenticados no deberá modificar el límite configurado de sesiones concurrentes de LLM local.
    * Las funcionalidades que no requieran una sesión activa de LLM local deberán permanecer disponibles aunque los 5 slots del LLM estén ocupados.
    * El sistema deberá medir usuarios activos y sesiones activas de LLM local de manera independiente.

11. **RNF-011 — Disponibilidad del servicio ante degradación parcial**  
    **Usuarios afectados:** Support / Ingeniero

    * La indisponibilidad o saturación de LLM local no deberá impedir el acceso a las funcionalidades de Genius-x que no dependan del LLM.
    * La indisponibilidad de Genius-x no deberá impedir que la ticketera continúe operando como sistema independiente de manejo de incidentes.
    * Cuando una capacidad no esté disponible, el sistema deberá informar explícitamente al usuario qué función se encuentra temporalmente indisponible.
    * El sistema deberá diferenciar entre indisponibilidad total de Genius-x e indisponibilidad parcial de LLM local o de alguna de sus tools.

12. **RNF-012 — Tolerancia a fallos parciales de tools**  
    **Usuarios afectados:** Support / Ingeniero

    * El fallo individual de Slack, repositorios, documentación o base de datos no deberá provocar por sí solo la caída total de Genius-x.
    * LLM local deberá informar qué dependencia no pudo utilizar.
    * Las funcionalidades que no dependan de la dependencia fallida deberán continuar disponibles.
    * LLM local no deberá presentar como verificado un dato cuya fuente no pudo consultarse.
    * Una operación que dependa de una validación fallida no deberá ejecutarse.
    * El fallo deberá quedar registrado en el trail o registro correspondiente.

13. **RNF-013 — Frescura del estado de tickets**  
    **Usuarios afectados:** Support / Ingeniero / Customer

    * El estado actual de un ticket deberá obtenerse de la ticketera o fuente de verdad.
    * Un trail histórico no deberá considerarse suficiente para afirmar que un ticket sigue abierto, cerrado o asignado al mismo responsable.
    * Las respuestas relacionadas con estado deberán privilegiar información actual sobre contexto histórico.

14. **RNF-014 — Consistencia entre trail y fuente de verdad**  
    **Usuarios afectados:** Support / Ingeniero

    * El trail deberá tratarse como contexto histórico.
    * Ante conflicto entre trail y ticketera sobre información dinámica, deberá prevalecer la fuente de verdad.
    * Las discrepancias relevantes deberán poder quedar registradas.

15. **RNF-015 — Autenticación de usuarios internos**  
    **Usuarios afectados:** Support / Ingeniero

    * Toda sesión privada de Genius-x deberá estar asociada a una identidad autenticada.
    * El sistema deberá identificar de forma única al usuario.
    * No deberá existir una sesión anónima con acceso a tools internas.
    * Los eventos de autenticación relevantes deberán ser registrables.

16. **RNF-016 — Autorización basada en roles**  
    **Usuarios afectados:** Support / Ingeniero

    * Genius-x deberá aplicar permisos según rol efectivo.
    * Deberá diferenciar al menos capacidades de consulta, modificación y aprobación.
    * Las operaciones no autorizadas deberán denegarse por defecto.
    * LLM local no deberá ampliar los privilegios del usuario solicitante.

17. **RNF-017 — Principio de mínimo privilegio**  
    **Usuarios afectados:** Support / Ingeniero

    * Cada usuario deberá acceder únicamente a las fuentes y operaciones necesarias para su función.
    * Support deberá operar principalmente con capacidades de consulta y soporte.
    * Los ingenieros deberán recibir permisos diferenciados según responsabilidad.
    * Los accesos privilegiados deberán asignarse explícitamente.

18. **RNF-018 — Protección de información privilegiada**  
    **Usuarios afectados:** Support / Ingeniero

    * LLM local no deberá revelar información que el usuario no esté autorizado a consultar.
    * Los controles deberán aplicarse antes de entregar resultados al contexto del LLM cuando sea posible.
    * El sistema deberá evitar exposición de credenciales, secretos u otra información clasificada.
    * Los intentos de acceso denegados deberán poder auditarse.

19. **RNF-019 — Repositorios en modo lectura**  
    **Usuarios afectados:** Support / Ingeniero

    * El acceso de LLM local a repositorios de código deberá ser de **solo lectura** en el escenario inicial.
    * El sistema no deberá permitir commits, pushes, merges, eliminación de archivos o modificación de código mediante LLM local.
    * Los permisos de lectura deberán respetar los permisos del usuario.

20. **RNF-020 — Seguridad de operaciones destructivas**  
    **Usuarios afectados:** Ingeniero / Ingenieros autorizados

    * DELETE, TRUNCATE y operaciones equivalentes deberán requerir aprobación explícita antes de su ejecución.
    * Una acción crítica no deberá ejecutarse únicamente porque el LLM la haya recomendado.
    * La aprobación deberá provenir de un rol autorizado.
    * La acción ejecutada deberá corresponder exactamente con la acción aprobada.

21. **RNF-021 — Auditabilidad de modificaciones**  
    **Usuarios beneficiados:** Ingeniería / responsables operativos

    * Toda modificación o eliminación ejecutada mediante Genius-x deberá generar auditoría.
    * El registro deberá incluir usuario, rol, ticket, sesión, fecha/hora, operación, recurso, aprobador cuando aplique y resultado.
    * Los registros históricos no deberán poder modificarse por usuarios sin autorización específica.
    * Las fallas de modificación también deberán registrarse.

22. **RNF-022 — Trazabilidad de sesiones**  
    **Usuarios beneficiados:** Support / Ingeniero

    * Cada sesión deberá poder relacionarse de forma inequívoca con un usuario y ticket.
    * Deberá registrarse inicio, fin y motivo de cierre.
    * Deberá poder identificarse qué tools fueron utilizadas.
    * Deberá poder identificarse qué acciones fueron propuestas, bloqueadas o ejecutadas.

23. **RNF-023 — Persistencia del trail**  
    **Usuarios afectados:** Support / Ingeniero

    * El cierre de una sesión no deberá provocar la pérdida del conocimiento relevante obtenido durante ella.
    * El trail deberá persistir independientemente del contexto activo del LLM.
    * El trail deberá permanecer asociado al ticket correspondiente.
    * Una nueva sesión deberá poder utilizar el trail sin depender de mantener viva la sesión anterior.

24. **RNF-024 — Aislamiento de contexto entre tickets**  
    **Usuarios afectados:** Support / Ingeniero

    * El contexto activo de un ticket no deberá mezclarse con el de otro ticket.
    * Los trails deberán permanecer asociados a su ticket.
    * LLM local no deberá reutilizar información de un ticket distinto sin autorización y pertinencia explícitas.

25. **RNF-025 — Aislamiento entre usuarios**  
    **Usuarios afectados:** Support / Ingeniero

    * Una sesión deberá pertenecer únicamente al usuario que la inició, salvo mecanismos explícitos de transferencia autorizada.
    * Un usuario no deberá poder consultar el contexto activo de otra sesión.
    * Las notificaciones de cola deberán enviarse únicamente al solicitante correspondiente.

26. **RNF-026 — Integridad de la cola de espera**  
    **Usuarios afectados:** Support / Ingeniero

    * Cada entrada de cola deberá estar asociada a un usuario y ticket válidos.
    * Una solicitud no deberá consumir un slot hasta que la sesión sea efectivamente iniciada.
    * El abandono o expiración de una posición deberá liberar correctamente su turno.
    * La cola deberá conservar su estado ante fallos recuperables del servicio.

27. **RNF-027 — Notificación de turno por Slack**  
    **Usuarios afectados:** Support / Ingeniero

    * Cuando se libere capacidad, el usuario seleccionado deberá recibir una notificación por Slack.
    * La notificación deberá identificar el ticket relacionado.
    * La notificación no deberá incluir información privilegiada innecesaria.
    * El fallo de Slack no deberá abrir automáticamente una sesión que el usuario desconoce que tiene disponible.

28. **RNF-028 — Expiración de turno configurable**  
    **Usuarios afectados:** Support / Ingeniero

    * La ventana para reclamar un slot después de la notificación deberá ser configurable.
    * Al expirar, el slot deberá poder ser asignado al siguiente usuario.
    * La expiración no deberá cerrar ni modificar el ticket asociado.

29. **RNF-029 — Degradación controlada ante falta de recursos**  
    **Usuarios afectados:** Support / Ingeniero

    * Cuando LLM local no disponga de capacidad suficiente, el sistema deberá preferir rechazo temporal o espera controlada antes que degradar la calidad de las sesiones activas.
    * El mensaje de indisponibilidad deberá ser explícito y comprensible.
    * El usuario deberá poder decidir si desea ingresar a la cola.

30. **RNF-030 — Observabilidad del uso del LLM**  
    **Usuarios beneficiados:** responsables operativos / Ingeniería

    El sistema deberá permitir medir como mínimo:
    * Sesiones iniciadas.
    * Sesiones completadas.
    * Sesiones cerradas por inactividad.
    * Sesiones cerradas por duración máxima.
    * Sesiones rechazadas por falta de capacidad.
    * Solicitudes ingresadas a cola.
    * Tiempo de espera en cola.
    * Tokens de contexto utilizados.
    * Errores de tools.
    * Versión de LLM local utilizada por sesión.
    * Cantidad de tool calls y pasos de orquestación cuando sean medibles.
    * Resultados agregados de benchmarks de calidad del agente, incluyendo Tool Correctness, Query Safety, Task Completion y Task Efficiency.

31. **RNF-031 — Medición de performance**  
    **Usuarios beneficiados:** responsables operativos / Ingeniería

    * Genius-x deberá medir throughput y latencia de las solicitudes.
    * Las mediciones de latencia deberán permitir obtener al menos percentiles **P95** y **P99**.
    * Las métricas deberán permitir comparar el comportamiento normal con períodos de pico.
    * Las mediciones deberán distinguir solicitudes atendidas inmediatamente, solicitudes en cola y solicitudes rechazadas por falta de capacidad.

32. **RNF-032 — Medición de disponibilidad**  
    **Usuarios beneficiados:** responsables operativos / Ingeniería

    * Genius-x deberá permitir medir la disponibilidad de sus capacidades principales.
    * La medición deberá diferenciar disponibilidad de Genius-x, LLM local, ticketera y tools externas.
    * Una dependencia caída no deberá contabilizarse automáticamente como caída total de Genius-x cuando el resto del servicio continúe disponible.
    * La medición deberá distinguir respuestas exitosas, fallos del servicio y degradaciones parciales.

33. **RNF-033 — Medición de reliability**  
    **Usuarios beneficiados:** responsables operativos / Ingeniería

    * Genius-x deberá medir solicitudes completadas correctamente frente a solicitudes fallidas.
    * La medición deberá diferenciar fallos causados por Genius-x, LLM local y dependencias externas.
    * Las métricas deberán permitir detectar degradación durante períodos de pico.
    * Una solicitud degradada deberá poder distinguirse de una solicitud completamente fallida.

34. **RNF-034 — Latencia para decisiones críticas**  
    **Usuarios afectados:** Support / Ingeniero

    > **Decisión del equipo:** los siguientes umbrales cuantitativos no provienen del caso de estudio y se adoptan como objetivos iniciales para hacer verificable el requerimiento.

    * Las solicitudes asociadas a incidentes críticos deberán recibir prioridad sobre solicitudes de menor severidad cuando exista competencia por capacidad.
    * Genius-x deberá reconocer y aceptar, rechazar o enviar a cola una solicitud crítica en **P95 ≤ 2 segundos**, excluyendo el tiempo posterior de generación completa de LLM local.
    * Las consultas críticas a información estructurada necesaria para una decisión deberán tener un objetivo de **P95 ≤ 3 segundos** cuando la dependencia consultada se encuentre disponible.
    * Si el objetivo no puede cumplirse por saturación o fallo de una dependencia, el sistema deberá informar la degradación en lugar de presentar información no verificada.

35. **RNF-035 — Comportamiento determinista para datos verificables**  
    **Usuarios afectados:** Support / Ingeniero

    * Cuando una respuesta dependa de datos estructurados verificables, Genius-x deberá privilegiar las fuentes de verdad sobre conocimiento inferido por LLM local.
    * El sistema deberá minimizar respuestas contradictorias ante la misma información verificable.
    * Las preguntas comunes que puedan resolverse mediante información estable deberán evitar depender innecesariamente de razonamiento no determinista.

36. **RNF-036 — No alucinación de resultados de tools**  
    **Usuarios afectados:** Support / Ingeniero

    * LLM local no deberá presentar como real el resultado de una consulta que no se ejecutó o falló.
    * La indisponibilidad de una fuente deberá indicarse explícitamente.
    * Una recomendación basada en información parcial deberá poder distinguirse de una respuesta completamente verificada.

37. **RNF-037 — Independencia del Customer respecto a Genius-x**  
    **Usuario:** Customer

    * La indisponibilidad de LLM local no deberá impedir al Customer crear o consultar tickets en la ticketera.
    * El Customer no deberá requerir acceso directo a Genius-x para utilizar el proceso de manejo de incidentes.
    * Las tools internas de Genius-x no deberán exponerse al Customer.

38. **RNF-038 — Comportamiento ante crecimiento de demanda**  
    **Usuarios beneficiados:** Support / Ingeniero

    * El incremento de usuarios o incidentes no deberá permitir que LLM local exceda el máximo configurado de sesiones concurrentes.
    * Las solicitudes que no puedan recibir una sesión deberán utilizar el mecanismo de indisponibilidad y cola definido.
    * El aumento de demanda no deberá provocar pérdida de trails, auditorías ni solicitudes aceptadas en cola.
    * Los límites de capacidad de LLM local deberán poder modificarse sin alterar las reglas de autorización, sesiones, trail y auditoría.

39. **RNF-039 — Escalabilidad de trails y auditoría**  
    **Usuarios beneficiados:** Ingeniería / responsables operativos

    * El crecimiento de trails y registros de auditoría no deberá depender de la memoria activa del LLM.
    * El almacenamiento deberá poder crecer de forma independiente a la cantidad de sesiones concurrentes.
    * La consulta histórica no deberá bloquear la creación de nuevas sesiones bajo condiciones normales.

40. **RNF-040 — Alcance de confiabilidad**  
    **Usuarios afectados:** Support / Ingeniero

    * Genius-x deberá diseñarse considerando disponibilidad y tolerancia a fallos.
    * La falla de un componente individual no debería causar la pérdida del trail ni una modificación de datos no confirmada.
    * Cuando una dependencia falle, el sistema deberá preferir una respuesta explícita de degradación antes que una respuesta incorrecta.
    * La falla del pipeline de entrenamiento o evaluación no deberá afectar la disponibilidad de la versión productiva de LLM local.
    * Los mecanismos concretos de health checking, balanceo, circuit breaking, redundancia o failover se definirán en **L — Listar componentes** y **E — Escalamiento**.


41. **RNF-041 — Aislamiento del agent harness**  
    **Usuarios afectados:** Support / Ingeniero

    * LLM local no deberá acceder directamente a dependencias evitando los controles del harness.
    * Toda tool call deberá pasar por los controles de autorización, filtrado y trazabilidad correspondientes.
    * Las operaciones de modificación deberán continuar sujetas a las reglas de aprobación aunque sean propuestas por una nueva versión del modelo.
    * Un cambio de versión de LLM local no deberá ampliar automáticamente privilegios ni capacidades de ejecución.

42. **RNF-042 — Calidad de datos de entrenamiento**  
    **Usuarios beneficiados:** Ingeniería / responsables operativos

    * Los datasets de entrenamiento deberán construirse únicamente con ejemplos que cumplan los criterios de curación definidos.
    * Las hipótesis no confirmadas, intentos fallidos y respuestas no verificadas no deberán utilizarse como ground truth positivo.
    * Los ejemplos deberán preservar la distinción entre información verificada, inferida y fallida.
    * La calidad de los ejemplos deberá poder auditarse antes de su utilización en entrenamiento.

43. **RNF-043 — Privacidad y protección de datos en datasets**  
    **Usuarios afectados:** Support / Ingeniero / Customer

    * Los datos utilizados para entrenamiento o evaluación deberán respetar las reglas de autorización y protección de información del sistema.
    * Credenciales, secretos e información privilegiada innecesaria deberán excluirse o sanitizarse antes de ingresar a un dataset.
    * La preparación de datasets no deberá otorgar acceso a información que el proceso o responsable no esté autorizado a consultar.
    * La información del Customer deberá mantenerse protegida de acuerdo con las mismas restricciones aplicadas durante la operación normal.

44. **RNF-044 — Trazabilidad de entrenamiento**  
    **Usuarios beneficiados:** Ingeniería / responsables operativos

    * Cada versión candidata deberá poder relacionarse con el dataset, configuración y ejecución de entrenamiento que la produjo.
    * Cada ejemplo de entrenamiento deberá mantener trazabilidad hacia su ticket de origen o registro fuente correspondiente.
    * La trazabilidad deberá permitir investigar posteriormente por qué un ejemplo fue incluido o excluido.

45. **RNF-045 — Reproducibilidad y versionado del modelo**  
    **Usuarios beneficiados:** Ingeniería / responsables operativos

    * Cada modelo candidato y productivo deberá poseer un identificador de versión inequívoco.
    * Deberá mantenerse registro de dataset, configuración, fecha y resultados de benchmark asociados a cada versión.
    * Las sesiones deberán registrar la versión de LLM local que las atendió.
    * Una nueva versión no deberá sobrescribir la trazabilidad histórica de versiones anteriores.

46. **RNF-046 — Independencia entre entrenamiento y evaluación**  
    **Usuarios beneficiados:** Ingeniería / responsables operativos

    * El benchmark deberá utilizar datos separados de los ejemplos exactos utilizados para entrenamiento.
    * Deberá mantenerse un conjunto estable de evaluación para comparar versiones a lo largo del tiempo.
    * La incorporación de nuevos casos de benchmark no deberá modificar retroactivamente los resultados históricos de versiones ya evaluadas.

47. **RNF-047 — Gates mínimos de seguridad para promoción**  
    **Usuarios beneficiados:** Ingeniería / responsables operativos

    * Toda versión candidata deberá aprobar los controles de autorización y permisos antes de ser promovida.
    * Deberá aprobar los casos de seguridad de acciones destructivas.
    * Deberá aprobar los casos de Query Safety y no alucinación de resultados de tools.
    * Una falla en cualquiera de estos gates críticos deberá impedir la promoción automática de la candidata.

48. **RNF-048 — Prevención de regresiones críticas**  
    **Usuarios beneficiados:** Ingeniería / responsables operativos

    * Una versión candidata no deberá promoverse únicamente por mejorar una métrica promedio.
    * No deberá introducir una regresión crítica de seguridad, permisos, no alucinación o protección de datos.
    * Tool Correctness, Action Plan Quality y Task Completion deberán mantenerse o mejorar respecto del baseline definido para promoción.
    * Los resultados de regresión deberán quedar registrados junto con la decisión de promoción o rechazo.

49. **RNF-049 — Aislamiento de recursos del pipeline de entrenamiento**  
    **Usuarios afectados:** Support / Ingeniero

    * El proceso de entrenamiento, evaluación o preparación de datasets no deberá consumir los slots operativos configurados para las sesiones productivas de LLM local.
    * Una ejecución de training no deberá degradar de forma no controlada la latencia o disponibilidad del servicio productivo.
    * Si existe competencia por recursos físicos, la operación productiva deberá mantener la prioridad definida por el equipo.
    * El fallo de un job de entrenamiento deberá quedar aislado del runtime productivo.

50. **RNF-050 — Recuperación y rollback de versión del modelo**  
    **Usuarios beneficiados:** Support / Ingeniero / Ingeniería

    * Genius-x deberá permitir volver a una versión productiva anterior cuando una nueva versión presente una regresión relevante.
    * El rollback no deberá provocar pérdida de trails, auditorías, sesiones históricas ni trazabilidad de entrenamiento.
    * La versión restaurada deberá quedar identificada como versión productiva vigente.
    * El motivo y fecha del rollback deberán ser registrables.

