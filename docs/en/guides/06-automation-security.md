# Guide 06 — Automation, Scheduling and Security

[English](06-automation-security.md) | [Español](../../es/guides/06-automation-security.md)

## Purpose

This guide explains the automation boundaries and security decisions
of Ticket Operations — Executive Control. The goal is to distinguish
configured recurrence from validated delivery, and to describe how
cloud extraction, local synchronization and analytical access are
coordinated without publishing proprietary configuration or secrets.

## Scheduling Architecture

The solution uses two separate schedulers, with different ownership:

| Mechanism | Scope | Responsibility |
|---|---|---|
| Power Automate recurrence | Cloud | Obtain authorized Dataverse snapshots and deliver JSON files |
| Windows Task Scheduler | Local workstation | Check for new files and initiate the controlled Python synchronization path |

Scheduling a job does not establish that all downstream processing
has succeeded. Each boundary needs its own execution evidence.

## Cloud Recurrence

A recurring Power Automate workflow is configured to run during
business days and extract full JSON snapshots. Its responsibility ends
at producing and delivering the requested source information to
OneDrive, subject to permissions, connector limits and failures.

A successful cloud flow is evidence of that workflow's execution;
it is not by itself proof that SQLite or the dashboard is current.

Schedule details are deployment configuration, not a public
service-level commitment.

## Local Synchronization Checks

The local Windows schedule runs periodic Python checks against the
synchronized file exchange area. The implementation has used
approximately ten-minute checks as a local configuration choice.

The local processing path checks whether a snapshot requires ingestion
before invoking the staged data pipeline. It distinguishes a check
with no new work from an attempted import and from a verified success.

Cloud recurrence and local polling are independent: their clocks,
errors and execution outcomes must be evaluated separately.

## Snapshot Handoff and State

OneDrive acts as a file exchange boundary, not as the analytical
database. The logical file lifecycle distinguishes Incoming,
Processed and synchronization/audit state.

Useful handoff evidence includes source batch identity, file
readability, SHA-256 checksum, processing outcome and last successful
synchronization. A matching hash indicates file identity, not data
completeness, correctness or authorization.

A file arriving in Incoming does not mean it has already been
validated and published to SQLite.

## Duplicate Processing and Concurrency

The local design includes safeguards against overlapping
synchronization executions and repeated processing of the same
snapshot. Batch identity and checksums support these decisions.

The precise behavior after a crash, interrupted write or concurrent
file update must be verified in the deployed implementation. This
guide does not claim exactly-once delivery or distributed atomicity.

## Monitoring and Audit Evidence

Operational monitoring should correlate the following events:

1. Cloud workflow execution and JSON delivery.
2. Local detection of the candidate snapshot.
3. Batch identity and checksum verification.
4. Processing result and SQLite integrity evidence.
5. Updated last-success information and dashboard freshness status.

An error in one stage should be reported as that stage's failure;
a later healthy API response must not silently erase the evidence.

## Failure Handling

Possible failures include connector errors, incomplete files,
OneDrive synchronization delays, schema mismatches, processing errors
and database integrity failures. Recovery procedures should identify
which batch was involved and preserve a previously usable state.

Retries must be designed to avoid ambiguous or duplicated ingestion.
Successful retry, backup restoration and recovery under interruption
are separate scenarios that require targeted validation.

## Secrets and Least Privilege

Dataverse and Power Automate connections should use authorized
identities with only the required source access. Credentials, tokens,
connection strings and tenant-specific endpoints must never be
committed to a public repository.

Local file permissions should restrict access to operational JSON,
SQLite files, backups and synchronization evidence. Public documents
use descriptive components instead of real account and customer data.

## Local API Boundary

The executive dashboard reads analytical information through a
loopback-bound local Python HTTP service. It must not offer ticket
mutations, extraction triggers or arbitrary database maintenance.

Binding to loopback reduces network exposure but is not equivalent to
user authentication or database authorization. Endpoint behavior,
process permissions and exported PDF contents need separate review.

## Validation Boundary

The local implementation has been exercised with successive manual
full-snapshot imports, checksums, batch traceability, SQLite integrity
checks and read-only dashboard consumption. Local scheduled checks
have also been reviewed.

An unattended cycle initiated by the configured cloud recurrence and
completed through SQLite publication has not yet been independently
confirmed. This remains a specific operational acceptance test.

## Related Documentation

- [Reference Architecture](../architecture.md)
- [Guide 02 — Data Acquisition](02-data-acquisition.md)
- [Guide 03 — Python Processing and Synchronization](03-python-processing.md)
- [Guide 04 — SQLite Persistence and Data Integrity](04-sqlite-persistence.md)
- [Guide 05 — Executive Dashboard and Read-Only API](05-executive-dashboard.md)
- [Guide 07 — Validation and Lessons Learned](07-validation-lessons.md)
- [Project Overview](../../../README.md)
