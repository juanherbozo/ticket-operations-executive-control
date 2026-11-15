# Guide 05 — Executive Dashboard and Read-Only API

[English](05-executive-dashboard.md) | [Español](../../es/guides/05-executive-dashboard.md)

## Purpose

This guide documents the executive presentation layer of
Ticket Operations — Executive Control.

It explains how validated information from SQLite is
consumed through a local read-only API and presented
using a browser-based dashboard.

The documentation focuses on architecture, information
interpretation, usability, operational visibility and
confidentiality.

It does not publish real customer data or proprietary
dashboard source code.

## Position in the Architecture

The dashboard is the final consumption layer of the system.

The logical information flow is:

Dataverse -> Power Automate -> OneDrive -> Python
-> SQLite -> Local Read-Only API -> Executive Dashboard

The dashboard does not directly execute cloud acquisition,
JSON ingestion or database maintenance.

Its purpose is to support operational assessment and
executive review of information already processed by
the preceding layers.

## Local API and Data Access

The implementation uses a local service bound to the
loopback interface to provide analytical information.

The dashboard consumes this service rather than
connecting directly to SQLite from the browser.

The architectural responsibilities are separated:

| Component | Responsibility |
|---|---|
| SQLite | Persist validated analytical information |
| Local API | Serve permitted read-only analytical results |
| HTML | Structure the dashboard interface |
| CSS | Define visual presentation and layout |
| JavaScript | Handle filtering, rendering and interactions |

The API is intended for analytical consultation,
not for triggering processing or modifying tickets.

A locally bound service is not automatically an
authentication or authorization mechanism.

## Executive Information Scope

The dashboard provides a consolidated view for reviewing
operational service activity.

Its information originates from the processed datasets
covering incidents, requests, SLA records and time entries.

The presentation can organize information around:

- Operational workload and ticket status.
- Priorities and records requiring attention.
- SLA-related indicators when source information exists.
- Recorded effort and associated operational activity.
- Distribution and comparison across available dimensions.

The precise set of displayed indicators must correspond
to the implemented interface and its verified data model.

This guide does not invent business metrics or results.

## KPI Interpretation and Governance

An executive indicator is useful only when its meaning
and source are understood.

Each KPI should have a documented interpretation covering:

| Dimension | Verification question |
|---|---|
| Source | Which processed dataset supports the indicator? |
| Population | Which records are included or excluded? |
| Calculation | What aggregation or business rule is applied? |
| Time scope | Which date or reporting period is represented? |
| Missing data | How are absent or unavailable values handled? |

A missing SLA value must not automatically be interpreted
as either compliance or non-compliance.

The application should distinguish unavailable source
information from genuine zero values.

No numerical KPI results are included in this public guide.

## Filtering and Exploration

The dashboard provides interactive filtering and
exploration capabilities for executive review.

Filters should operate over the same validated analytical
state used by the indicators and charts.

A reliable filter interaction must preserve consistency
between visible totals, related visualizations and
the selected scope.

Where relevant, the interface should distinguish:

- No records matching the applied filters.
- Data not available in the underlying source.
- An error while requesting or rendering information.

These states are operationally different and should not
be presented as equivalent.

## Bilingual Interface and Visualizations

The implementation supports an English and Spanish
presentation of the executive dashboard.

The interface combines visual summaries, charts
and interactive controls.

Language selection is a presentation concern and must
not change underlying analytical values.

For bilingual consistency, the following require review:

- Equivalent terminology across both languages.
- Consistent interpretation of KPI labels.
- Date and number presentation.
- Readability of chart labels and legends.
- Clear distinction between titles and data values.

Visual design should support the identification of
records that need attention without implying that
every alert or status is a verified business failure.

## PDF Export

The dashboard includes functionality for producing
a PDF representation of executive information.

The exported report should reflect the information
that the interface intends to communicate.

Relevant validation considerations include:

- Clear headings and readable visual elements.
- Correct language and reporting context.
- Consistency between selected filters and exported data.
- Legibility of charts, tables and long labels.
- Avoidance of unintended confidential information.

The exact PDF rendering implementation is outside
the scope of this public architectural document.

PDF generation does not independently validate
the accuracy of the underlying source data.

## Synchronization Status and Data Freshness

The dashboard includes a read-only synchronization
status indicator.

Its purpose is to provide context about the data
available for executive consultation.

Different events must remain distinguishable:

- A cloud extraction has been scheduled.
- A source snapshot has been delivered.
- Local synchronization has been attempted.
- A batch has been processed successfully.
- A validated analytical state is available.

An available dashboard or successful API response
does not by itself prove that the latest cloud
snapshot has already been incorporated.

Freshness assessments should rely on verified
synchronization evidence rather than assumed timing.

## Validation and Failure States

Local validation has covered the analytical pipeline,
database availability and dashboard consumption.

The dashboard implementation also includes filtering,
visualization, bilingual presentation and PDF export
capabilities.

The full unattended sequence, starting with a
scheduled cloud extraction and ending with a newly
validated dashboard state, requires separate confirmation.

Operational review should account for API failures,
unavailable analytical data, processing errors and
outdated synchronization states.

A visually successful page load is not sufficient
evidence of full end-to-end correctness.

## Security and Confidentiality

The dashboard is designed for consultation and should
not be used to expose internal corporate datasets.

Security responsibilities include:

- Keep database updates outside the dashboard interface.
- Restrict local API and database access appropriately.
- Avoid publishing credentials or service connection data.
- Review confidential content before generating or sharing PDFs.
- Exclude real customer and ticket details from screenshots
  used in public documentation.

A read-only user interface does not replace server-side
enforcement of permitted operations.

Illustrative examples should use synthetic or
appropriately anonymized information.

## Related Documentation

- [Reference Architecture](../architecture.md)
- [Guide 01 — Preparation and Prerequisites](01-prerequisites.md)
- [Guide 02 — Data Acquisition](02-data-acquisition.md)
- [Guide 03 — Python Processing and Synchronization](03-python-processing.md)
- [Guide 04 — SQLite Persistence and Data Integrity](04-sqlite-persistence.md)
- [Project Overview](../../../README.md)
