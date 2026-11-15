# Guía 02 — Adquisición de datos

[English](../../en/guides/02-data-acquisition.md) | [Español](02-data-acquisition.md)

## Objetivo

Esta guía documenta la capa de adquisición de datos de
Ticket Operations — Executive Control.

Explica cómo se obtiene la información operativa de Microsoft
Dataverse mediante Power Automate y OneDrive antes de pasar
al procesamiento local con Python.

Su finalidad es describir el enfoque de ingeniería sin
exponer conexiones corporativas ni registros confidenciales.

## Arquitectura de adquisición

La adquisición utiliza tres componentes principales:

| Componente | Responsabilidad |
|---|---|
| Microsoft Dataverse | Origen de información operativa de servicios |
| Power Automate | Extracción programada y preparación de instantáneas |
| Microsoft OneDrive | Intercambio de archivos entre la nube y el procesamiento local |

La sincronización con Python funciona después de esta capa.

## Orígenes de datos operativos

La solución utiliza cuatro categorías conceptuales:

- Incidencias.
- Solicitudes de servicio.
- Registros de acuerdos de nivel de servicio (SLA).
- Registros de tiempo.

Cada categoría aporta información para analizar la carga
operativa, el cumplimiento de tiempos de respuesta y el
esfuerzo registrado.

La extracción debe limitarse a los registros accesibles
mediante permisos expresamente autorizados.

## Estrategia de instantáneas completas

La solución utiliza instantáneas JSON FULL, en lugar de
depender exclusivamente de extracciones incrementales.

Cada instantánea representa la información devuelta por
el proceso de extracción durante una ejecución concreta.

Este enfoque permite sustituir de forma controlada el
estado analítico después de su validación.

Sin embargo, la existencia de un archivo no demuestra
por sí sola que la instantánea esté completa o sea válida.

Deben comprobarse por separado la cobertura del origen,
la estructura esperada y la consistencia de sincronización.

## Extracción mediante Power Automate

Power Automate coordina el proceso de adquisición en la nube.

Sus responsabilidades lógicas son:

1. Iniciar la extracción según una recurrencia configurada.
2. Consultar los orígenes autorizados de Dataverse.
3. Construir una instantánea JSON estructurada.
4. Entregarla a la ubicación designada de OneDrive.
5. Proporcionar el resultado de ejecución para seguimiento.

La extracción debe respetar los permisos del origen,
las capacidades de los conectores y los límites del servicio.

La programación recurrente está configurada.
Su funcionamiento integral y desatendido requiere
confirmación operativa independiente.

## Intercambio de archivos mediante OneDrive

OneDrive permite intercambiar información entre el flujo
en la nube y el entorno local de Python.

El diseño distingue:

- Instantáneas entrantes pendientes de procesamiento.
- Instantáneas procesadas conservadas para trazabilidad.
- Información de auditoría y estado de sincronización.

Las rutas corporativas concretas se omiten deliberadamente
en esta documentación pública.

La presencia de un archivo sincronizado no confirma
por sí sola que su incorporación haya finalizado correctamente.

## Transferencia al procesamiento con Python

La capa de adquisición termina cuando la instantánea
queda disponible para el proceso local de sincronización.

Python se encarga de los controles posteriores:
comprobación del estado de sincronización, validación
de archivos entrantes y coordinación de su incorporación.

RAW, STAGING 01, STAGING 02, FINAL y SQLite pertenecen
a las capas de procesamiento y persistencia, no al flujo
de Power Automate.

Esta separación evita atribuir todas las transformaciones
de datos al proceso de extracción en la nube.

## Automatización y programación

La arquitectura utiliza dos mecanismos independientes:

| Mecanismo | Responsabilidad |
|---|---|
| Recurrencia de Power Automate | Ejecutar la extracción desde la nube |
| Programador de tareas de Windows | Ejecutar comprobaciones locales de sincronización |

Ambas programaciones cumplen funciones distintas.

Una ejecución correcta en la nube no demuestra
automáticamente que la instantánea se haya procesado
y publicado satisfactoriamente en SQLite.

Cada etapa requiere supervisión y validación propias.

## Calidad de datos y trazabilidad

El diseño permite establecer la relación entre las
instantáneas de origen y la base de datos analítica.

Los controles posteriores incluyen:

- Identificación de instantáneas.
- Seguimiento de lotes de origen.
- Comparación de checksums.
- Registros de procesamiento y sincronización.
- Validación previa a la publicación del estado analítico.
- Procedimientos de recuperación y copias de seguridad.

Estos controles se ejecutan después de la adquisición
y no deben atribuirse exclusivamente a Power Automate.

## Seguridad y confidencialidad

La extracción debe utilizar identidades autorizadas
y permisos limitados a las necesidades del proceso.

Los secretos de conexión y las credenciales corporativas
deben permanecer fuera de los repositorios públicos.

Este caso de estudio no publica:

- Direcciones de entornos o identificadores corporativos.
- Material de autenticación o secretos de conectores.
- Exportaciones JSON operativas reales.
- Incidencias, solicitudes o registros de tiempo confidenciales.
- Copias sin restricciones de lógica interna de extracción.

## Alcance de la validación

La implementación local se ha probado mediante
importaciones sucesivas de instantáneas completas
y comprobaciones de consistencia posteriores.

La programación recurrente de Power Automate está configurada.

Un ciclo integral y desatendido, iniciado por la recurrencia
y completado con la publicación en SQLite, requiere
confirmación operativa independiente.

Así se distingue la automatización configurada de
una garantía de funcionamiento todavía no demostrada.

## Documentación relacionada

- [Arquitectura de referencia](../architecture.md)
- [Guía 01 — Preparación y requisitos previos](01-prerequisites.md)
- [Descripción general del proyecto](../../../README.es.md)
