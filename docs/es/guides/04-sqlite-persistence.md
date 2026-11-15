# Guía 04 — Persistencia en SQLite e integridad de datos

[English](../../en/guides/04-sqlite-persistence.md) | [Español](04-sqlite-persistence.md)

## Objetivo

Esta guía describe la capa de persistencia de
Ticket Operations — Executive Control.

Explica cómo se almacena en SQLite la información
operativa validada y cómo se abordan la integridad,
la trazabilidad y el acceso analítico de solo lectura.

La finalidad es documentar el enfoque técnico sin
publicar datos confidenciales ni esquemas internos.

## Posición dentro de la arquitectura

SQLite funciona después de las capas de adquisición
y procesamiento con Python.

La secuencia lógica es:

Dataverse -> Power Automate -> OneDrive -> Python
-> RAW -> STAGING 01 -> STAGING 02 -> FINAL -> SQLite

El panel ejecutivo obtiene la información analítica
mediante una API local independiente de solo lectura.

SQLite no realiza la extracción en la nube y no debe
confundirse con el origen de datos de Dataverse.

## Responsabilidades lógicas de persistencia

La arquitectura contempla distintas responsabilidades:

| Responsabilidad | Descripción |
|---|---|
| Registros analíticos | Información preparada para los informes ejecutivos |
| Contexto de origen | Referencias a los conjuntos de datos importados |
| Trazabilidad de lotes | Identificación de la ejecución de procesamiento |
| Evidencias de validación | Resultados para evaluar la consistencia |
| Estado de sincronización | Información para determinar el estado del proceso |

Estas categorías son responsabilidades conceptuales,
no una lista publicada de nombres reales de tablas.

Algunos elementos de auditoría y sincronización también
pueden almacenarse fuera de la base de datos.

El esquema físico queda fuera del alcance público.

## Ciclo de la instantánea a la base de datos

El procesamiento local transforma instantáneas JSON
completas mediante una secuencia de etapas controladas.

El ciclo previsto es:

1. Recibir una instantánea de origen identificada.
2. Validar su lectura y estructura esperada.
3. Registrar el contexto del origen y del lote.
4. Procesar RAW y las capas sucesivas de STAGING.
5. Producir la representación analítica FINAL.
6. Ejecutar las comprobaciones de consistencia e integridad.
7. Dejar disponible la información validada en SQLite.
8. Registrar el resultado para supervisar la sincronización.

Finalizar una transformación no demuestra por sí solo
que el estado vigente de la base de datos sea válido.

Deben evaluarse conjuntamente el resultado de procesamiento
y el estado final de sincronización.

## Trazabilidad de lotes e identidad de archivos

La trazabilidad relaciona el estado analítico con
la instantánea de origen y la ejecución que lo generó.

Los identificadores relevantes incluyen:

- Identificación de la instantánea o lote de origen.
- Información de la ejecución de procesamiento.
- Checksum del archivo.
- Resultado y fechas asociadas a la sincronización.
- Evidencias de procesamiento correcto o fallido.

SHA-256 permite determinar con una confianza muy alta
si dos archivos tienen contenido idéntico.

Un checksum coincidente no demuestra que los datos
de origen sean completos, autorizados o correctos.

Los identificadores de lote y los checksums cumplen
funciones complementarias y no son intercambiables.

## Integridad y conciliación de datos

Las comprobaciones deben distinguir entre la salud
estructural de SQLite y la consistencia funcional.

Las categorías de validación incluyen:

- Lectura y estructura de la instantánea.
- Disponibilidad de conjuntos de datos esperados.
- Conciliación de recuentos relevantes.
- Consistencia de información operativa relacionada.
- Correspondencia de lotes y checksums.
- Integridad interna de SQLite.

SQLite dispone de una instrucción nativa de verificación:

```sql
PRAGMA integrity_check;
```

Este comando se muestra como referencia técnica general.
No sustituye la conciliación funcional ni determina
qué comprobaciones ejecutó una operación concreta.

