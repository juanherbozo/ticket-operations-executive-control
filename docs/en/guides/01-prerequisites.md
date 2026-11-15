# Guide 01 — Preparation and Prerequisites

[English](01-prerequisites.md) | [Español](../../es/guides/01-prerequisites.md)

## Purpose

This guide describes the prerequisites for understanding and
preparing an environment based on the Ticket Operations —
Executive Control architecture.

It documents technical dependencies and readiness checks.
It does not provide access to, or reproduce, the original
corporate operational environment.

## Solution Context

The solution consists of four logical stages:

1. Acquisition: Dataverse, Power Automate and OneDrive.
2. Transformation: Python and staged data processing.
3. Persistence and security: SQLite, validation and backups.
4. Visualization: a read-only local API and executive dashboard.

The implementation runs its analytical components locally,
while data acquisition depends on authorized cloud services.

## Technical Prerequisites

| Component | Requirement |
|---|---|
| Microsoft Dataverse | Authorized access to the relevant operational data |
| Power Automate | Permission to configure and operate an extraction flow |
| Microsoft OneDrive | Access to a synchronized file exchange location |
| Windows | Local environment capable of running scheduled tasks |
| Python | Compatible Python 3 installation with required dependencies |
| SQLite | SQLite support for local structured persistence |
| Windows Task Scheduler | Permission to configure local scheduled execution |
| Web browser | Support for modern HTML, CSS and JavaScript |
| Git | Version control for documentation and approved examples |

The exact software versions and licensing requirements
must be confirmed for each target environment.

## Access and Permissions

Access should follow the principle of least privilege.

The acquisition process requires authorized access to
source records. Local processing requires appropriate
file-system permissions for incoming snapshots, logs,
database files and recovery backups.

The executive dashboard uses a local read-only interface.
It does not require database write permissions.

Credentials, tokens and connection secrets must not be
stored in a public repository.

## Input Data Requirements

The processing pipeline expects full JSON snapshots
representing the data required by the solution.

The conceptual source categories are:

- Incidents.
- Service requests.
- SLA information.
- Time entries.

Source identifiers, timestamps and expected structures
must be checked before loading data into SQLite.

Only authorized, appropriately protected source exports
should be used.

## Logical Workspace Organization

Separate locations should be maintained for:

- Incoming and processed snapshots.
- Python processing and orchestration.
- SQLite databases.
- Synchronization logs and audit information.
- Recovery backups.
- Dashboard and local API components.

These are logical responsibilities, not mandatory
operating-system paths.

## Initial Environment Checks

The following read-only PowerShell commands can help
identify the local Python environment:

```powershell
python --version
python -c "import sqlite3; print(sqlite3.sqlite_version)"
Get-Command python -ErrorAction SilentlyContinue
```

These checks do not establish that cloud permissions,
scheduled extraction or end-to-end synchronization
are correctly configured.

## Readiness Checklist

Before implementation or testing, verify that:

1. Source-system access is explicitly authorized.
2. The extraction workflow has appropriate permissions.
3. OneDrive synchronization is configured and available.
4. Python and its required dependencies are installed.
5. Database, audit and backup locations are defined.
6. Local scheduled execution can be configured.
7. The dashboard can operate through a read-only local API.
8. Sensitive operational information is excluded from
   public documentation and repositories.

## Validation Boundary

Successful preparation does not imply successful
end-to-end automation.

The recurring extraction schedule has been configured
in the documented implementation. Unattended scheduled
execution requires independent operational confirmation.

## Security and Confidentiality

This guide provides an architectural preparation
reference, not a production deployment package.

It intentionally omits corporate environment addresses,
credentials, production exports and proprietary logic.

## Related Documentation

- [Reference Architecture](../architecture.md)
- [Project Overview](../../../README.md)
