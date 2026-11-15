# Guide 04 — SQLite Persistence and Data Integrity

[English](04-sqlite-persistence.md) | [Español](../../es/guides/04-sqlite-persistence.md)

## Purpose

This guide describes the persistence layer of
Ticket Operations — Executive Control.

It explains how validated operational information is
stored in SQLite and how database integrity, source
traceability and read-only analytical access are addressed.

The objective is to document the technical approach
without publishing confidential data or internal schemas.

## Architecture Position

SQLite operates downstream from the acquisition and
Python processing layers.

The logical sequence is:

Dataverse -> Power Automate -> OneDrive -> Python
-> RAW -> STAGING 01 -> STAGING 02 -> FINAL -> SQLite

The executive dashboard obtains analytical information
through a separate local read-only API.

SQLite does not perform the cloud extraction and should
not be confused with the original Dataverse data source.

## Logical Persistence Responsibilities

The persistence architecture must account for several
different information responsibilities:

| Responsibility | Description |
|---|---|
| Analytical records | Information prepared for executive reporting |
| Source context | References to imported operational datasets |
| Batch lineage | Identification of the processing execution |
| Validation evidence | Results used to assess processing consistency |
| Synchronization state | Information required to determine processing status |

These are logical responsibilities, not a published list
of actual SQLite table names.

Some synchronization and audit artifacts may also exist
outside the database.

The physical schema remains outside the public scope.

## Snapshot-to-Database Lifecycle

The local pipeline processes full JSON snapshots through
a sequence of controlled transformations.

The intended lifecycle is:

1. Receive an identified source snapshot.
2. Validate its readability and expected structure.
3. Register source and batch context.
4. Process RAW and the successive STAGING layers.
5. Produce the FINAL analytical representation.
6. Perform applicable consistency and integrity checks.
7. Make the validated information available through SQLite.
8. Record the outcome for synchronization monitoring.

Completing a transformation step does not by itself
prove that the current database state is valid.

The processing result and synchronization outcome
must be evaluated together.

## Batch Lineage and File Identity

Traceability connects the analytical state to the source
snapshot and the processing execution that produced it.

Relevant identifiers include:

- Source snapshot or batch identification.
- Processing execution information.
- File checksum information.
- Synchronization result and related timestamps.
- Processing success or failure evidence.

SHA-256 can establish whether two files have identical
content with very high confidence.

A matching checksum does not prove that the source
information was complete, authorized or accurate.

Batch identifiers and checksums serve complementary
purposes and should not be treated as interchangeable.

## Data Integrity and Reconciliation

Integrity checks must distinguish between structural
database health and business-level consistency.

Typical validation categories include:

- Source snapshot readability and structure.
- Expected operational dataset availability.
- Reconciliation of relevant record counts.
- Consistency of related operational information.
- Batch and checksum correspondence.
- SQLite internal integrity.

SQLite offers a native integrity inspection statement:

```sql
PRAGMA integrity_check;
```

This command is shown as a general technical reference.
It does not replace domain-specific reconciliation or
establish which exact checks a particular execution ran.

A successful database integrity check alone cannot
prove that every source record was extracted correctly.

## Publication Consistency

Persistence should prevent incomplete or invalid processing
results from being mistaken for the current analytical state.

SQLite supports transactional changes within the database.

However, database transactions do not automatically make
a multi-step workflow involving OneDrive files, Python,
backups and synchronization metadata fully atomic.

A robust publication process must define:

- The point at which a processed batch is accepted.
- Conditions required before reporting success.
- Behavior when validation or writing fails.
- How the previous usable analytical state is protected.
- How the synchronization status reflects the outcome.

The exact publication and rollback implementation must
be verified against the deployed code; this guide does
not claim an unverified atomic-swap mechanism.

## Backup and Recovery

The local design includes database backup and recovery
considerations.

A reliable recovery procedure should identify:

- Which database or supporting files require backup.
- The relationship between a backup and its source batch.
- How backup integrity is checked.
- What happens after an interrupted processing attempt.
- How the restored analytical state is validated.

Copying an active SQLite database file without considering
its journaling mode and concurrent writes may not provide
a consistent backup.

Use a verified SQLite-compatible backup procedure and
test restoration before relying on a backup operationally.

No confidential database files or recovery credentials
are included in this repository.

## Read-Only Dashboard Access

The dashboard consumes analytical information through
a local API rather than directly running extraction or
database maintenance operations.

The implementation uses a service bound to the local
loopback interface.

The intended separation is:

| Component | Responsibility |
|---|---|
| SQLite | Persist validated analytical information |
| Local API | Serve permitted read-only analytical results |
| Dashboard | Display filters, indicators and visualizations |
| Python processing | Perform synchronization and data updates |

A read-only dashboard interface must not be considered
a substitute for server-side access controls.

Database connection permissions and API endpoint behavior
must be reviewed independently.

## Operational Validation Status

Local validation has covered successive full-snapshot
imports, batch and checksum traceability, database
integrity checks and analytical dashboard consumption.

The system also includes configured cloud and local
scheduling mechanisms.

An unattended execution starting with the cloud recurrence
and ending with publication of a validated SQLite state
still requires independent end-to-end confirmation.

The observed results support the documented local tests,
not a guarantee for every future execution.

## Security and Confidentiality

SQLite is a local persistence component, not an
authentication or authorization system by itself.

Appropriate safeguards include:

- Restrict access to database and backup files.
- Keep corporate secrets outside version control.
- Avoid exposing the local API to untrusted networks.
- Separate processing permissions from reporting access.
- Exclude confidential exports and operational records
  from public documentation.

This guide intentionally omits internal table names,
corporate identifiers, real record contents and
production database copies.

## Related Documentation

- [Reference Architecture](../architecture.md)
- [Guide 01 — Preparation and Prerequisites](01-prerequisites.md)
- [Guide 02 — Data Acquisition](02-data-acquisition.md)
- [Guide 03 — Python Processing and Synchronization](03-python-processing.md)
- [Project Overview](../../../README.md)
