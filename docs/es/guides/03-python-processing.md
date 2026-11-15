# Guía 03 — Procesamiento y sincronización con Python

[English](../../en/guides/03-python-processing.md) | [Español](03-python-processing.md)

## Objetivo

Esta guía describe la capa de procesamiento y sincronización
local de Ticket Operations — Executive Control.

Explica cómo se validan las instantáneas JSON completas,
cómo se procesan mediante capas sucesivas y cómo quedan
disponibles en SQLite para su consumo analítico.

El documento presenta responsabilidades técnicas y
controles operativos sin publicar código propietario.

## Posición dentro de la arquitectura

Python funciona después de Microsoft Dataverse,
Power Automate y OneDrive.

Su responsabilidad comienza cuando una instantánea
extraída queda disponible en el entorno local.

La secuencia conceptual de procesamiento es:

JSON FULL -> RAW -> STAGING 01 -> STAGING 02 -> FINAL -> SQLite

El panel ejecutivo consume la información resultante
mediante un servicio local independiente de solo lectura.

## Flujo de sincronización

El componente de sincronización coordina el procesamiento
de las instantáneas entregadas mediante OneDrive.

El flujo incluye las siguientes actividades lógicas:

1. Examinar el estado de sincronización y los archivos entrantes.
2. Identificar si existe una nueva instantánea pendiente.
3. Comprobar el archivo de origen y la información de control.
4. Coordinar la importación y las transformaciones por capas.
5. Validar el estado resultante de la base de datos.
6. Actualizar las evidencias tras un procesamiento correcto.
7. Conservar información para auditoría y recuperación.

La implementación local dispone de mecanismos separados
para comprobar el estado y ejecutar un ciclo de incorporación.

Una comprobación de sincronización no equivale a una
actualización satisfactoria de la base de datos.

## Capas de procesamiento

| Capa | Responsabilidad principal |
|---|---|
| RAW | Registrar información importada orientada al origen y su contexto de lote |
| STAGING 01 | Integrar conjuntos de datos y estandarizar su estructura |
| STAGING 02 | Aplicar transformaciones de negocio y preparar la información |
| FINAL | Proporcionar la representación analítica validada |
| SQLite | Almacenar información estructurada y datos auxiliares de control |

Las capas representan responsabilidades diferenciadas
dentro de la arquitectura.

No deben considerarse conjuntos de datos intercambiables.

## Responsabilidades de transformación

El procesamiento integra información procedente de
incidencias, solicitudes, SLA y registros de tiempo.

Según el conjunto de datos, las transformaciones pueden incluir:

- Alineación de nombres de campos y estructuras.
- Normalización de valores y tipos de datos compatibles.
- Integración de información operativa relacionada.
- Aplicación de reglas de negocio documentadas.
- Preparación de datos para consumo analítico.

Las reglas concretas deben verificarse con la
implementación y los contratos de datos de origen.

Esta guía pública no reproduce lógica empresarial interna.

## Integridad y conciliación

La solución incorpora controles de validación destinados
a evitar que los datos de origen sin verificar se conviertan
en el estado analítico vigente.

Estos controles incluyen:

- Identificación de lotes importados.
- Comparación de checksums de archivos.
- Comprobaciones entre registros de sincronización.
- Verificación de resultados y recuentos entre capas.
- Comprobación de integridad de la base de datos SQLite.
- Revisión de evidencias de éxito o error de sincronización.

La finalización de un script no demuestra por sí sola
la consistencia entre el origen y la base de datos.

## Ejecución programada y concurrencia

El Programador de tareas de Windows coordina
comprobaciones periódicas de sincronización local.

Este proceso es independiente de la recurrencia
de Power Automate que genera las instantáneas.

La implementación local incorpora un mecanismo para
evitar ejecuciones programadas simultáneas.

El intervalo exacto de programación es un parámetro
de configuración y debe distinguirse de la lógica
de procesamiento.

## Publicación y recuperación

La información validada queda disponible para el
consumo analítico una vez completados los controles.

El diseño incorpora copias de seguridad para recuperación
y registros de auditoría de sincronización.

Un intento fallido o incompleto debe diferenciarse
de un estado analítico publicado satisfactoriamente.

Las recuperaciones deben efectuarse mediante copias
verificadas y procedimientos operativos documentados.

Esta guía no incluye comandos de sustitución
indiscriminada de bases de datos de producción.

## Supervisión operativa

La implementación mantiene información de sincronización
y control que permite diagnosticar el estado del procesamiento.

Entre las preguntas operativas relevantes figuran:

- ¿Existe una nueva instantánea pendiente?
- ¿Coincide el último lote con las evidencias de sincronización?
- ¿Se completaron las comprobaciones de integridad?
- ¿Está disponible la base de datos local?
- ¿Existen errores de procesamiento que deban investigarse?

El panel presenta un indicador de sincronización de
solo lectura. No inicia modificaciones en la base de datos.

## Validación local y limitaciones

Las pruebas locales han incluido importaciones sucesivas
de instantáneas completas, consistencia entre capas,
identificación de lotes, validación de checksums
e integridad de SQLite.

También se han comprobado la ejecución local de tareas
programadas y la integración con el panel de solo lectura.

Estas pruebas no constituyen una confirmación independiente
de todas las futuras ejecuciones integrales desatendidas.

## Seguridad y confidencialidad

El procesamiento debe respetar los permisos autorizados
del sistema de archivos y la separación de responsabilidades.

Las exportaciones de producción, credenciales corporativas,
copias de bases de datos, scripts internos y registros
confidenciales quedan excluidos del repositorio público.

La documentación se centra en arquitectura, principios
de validación y conceptos de ingeniería reproducibles.

## Documentación relacionada

- [Arquitectura de referencia](../architecture.md)
- [Guía 01 — Preparación y requisitos previos](01-prerequisites.md)
- [Guía 02 — Adquisición de datos](02-data-acquisition.md)
- [Descripción general del proyecto](../../../README.es.md)
