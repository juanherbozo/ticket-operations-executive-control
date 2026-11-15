# Guía 01 — Preparación y requisitos previos

[English](../../en/guides/01-prerequisites.md) | [Español](01-prerequisites.md)

## Objetivo

Esta guía describe los requisitos previos para comprender
y preparar un entorno basado en la arquitectura de
Ticket Operations — Executive Control.

Documenta las dependencias técnicas y las comprobaciones
iniciales. No proporciona acceso al entorno operativo
corporativo original ni constituye una reproducción de este.

## Contexto de la solución

La solución comprende cuatro etapas lógicas:

1. Adquisición: Dataverse, Power Automate y OneDrive.
2. Transformación: Python y procesamiento por capas.
3. Persistencia y seguridad: SQLite, validaciones y copias de seguridad.
4. Visualización: API local de solo lectura y panel ejecutivo.

Los componentes analíticos funcionan localmente, mientras
que la adquisición depende de servicios en la nube
a los que se debe disponer de acceso autorizado.

## Requisitos técnicos

| Componente | Requisito |
|---|---|
| Microsoft Dataverse | Acceso autorizado a los datos operativos correspondientes |
| Power Automate | Permisos para configurar y ejecutar el flujo de extracción |
| Microsoft OneDrive | Acceso a una ubicación de intercambio sincronizada |
| Windows | Entorno local capaz de ejecutar tareas programadas |
| Python | Instalación compatible de Python 3 y sus dependencias |
| SQLite | Compatibilidad con SQLite para persistencia local |
| Programador de tareas | Permisos para configurar ejecuciones locales |
| Navegador web | Compatibilidad con HTML, CSS y JavaScript modernos |
| Git | Control de versiones para documentación y ejemplos autorizados |

Las versiones exactas y los requisitos de licencia deben
confirmarse para cada entorno de destino.

## Accesos y permisos

El acceso debe seguir el principio de mínimo privilegio.

La adquisición necesita autorización para consultar los
registros de origen. El procesamiento local requiere
permisos adecuados sobre instantáneas, registros,
archivos de base de datos y copias de seguridad.

El panel ejecutivo utiliza una interfaz local de solo lectura.
No necesita permisos de escritura sobre la base de datos.

Las credenciales, los tokens y los secretos de conexión
no deben almacenarse en repositorios públicos.

## Requisitos de los datos de entrada

El procesamiento requiere instantáneas JSON completas
que representen la información necesaria para la solución.

Las categorías conceptuales de origen son:

- Incidencias.
- Solicitudes de servicio.
- Información de SLA.
- Registros de tiempo.

Antes de cargar información en SQLite deben comprobarse
los identificadores de origen, las marcas temporales
y las estructuras esperadas.

Solo deben utilizarse exportaciones autorizadas y
protegidas adecuadamente.

## Organización lógica del entorno

Conviene mantener ubicaciones separadas para:

- Instantáneas entrantes y procesadas.
- Procesamiento y orquestación con Python.
- Bases de datos SQLite.
- Registros de sincronización e información de auditoría.
- Copias de seguridad para recuperación.
- Componentes del panel y de la API local.

Estas son responsabilidades lógicas y no rutas
obligatorias del sistema operativo.

## Comprobaciones iniciales del entorno

Los siguientes comandos PowerShell de solo lectura
permiten identificar el entorno local de Python:

```powershell
python --version
python -c "import sqlite3; print(sqlite3.sqlite_version)"
Get-Command python -ErrorAction SilentlyContinue
```

Estas comprobaciones no demuestran que los permisos
en la nube, la extracción programada o la sincronización
integral estén correctamente configurados.

## Lista de comprobación previa

Antes de implementar o probar la solución, verificar que:

1. El acceso a los sistemas de origen está autorizado.
2. El flujo de extracción dispone de los permisos necesarios.
3. La sincronización de OneDrive está configurada y disponible.
4. Python y sus dependencias están instalados.
5. Se han definido las ubicaciones de datos, auditoría y copias.
6. Es posible configurar la ejecución local programada.
7. El panel puede operar mediante una API local de solo lectura.
8. La información sensible queda excluida de repositorios
   y documentación pública.

## Alcance de la validación

La preparación correcta del entorno no implica que
la automatización completa funcione sin intervención.

En la implementación documentada, la programación de
extracción recurrente está configurada. La ejecución
programada y desatendida requiere una confirmación
operativa independiente.

## Seguridad y confidencialidad

Esta guía es una referencia de preparación arquitectónica,
no un paquete de despliegue para producción.

Se omiten deliberadamente direcciones corporativas,
credenciales, exportaciones de producción y lógica propietaria.

## Documentación relacionada

- [Arquitectura de referencia](../architecture.md)
- [Descripción general del proyecto](../../../README.es.md)
