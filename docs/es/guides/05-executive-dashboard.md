# Guía 05 — Dashboard ejecutivo y API de solo lectura

[English](../../en/guides/05-executive-dashboard.md) | [Español](05-executive-dashboard.md)

## Objetivo

Esta guía documenta la capa de presentación ejecutiva
de Ticket Operations — Executive Control.

Explica cómo se consulta la información validada de
SQLite mediante una API local de solo lectura y cómo
se presenta en un dashboard accesible desde el navegador.

La documentación se centra en arquitectura,
interpretación de información, usabilidad,
supervisión operativa y confidencialidad.

No se publican datos reales de clientes ni código
propietario del dashboard.

## Posición dentro de la arquitectura

El dashboard es la capa final de consulta del sistema.

El flujo lógico de información es:

Dataverse -> Power Automate -> OneDrive -> Python
-> SQLite -> API local de solo lectura -> Dashboard ejecutivo

El panel no ejecuta directamente extracciones en la nube,
importaciones JSON ni mantenimiento de la base de datos.

Su finalidad es facilitar el análisis operativo y
la revisión ejecutiva de información procesada
por las capas anteriores.

## API local y acceso a los datos

La implementación utiliza un servicio local limitado
a la interfaz de loopback para proporcionar información.

El dashboard consulta este servicio en lugar de
acceder directamente a SQLite desde el navegador.

Las responsabilidades se distribuyen de esta manera:

| Componente | Responsabilidad |
|---|---|
| SQLite | Almacenar información analítica validada |
| API local | Servir resultados analíticos permitidos de solo lectura |
| HTML | Estructurar la interfaz del dashboard |
| CSS | Definir el diseño y la presentación visual |
| JavaScript | Gestionar filtros, visualización e interacciones |

La API está destinada a la consulta analítica,
no a iniciar procesamientos ni modificar tickets.

Un servicio limitado al equipo local no constituye
automáticamente un mecanismo de autenticación.

## Alcance de la información ejecutiva

El dashboard proporciona una vista consolidada
para revisar la actividad operativa de servicios.

La información procede de conjuntos de datos
procesados de incidencias, solicitudes, SLA
y registros de tiempo.

La presentación puede organizarse alrededor de:

- Carga operativa y estado de los tickets.
- Prioridades y registros que requieren atención.
- Indicadores SLA cuando existen datos de origen.
- Esfuerzo registrado y actividad relacionada.
- Distribuciones y comparativas entre dimensiones disponibles.

El conjunto preciso de indicadores mostrados debe
corresponder a la interfaz implementada y validada.

Esta guía no inventa métricas ni resultados de negocio.

## Interpretación y gobierno de los KPI

Un indicador ejecutivo solo resulta útil cuando
se comprende su significado y procedencia.

Cada KPI debería tener una interpretación documentada:

| Dimensión | Pregunta de validación |
|---|---|
| Origen | ¿Qué conjunto procesado sustenta el indicador? |
| Población | ¿Qué registros se incluyen o excluyen? |
| Cálculo | ¿Qué agregación o regla de negocio se utiliza? |
| Periodo | ¿Qué fecha o intervalo se representa? |
| Datos ausentes | ¿Cómo se gestionan los valores no disponibles? |

La ausencia de un valor SLA no debe interpretarse
automáticamente como cumplimiento o incumplimiento.

La aplicación debe diferenciar los datos no disponibles
de los valores realmente iguales a cero.

Esta documentación no publica resultados numéricos de KPI.

## Filtros y exploración

El dashboard dispone de funciones interactivas
de filtrado y exploración para la revisión ejecutiva.

Los filtros deben utilizar el mismo estado analítico
validado que sustenta los indicadores y gráficos.

Una interacción fiable debe mantener la consistencia
entre totales visibles, visualizaciones relacionadas
y los criterios seleccionados.

Cuando corresponda, la interfaz debe distinguir:

- Ausencia de registros que cumplan los filtros.
- Información no disponible en el origen.
- Errores al consultar o representar los datos.