Una verificación satisfactoria de integridad interna
no demuestra que todos los registros del origen
se hayan extraído correctamente.

## Consistencia de publicación

La persistencia debe evitar que un procesamiento
incompleto o inválido se presente como estado vigente.

SQLite permite realizar cambios transaccionales
dentro de la propia base de datos.

Sin embargo, sus transacciones no convierten
automáticamente en atómico un proceso que involucra
archivos de OneDrive, Python, copias de seguridad
y metadatos de sincronización.

Un procedimiento sólido debe definir:

- Cuándo se acepta un lote procesado.
- Qué condiciones permiten informar de un éxito.
- Cómo actuar ante errores de escritura o validación.
- Cómo proteger el estado analítico anterior.
- Cómo reflejar el resultado en la sincronización.

La implementación exacta de publicación y reversión
debe comprobarse en el código desplegado; esta guía
no atribuye al sistema mecanismos atómicos no verificados.

## Copias de seguridad y recuperación

El diseño local contempla copias de seguridad
y mecanismos de recuperación.

Un procedimiento fiable debe identificar:

- Qué base de datos y archivos auxiliares se respaldan.
- La relación entre la copia y el lote de origen.
- Cómo se comprueba la integridad de la copia.
- Qué ocurre ante una interrupción del procesamiento.
- Cómo se valida el estado restaurado.

Copiar un archivo SQLite mientras está activo, sin
considerar el modo de registro y las escrituras
concurrentes, puede producir una copia inconsistente.

Debe utilizarse un procedimiento compatible con SQLite
y probar la restauración antes de confiar en la copia.

Este repositorio no incluye bases de datos
confidenciales ni credenciales de recuperación.

## Acceso de solo lectura desde el dashboard

El panel consulta la información mediante una API
local, sin ejecutar directamente extracciones
ni operaciones de mantenimiento de la base de datos.

La implementación utiliza un servicio limitado
a la interfaz local de loopback.

La separación prevista es:

| Componente | Responsabilidad |
|---|---|
| SQLite | Almacenar información analítica validada |
| API local | Servir resultados analíticos permitidos de solo lectura |
| Dashboard | Mostrar filtros, indicadores y visualizaciones |
| Procesamiento Python | Ejecutar sincronizaciones y actualizaciones |

Una interfaz visual de solo lectura no sustituye
los controles de acceso implementados en el servidor.

Los permisos de conexión y el comportamiento
de los endpoints deben revisarse por separado.

## Estado de validación operativa

Las pruebas locales han incluido importaciones sucesivas
de instantáneas completas, trazabilidad de lotes
y checksums, comprobaciones de integridad de SQLite
y consumo de datos desde el panel analítico.

El sistema dispone también de programaciones
configuradas en la nube y en el entorno local.

Una ejecución desatendida que comience con la recurrencia
en la nube y finalice con la publicación validada en
SQLite requiere confirmación integral independiente.

Los resultados observados respaldan las pruebas locales
descritas, no garantizan todas las ejecuciones futuras.

## Seguridad y confidencialidad

SQLite es un componente de persistencia local,
no un sistema de autenticación por sí mismo.

Entre las medidas de protección figuran:

- Restringir el acceso a la base de datos y sus copias.
- Mantener los secretos corporativos fuera de Git.
- Evitar exponer la API local a redes no confiables.
- Separar permisos de procesamiento y consulta.
- Excluir exportaciones y registros confidenciales
  de la documentación pública.

Esta guía omite deliberadamente nombres de tablas
internas, identificadores corporativos, registros
reales y copias de bases de datos de producción.

## Documentación relacionada

- [Arquitectura de referencia](../architecture.md)
- [Guía 01 — Preparación y requisitos previos](01-prerequisites.md)
- [Guía 02 — Adquisición de datos](02-data-acquisition.md)
- [Guía 03 — Procesamiento y sincronización con Python](03-python-processing.md)
- [Descripción general del proyecto](../../../README.es.md)
