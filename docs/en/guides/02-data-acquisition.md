# Guide 02 — Data Acquisition

[English](02-data-acquisition.md) | [Español](../../es/guides/02-data-acquisition.md)

## Purpose

This guide documents the acquisition layer of Ticket Operations —
Executive Control.

It explains how operational information moves from Microsoft
Dataverse through Power Automate and OneDrive before being
processed by the local Python pipeline.

The objective is to describe the engineering approach without
exposing corporate connection details or confidential records.

## Acquisition Architecture

The acquisition layer involves three main components:

| Component | Responsibility |
|---|---|
| Microsoft Dataverse | Source of operational service information |
| Power Automate | Scheduled extraction and preparation of snapshots |
| Microsoft OneDrive | File exchange between cloud acquisition and local processing |

Python synchronization operates downstream from this layer.

## Operational Data Sources

The solution works with four conceptual information categories:

- Incidents.
- Service requests.
- Service Level Agreement (SLA) records.
- Time entries.

Each category contributes information required for monitoring
operational workload, response performance and recorded effort.

Only source records accessible through authorized permissions
should be included in the extraction.

## Full-Snapshot Strategy

The solution uses full JSON snapshots rather than relying
exclusively on incremental extraction.

A full snapshot represents the information returned by the
configured extraction process at a particular execution.

This approach supports controlled replacement of the analytical
state after validation.

However, a full snapshot should not automatically be assumed
complete or valid simply because its file exists.

Source coverage, expected structure and synchronization
consistency must be checked separately.

## Power Automate Extraction

Power Automate coordinates the cloud acquisition workflow.

Its logical responsibilities include:

1. Start an extraction according to a configured recurrence.
2. Query the authorized Dataverse data sources.
3. Build a structured JSON snapshot.
4. Deliver the snapshot to the designated OneDrive location.
5. Provide an execution result for operational monitoring.

Extraction must respect source-system permissions,
connector capabilities and applicable service limits.

The extraction schedule is configured as a recurring workflow.
Its fully unattended end-to-end operation requires separate
operational confirmation.

## OneDrive File Exchange

OneDrive provides the exchange location between the
cloud workflow and the local Python environment.

The processing design distinguishes:

- Incoming snapshots awaiting processing.
- Processed snapshots retained for traceability.
- Synchronization state and audit information.

Exact corporate directories are deliberately excluded
from this public documentation.

The presence of a synchronized file does not by itself
confirm that ingestion has completed successfully.

## Handoff to Python

The acquisition layer ends when a snapshot becomes
available for the local synchronization process.

The Python component is responsible for downstream controls,
including checking the synchronization state, validating
incoming files and coordinating their ingestion.

RAW, STAGING 01, STAGING 02, FINAL and SQLite belong to the
processing and persistence layers, not to Power Automate.

This separation avoids presenting the cloud workflow
as the component that performs all data transformations.

## Automation and Scheduling

The architecture uses two independent automation mechanisms:

| Mechanism | Responsibility |
|---|---|
| Power Automate recurrence | Execute cloud data extraction |
| Windows Task Scheduler | Run local synchronization checks |

The two schedules are not equivalent.

A successful cloud execution does not automatically prove
that the resulting snapshot was processed and published
successfully to SQLite.

Both stages require their own monitoring and validation.

## Data Quality and Traceability

The solution is designed to support traceability between
source snapshots and the analytical database.

Relevant downstream controls include:

- Snapshot identification.
- Source-batch tracking.
- Checksum comparison.
- Processing and synchronization logs.
- Validation before publishing the current analytical state.
- Recovery and backup procedures.

These controls are implemented downstream of acquisition
and should not be attributed solely to Power Automate.

## Security and Confidentiality

Data extraction must operate with authorized identities
and the minimum permissions necessary.

Connection secrets and corporate credentials must remain
outside public source-control repositories.

This case study does not publish:

- Corporate environment addresses or tenant identifiers.
- Authentication material or connector secrets.
- Real operational JSON exports.
- Confidential incidents, requests or time entries.
- Unrestricted copies of internal extraction logic.

## Validation Boundary

The local implementation has been tested using successive
full-snapshot imports and downstream consistency checks.

The recurring Power Automate extraction schedule has
been configured.

A fully unattended end-to-end cycle, initiated by the
recurrence and completed through SQLite publication,
requires independent operational confirmation.

This distinction prevents configured automation from
being presented as an already proven production guarantee.

## Related Documentation

- [Reference Architecture](../architecture.md)
- [Guide 01 — Preparation and Prerequisites](01-prerequisites.md)
- [Project Overview](../../../README.md)
