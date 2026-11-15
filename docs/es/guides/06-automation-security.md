# Guía 06 — Automatización, programación y seguridad

[English](../../en/guides/06-automation-security.md) | [Español](06-automation-security.md)

## Objetivo

Esta guía describe los límites de automatización y las decisiones
de seguridad de Ticket Operations — Executive Control. Distingue una
programación configurada de una entrega efectivamente validada y
explica la coordinación entre extracción en la nube, sincronización
local y acceso analítico sin revelar configuraciones ni secretos.

## Arquitectura de programación

La solución utiliza dos programadores independientes:

| Mecanismo | Ámbito | Responsabilidad |
|---|---|---|
| Recurrencia de Power Automate | Nube | Obtener instantáneas autorizadas de Dataverse y entregar archivos JSON |
| Programador de tareas de Windows | Equipo local | Comprobar archivos nuevos e iniciar la sincronización controlada con Python |

Programar una tarea no demuestra que todos los procesos posteriores
se hayan completado. Cada límite requiere sus propias evidencias.

## Recurrencia en la nube

El flujo recurrente de Power Automate está configurado para ejecutarse
en días laborables y extraer instantáneas JSON completas. Su alcance
es obtener y entregar información autorizada a OneDrive, sujeto a
permisos, límites de los conectores y posibles errores.

Una ejecución satisfactoria del flujo solo acredita ese proceso; no
demuestra por sí misma que SQLite o el dashboard estén actualizados.

La programación es un parámetro de despliegue, no un compromiso
público de nivel de servicio.

## Comprobaciones de sincronización local

La programación de Windows ejecuta comprobaciones periódicas mediante
Python sobre la ubicación de intercambio sincronizada. La
implementación local ha utilizado un intervalo aproximado de diez
minutos como decisión de configuración.

El proceso comprueba si una instantánea requiere incorporación antes
de iniciar las transformaciones. Distingue una comprobación sin
novedades de una importación intentada y de un éxito verificado.

La recurrencia en la nube y el sondeo local son independientes:
sus horarios, errores y resultados deben evaluarse por separado.

## Entrega de instantáneas y estado

OneDrive proporciona el intercambio de archivos, no la base de datos
analítica. El ciclo lógico distingue Incoming, Processed y registros
de sincronización o auditoría.

Las evidencias relevantes incluyen lote de origen, lectura del
archivo, checksum SHA-256, resultado del procesamiento y última
sincronización satisfactoria. Un hash coincidente demuestra identidad
de archivo, no integridad funcional, completitud ni autorización.

La llegada de un archivo a Incoming no significa que ya esté
validado y publicado en SQLite.

## Procesamiento duplicado y concurrencia

El diseño local incluye protecciones frente a ejecuciones de
sincronización solapadas y reprocesamientos de una instantánea.
La identidad del lote y los checksums ayudan a tomar estas decisiones.

El comportamiento exacto ante un cierre inesperado, escritura
interrumpida o actualización concurrente debe contrastarse con el
código desplegado. No afirmamos entrega exactamente una vez ni
atomicidad distribuida sin evidencia.

## Supervisión y evidencias de auditoría

La supervisión debe correlacionar los siguientes eventos:

1. Ejecución del flujo en la nube y entrega del JSON.
2. Detección local de la instantánea candidata.
3. Comprobación de identidad y checksum del lote.
4. Resultado del procesamiento e integridad de SQLite.
5. Actualización de última ejecución satisfactoria y vigencia del panel.

El fallo de una etapa debe reflejarse como tal; una respuesta correcta
posterior de la API no debe ocultar la incidencia anterior.

## Gestión de errores

Entre los posibles fallos figuran errores de conectores, archivos
incompletos, demoras de OneDrive, cambios de estructura, errores
de procesamiento y problemas de integridad de la base de datos.
La recuperación debe identificar el lote y proteger un estado
analítico anterior que siga siendo utilizable.

Los reintentos deben evitar incorporaciones ambiguas o duplicadas.
Un reintento exitoso, la restauración de copias y la recuperación
tras interrupciones requieren pruebas específicas independientes.

## Secretos y mínimo privilegio

Las conexiones a Dataverse y Power Automate deben utilizar identidades
autorizadas y solo los permisos necesarios. Nunca deben publicarse
credenciales, tokens, cadenas de conexión o endpoints corporativos.

Los permisos de archivos locales deben proteger los JSON operativos,
SQLite, copias de seguridad y evidencias de sincronización. Esta
documentación describe componentes sin divulgar clientes ni cuentas.

## Límite de la API local

El dashboard consulta información analítica mediante un servicio HTTP
de Python limitado a la interfaz local de loopback. No debe permitir
modificar tickets, iniciar extracciones ni ejecutar mantenimiento
arbitrario de la base de datos.

Utilizar loopback reduce la exposición de red, pero no sustituye
la autenticación ni la autorización de la base de datos. Deben
revisarse por separado los endpoints, permisos y contenidos PDF.

## Límite de la validación

La implementación local se ha probado con importaciones manuales
sucesivas de instantáneas completas, checksums, trazabilidad de lotes,
comprobaciones de integridad SQLite y consulta de datos desde el panel.
También se ha revisado la programación local de comprobaciones.

Todavía falta confirmar independientemente una ejecución integral
desatendida, desde la recurrencia configurada en la nube hasta
la publicación en SQLite. Es una prueba de aceptación pendiente.

## Documentación relacionada

- [Arquitectura de referencia](../architecture.md)
- [Guía 02 — Adquisición de datos](02-data-acquisition.md)
- [Guía 03 — Procesamiento y sincronización con Python](03-python-processing.md)
- [Guía 04 — Persistencia en SQLite e integridad de datos](04-sqlite-persistence.md)
- [Guía 05 — Dashboard ejecutivo y API de solo lectura](05-executive-dashboard.md)
- [Guía 07 — Validación y lecciones aprendidas](07-validation-lessons.md)
- [Descripción general del proyecto](../../../README.es.md)
