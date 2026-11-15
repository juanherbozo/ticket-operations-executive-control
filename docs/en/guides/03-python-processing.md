# Guide 03 — Python Processing and Synchronization

[English](03-python-processing.md) | [Español](../../es/guides/03-python-processing.md)

## Purpose

This guide describes the local processing and synchronization
layer of Ticket Operations — Executive Control.

It explains how full JSON snapshots are validated, processed
through successive data layers and made available for
analytical consumption through SQLite.

The document describes engineering responsibilities and
operational controls without publishing proprietary code.

## Position in the Architecture

Python operates downstream from Microsoft Dataverse,
Power Automate and OneDrive.

Its responsibility begins when an extracted snapshot becomes
available to the local environment.

The conceptual processing sequence is:

JSON FULL -> RAW -> STAGING 01 -> STAGING 02 -> FINAL -> SQLite

The executive dashboard consumes the resulting analytical
information through a separate read-only local service.

## Synchronization Workflow

The synchronization component coordinates the processing
of snapshots delivered through OneDrive.

The workflow includes the following logical activities:

1. Inspect the synchronization and incoming-file state.
2. Identify whether a new snapshot requires processing.
3. Check the source file and associated control information.
4. Coordinate import and staged transformation.
5. Validate the resulting database state.
6. Update synchronization evidence after successful processing.
7. Preserve information needed for auditing and recovery.

The local implementation provides separate execution paths
for checking synchronization state and running an ingestion
cycle.

A synchronization check must not be confused with a
successful database update.

## Processing Layers

| Layer | Main Responsibility |
|---|---|
| RAW | Register imported source-oriented information and its batch context |
| STAGING 01 | Integrate datasets and standardize their structure |
| STAGING 02 | Apply business-oriented transformation and preparation |
| FINAL | Provide the validated analytical representation |
| SQLite | Persist structured information and supporting control data |

The layers represent distinct responsibilities within the
processing architecture.

They should not be treated as interchangeable datasets.

## Data Transformation Responsibilities

The processing layer integrates information originating
from incidents, service requests, SLA records and time entries.

Depending on the dataset, transformations may include:

- Alignment of field names and data structures.
- Normalization of compatible values and data types.
- Integration of related operational information.
- Application of documented business rules.
- Preparation of information for analytical consumption.

Specific transformation rules must be verified against
the implementation and source data contracts.

This public guide does not reproduce internal business logic.

## Integrity and Reconciliation

The solution uses validation controls intended to prevent
unverified source data from becoming the current analytical state.

These controls include:

- Identification of imported source batches.
- Comparison of file checksums.
- Consistency checks across synchronization records.
- Verification of processing results and record counts.
- SQLite database integrity verification.
- Review of synchronization success or failure evidence.

A completed script execution alone is not sufficient
proof of source-to-database consistency.

## Scheduled Execution and Concurrency

Windows Task Scheduler coordinates periodic local
synchronization checks.

The scheduled process is separate from the Power Automate
recurrence that generates source snapshots.

The local implementation includes a mechanism to prevent
simultaneous scheduled synchronization executions.

The precise scheduling interval is a configuration choice
and should be documented separately from the processing
logic.

## Publication and Recovery

Validated information is made available for analytical
consumption after the processing controls complete.

The design incorporates recovery backups and synchronization
audit records.

A failed or incomplete processing attempt must be
distinguished from a successfully published analytical state.

Recovery actions should be performed using verified
backups and documented operational procedures.

This guide does not provide unrestricted production
database replacement commands.

## Operational Monitoring

The implementation maintains synchronization and control
information that supports diagnosis of processing status.

Relevant operational questions include:

- Is a new snapshot waiting to be processed?
- Does the latest source batch match synchronization evidence?
- Were integrity and consistency checks successful?
- Is the local database available?
- Are there processing errors requiring investigation?

The dashboard presents a read-only synchronization status
indicator. It does not trigger database modifications.

## Local Validation and Limitations

Local testing has covered successive full-snapshot imports,
processing-layer consistency, batch identification,
checksum validation and SQLite integrity.

Local scheduled-task execution and the read-only dashboard
integration have also been checked.

These validations do not constitute independent confirmation
of every future fully unattended end-to-end execution.

## Security and Confidentiality

Processing must respect authorized file-system access
and the separation of operational responsibilities.

Production exports, corporate credentials, database copies,
internal scripts and confidential service records are
excluded from this public repository.

The documentation focuses on architecture, validation
principles and reproducible engineering concepts.

## Related Documentation

- [Reference Architecture](../architecture.md)
- [Guide 01 — Preparation and Prerequisites](01-prerequisites.md)
- [Guide 02 — Data Acquisition](02-data-acquisition.md)
- [Project Overview](../../../README.md)
