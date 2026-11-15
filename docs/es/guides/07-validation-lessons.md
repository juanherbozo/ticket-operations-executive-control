# Guía 07 — Validación y lecciones aprendidas

[English](../../en/guides/07-validation-lessons.md) | [Español](07-validation-lessons.md)

## Objetivo

Esta guía resume el enfoque de validación, los resultados observados
y las lecciones de Ticket Operations — Executive Control. Distingue
las comprobaciones respaldadas por pruebas locales de las condiciones
que todavía requieren una prueba operativa específica. No reproduce
exportaciones confidenciales, identidades de clientes ni código interno.

## Método de validación

La validación sigue el recorrido lógico desde la instantánea de origen
hasta la consulta ejecutiva. Cada etapa se evalúa por entradas,
salidas, trazabilidad e información de errores, y no únicamente por
la finalización de un script o la apariencia correcta de una página.

El principio central es separar completitud del origen, integridad
del proceso, salud de SQLite y corrección del informe. Son criterios
de aceptación relacionados, pero distintos.

## Clasificación de evidencias

| Clasificación | Interpretación |
|---|---|
| Comprobado localmente | Resultado observado en una ejecución local controlada |
| Configurado | Existe una tarea, un flujo o protección, pero esto no acredita un ciclo integral satisfactorio |
| Requiere confirmación | Prueba integral o escenario de error sin evidencia independiente de aceptación |

Esta clasificación evita presentar una capacidad técnica como una
garantía operativa que aún no se ha demostrado.

## Validación de entradas e instantáneas

El diseño utiliza instantáneas JSON completas para incidencias,
solicitudes, SLA y registros de tiempo. Su validación contempla
lectura, categorías esperadas, identificación de lotes, checksums
y recepción desde la ubicación de intercambio autorizada.

Las pruebas locales incluyeron importaciones sucesivas y comparación
de evidencias de procesamiento. Un checksum SHA-256 verifica la
identidad del archivo, no la completitud o corrección de la consulta.

La recurrencia en la nube todavía no se ha confirmado mediante un
ciclo integral y desatendido hasta la base de datos.

## Transformación y conciliación

El proceso Python separa RAW, STAGING 01, STAGING 02 y FINAL.
Las capas permiten identificar dónde se cargan, integran, transforman
y preparan los datos para los informes.

Las comprobaciones locales incluyeron consistencia de procesamiento
y conciliación de recuentos relevantes en importaciones sucesivas.
Las reglas internas de mapeo y los volúmenes operativos reales no
se publican en esta documentación.

Algunos registros de origen carecen de campos SLA. Su ausencia
no debe convertirse automáticamente en cumplimiento o en cero.

## SQLite, trazabilidad y publicación

Las pruebas locales incluyeron identificación de lotes, comparación
de checksums, integridad de SQLite y consulta de registros analíticos.
La persistencia conserva además evidencias auxiliares de proceso
y sincronización.

La integridad estructural de la base de datos no acredita por sí misma
la exactitud funcional. El comportamiento ante una publicación fallida
o una restauración tras interrupción requiere una prueba propia.

## Dashboard e informes

La interfaz ejecutiva consulta una API local de solo lectura e incluye
filtros, gráficos, presentación bilingüe, estado de sincronización
y capacidad de exportación a PDF.

El acceso local y el consumo de información analítica se han probado.
Conviene validar por separado la coherencia entre filtros y PDF,
etiquetas extensas, ambos idiomas, registros ausentes y errores de API.
Una funcionalidad implementada no equivale a una prueba exhaustiva.

## Programación y recuperación

Power Automate y el Programador de tareas de Windows tienen
activadores y dominios de error distintos. Una ejecución correcta en
la nube, un intento de sincronización y la publicación en SQLite
requieren supervisión individual.

Se han revisado la configuración y las comprobaciones locales.
El ciclo desatendido completo —extracción programada, entrega por
OneDrive, importación local y visualización actualizada— todavía
requiere confirmación independiente.

Una transferencia interrumpida, los reintentos, la restauración
de copias y las ejecuciones simultáneas deben probarse expresamente
antes de afirmar garantías de recuperación operativa.

## Desafíos de ingeniería

El proyecto abordó la integración de registros operativos heterogéneos,
la conservación de trazabilidad por lotes y la presentación de datos
útiles para una revisión ejecutiva sin permitir modificaciones desde
la capa de visualización.

Otros desafíos fueron distinguir SLA ausentes, reducir el riesgo de
procesamiento duplicado y separar la entrega desde la nube de la
sincronización periódica local.

Son aspectos de diseño documentados, no afirmaciones inventadas
sobre incidencias concretas o pruebas exhaustivas de producción.

## Validaciones pendientes

Antes de afirmar una preparación operativa desatendida, comprobar:

1. La extracción programada entrega la instantánea esperada.
2. OneDrive hace accesible el archivo completo al proceso local.
3. La programación local procesa el lote previsto con trazabilidad.
4. SQLite y el estado de sincronización reflejan el lote aceptado.
5. El dashboard presenta la información actualizada correspondiente.
6. Los fallos, las restauraciones y los reintentos se comportan según lo documentado.

La lista describe trabajo de aceptación futuro, no pruebas finalizadas.

## Lecciones aprendidas

- Separar adquisición, procesamiento, persistencia y visualización para aislar fallos.
- Conservar la identidad del lote y las evidencias de integridad con los resultados.
- Conciliar los datos de negocio independientemente de la integridad estructural SQLite.
- Interpretar los campos SLA ausentes sin inventar valores.
- Distinguir las pruebas locales de una preparación desatendida aún no confirmada.
- Publicar arquitectura y lecciones sin divulgar identificadores ni registros corporativos.

## Documentación relacionada

- [Arquitectura de referencia](../architecture.md)
- [Guía 02 — Adquisición de datos](02-data-acquisition.md)
- [Guía 03 — Procesamiento y sincronización con Python](03-python-processing.md)
- [Guía 04 — Persistencia en SQLite e integridad de datos](04-sqlite-persistence.md)
- [Guía 05 — Dashboard ejecutivo y API de solo lectura](05-executive-dashboard.md)
- [Guía 06 — Automatización, programación y seguridad](06-automation-security.md)
- [Descripción general del proyecto](../../../README.es.md)