Son situaciones operativamente distintas y no deben
mostrarse como si fueran equivalentes.

## Interfaz bilingüe y visualizaciones

La implementación ofrece una presentación del
dashboard en inglés y español.

La interfaz combina resúmenes visuales,
gráficos y controles interactivos.

Seleccionar un idioma no debe modificar
los valores analíticos subyacentes.

Para comprobar la coherencia bilingüe es importante revisar:

- Terminología equivalente en ambos idiomas.
- Interpretación consistente de las etiquetas KPI.
- Presentación de fechas y valores numéricos.
- Legibilidad de leyendas y etiquetas de gráficos.
- Distinción clara entre títulos y valores de datos.

El diseño visual debe facilitar la detección
de registros que requieren atención, sin convertir
automáticamente cada alerta en un fallo de negocio.

## Exportación a PDF

El dashboard incluye una función para generar
una representación PDF de la información ejecutiva.

El documento exportado debe reflejar correctamente
la información que se desea comunicar.

Las comprobaciones relevantes incluyen:

- Encabezados claros y elementos visuales legibles.
- Idioma y contexto del informe correctos.
- Coherencia entre filtros y datos exportados.
- Legibilidad de gráficos, tablas y etiquetas extensas.
- Ausencia de información confidencial no prevista.

El mecanismo concreto de generación del PDF queda
fuera del alcance de esta descripción arquitectónica.

Generar un PDF no valida por sí mismo
la exactitud de los datos de origen.

## Estado de sincronización y actualidad de los datos

El dashboard incorpora un indicador de
sincronización de solo lectura.

Su propósito es proporcionar contexto sobre los datos
disponibles para la consulta ejecutiva.

Es necesario diferenciar los siguientes eventos:

- Se ha programado una extracción en la nube.
- Se ha entregado una instantánea de origen.
- Se ha intentado ejecutar la sincronización local.
- Un lote se ha procesado satisfactoriamente.
- Existe un estado analítico validado y disponible.

La disponibilidad del dashboard o una respuesta
correcta de la API no demuestran por sí solas
que ya se haya incorporado la última instantánea.

La actualidad de los datos debe evaluarse utilizando
evidencias de sincronización verificadas.

## Validación y situaciones de error

Las pruebas locales han incluido el procesamiento
analítico, la disponibilidad de SQLite y el consumo
de información desde el dashboard.

La implementación también incorpora filtros,
visualizaciones, interfaz bilingüe y exportación a PDF.

La ejecución integral desatendida, iniciada por una
extracción programada y finalizada con un nuevo
estado validado del dashboard, requiere confirmación
independiente.

La revisión operativa debe contemplar errores de API,
datos no disponibles, fallos de procesamiento
y estados de sincronización desactualizados.

La carga visual satisfactoria de la página no demuestra
la corrección integral de todo el sistema.

## Seguridad y confidencialidad

El dashboard está diseñado para consulta y no debe
exponer conjuntos de datos corporativos internos.

Las responsabilidades de seguridad incluyen:

- Mantener las actualizaciones fuera de la interfaz.
- Restringir adecuadamente el acceso a API y SQLite.
- No publicar credenciales ni datos de conexión.
- Revisar el contenido confidencial de los PDF.
- Excluir información real de clientes y tickets de
  las capturas destinadas a documentación pública.

Una interfaz de solo lectura no sustituye los
controles de autorización del servidor.

Los ejemplos deben utilizar información sintética
o adecuadamente anonimizada.

## Documentación relacionada

- [Arquitectura de referencia](../architecture.md)
- [Guía 01 — Preparación y requisitos previos](01-prerequisites.md)
- [Guía 02 — Adquisición de datos](02-data-acquisition.md)
- [Guía 03 — Procesamiento y sincronización con Python](03-python-processing.md)
- [Guía 04 — Persistencia en SQLite e integridad de datos](04-sqlite-persistence.md)
- [Descripción general del proyecto](../../../README.es.md)
