# Mauro Bobadilla — Support — Genius-x

## Descripción del usuario

**Mauro Bobadilla** representa al equipo de **Support** y funciona como la primera línea humana de atención al cliente.

Su responsabilidad principal es recibir los problemas reportados por los clientes, comprender el contexto inicial y tratar de resolverlos antes de que requieran intervención de Ingeniería. Para esto puede apoyarse en **Balbuena**, el LLM local de Genius-x, como asistente de consulta, análisis, documentación y redirección.

Mauro puede utilizar Balbuena para resolver consultas de desconocimiento o uso, generar un plan de acción, revisar documentación, consultar antecedentes y explorar información autorizada. Cuando el problema parece corresponder a una falla real del producto, Balbuena puede ayudar a identificar el producto o componente afectado y sugerir el área técnica correspondiente.

Mauro no utiliza a Balbuena como sustituto de su criterio ni como mecanismo para ampliar sus permisos. Su acceso es principalmente de **consulta**, y toda información obtenida mediante el LLM debe respetar los permisos de Support.

Un caso pasa a **Support Escalation** cuando Support ya entregó una respuesta o plan de acción y el cliente informa posteriormente que el problema continúa sin resolverse.

## Necesidades de Mauro Bobadilla

### 1. Consulta y comprensión inicial del ticket

Necesita comprender rápidamente el problema reportado por el cliente.

Debe poder:

* Consultar la información registrada en el ticket.
* Conocer el estado actual del ticket.
* Consultar el historial de interacciones previas.
* Revisar evidencia asociada al incidente.
* Solicitar a Balbuena un resumen del ticket.
* Solicitar a Balbuena una explicación del problema.
* Identificar si el caso parece corresponder a desconocimiento, configuración, uso incorrecto o una posible falla real del producto.

### 2. Apoyo de Balbuena para consultas simples

Necesita utilizar a Balbuena para resolver problemas que no requieren intervención inmediata de Ingeniería.

Debe poder:

* Consultar documentación de productos.
* Consultar procedimientos de soporte.
* Consultar información autorizada en repositorios en modo lectura.
* Consultar antecedentes relevantes.
* Solicitar posibles causas.
* Solicitar pasos de diagnóstico.
* Solicitar un plan de acción sugerido.
* Solicitar una propuesta de respuesta para el cliente.
* Revisar la respuesta antes de enviarla al Customer.

### 3. Filtro inicial asistido

Necesita reducir el tiempo de atención de casos simples.

Genius-x debe poder apoyar a Mauro para:

* Analizar la descripción inicial del ticket.
* Identificar documentación conocida relacionada.
* Detectar consultas que parecen resolubles mediante conocimiento existente.
* Proponer una respuesta o plan de acción inicial.
* Indicar cuándo el caso requiere exploración humana adicional.
* Mantener a Mauro como responsable de la comunicación con el Customer.

### 4. Identificación de fallas de producto

Necesita distinguir cuándo un problema requiere investigación técnica.

Debe poder:

* Solicitar a Balbuena que analice la información del ticket.
* Comparar el comportamiento reportado con documentación existente.
* Consultar antecedentes similares.
* Identificar el producto, servicio o componente potencialmente afectado.
* Solicitar a Balbuena que sugiera el área técnica correspondiente.
* Revisar la evidencia utilizada para esa sugerencia.

### 5. Redirección al área correspondiente

Necesita transferir correctamente los casos que no pueden resolverse desde Support.

Debe poder:

* Conocer el área técnica sugerida.
* Utilizar información autorizada de Slack para ubicar al equipo correspondiente.
* Confirmar la redirección sugerida por Balbuena.
* Registrar el motivo de la redirección.
* Mantener el contexto relevante del ticket.
* Evitar que la investigación realizada por Support se pierda al pasar el caso a Ingeniería.

### 6. Gestión de Support Escalations

Necesita reconocer cuándo un problema previamente atendido debe escalar.

Debe poder:

* Consultar si el ticket ya recibió una respuesta de Support.
* Registrar que el cliente continúa experimentando el problema.
* Clasificar el ticket como Support Escalation.
* Mantener la respuesta o plan de acción entregado previamente.
* Mantener la nueva evidencia proporcionada por el cliente.
* Transferir el contexto acumulado hacia Ingeniería.

### 7. Uso del trail del ticket

Necesita conocer qué se investigó previamente para evitar repetir trabajo.

Debe poder:

* Consultar el trail asociado al ticket.
* Revisar sesiones anteriores realizadas con Balbuena.
* Conocer fuentes consultadas.
* Conocer hallazgos y planes de acción sugeridos.
* Conocer acciones propuestas o ejecutadas.
* Utilizar el trail como contexto histórico al iniciar una nueva sesión.

El trail no reemplaza la consulta del estado actual del ticket.

### 8. Uso de sesiones de Balbuena

Necesita utilizar el LLM dentro de la capacidad disponible del servicio.

Debe poder:

* Solicitar una sesión asociada a un ticket.
* Conocer si existe capacidad disponible.
* Finalizar una sesión cuando haya terminado la atención.
* Recibir un mensaje claro cuando los cinco slots estén ocupados.
* Elegir si desea ingresar a una cola de espera.
* Abandonar la cola si ya no necesita la sesión.
* Recibir una notificación por Slack cuando llegue su turno.
* Iniciar una nueva sesión utilizando el trail previo cuando corresponda.

### 9. Restricciones de acceso

Necesita acceder únicamente a información necesaria para su función.

El sistema debe:

* Aplicar los permisos asociados al rol de Support.
* Mantener el acceso de repositorios en modo lectura cuando esté autorizado.
* Limitar las consultas de base de datos a información autorizada.
* Limitar las consultas de Slack a canales autorizados.
* Impedir que Balbuena revele información privilegiada.
* Impedir que Mauro utilice al LLM para evadir controles de acceso.

### 10. Trazabilidad de su atención

Necesita que la atención realizada quede documentada.

Debe poder conocerse:

* Ticket atendido.
* Fecha y hora de cada sesión.
* Usuario que inició la sesión.
* Fuentes utilizadas.
* Recomendaciones relevantes.
* Área a la que se redirigió el ticket.
* Resultado de la atención.
* Conversión del caso a Support Escalation cuando corresponda.
