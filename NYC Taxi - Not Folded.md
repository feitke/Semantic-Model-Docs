# NYC Taxi - Not Folded

**Workspace:** SMDocTest  
**Generated:** 2026-02-12T09:20:37-05:00

## Overview
- **Model Type:** Import
- **ModelName:** Model
- **DatabaseName:** NYC Taxi - Not Folded
- **CompatibilityLevel:** 1600
- **TableCount:** 1
- **TotalMeasureCount:** 0
- **TotalColumnCount:** 23
- **TotalPartitionCount:** 1
- **RelationshipCount:** 0
- **RoleCount:** 0
- **CultureCount:** 1
- **PerspectiveCount:** 0
- **DataSourceCount (M-derived):** 1

## Model Shape (Dimensional Modeling)

**Classification:** Not Dimensional &nbsp;|&nbsp; **Confidence:** Low

<details>
<summary>Classification Evidence</summary>

| Metric | Value |
|--------|-------|
| Note | No relationships in model |

</details>

> **Note:** No relationships in model

## Model Diagram

```mermaid
erDiagram
    JanTrips-NotFolded {
    }
```

## Model Quality Summary

### Relationship Risk Signals

*No relationships in this model.*

### RLS / OLS Governance

| Metric | Value |
|---|---|
| Security roles defined | **None** |

> ⚠️ No Row-Level Security roles are defined on this model.

### Model Complexity

| Metric | Value |
|---|---|
| Total tables | 1 |
| Total measures | 0 |

### Naming & Organization

**Naming Conventions:**

- Mixed: 1 names

### Data Source Risk Flags

#### Likely Query-Folding Breakers *(heuristic)*

> **Note:** This analysis is based on the presence and ordering of M transformation steps. It cannot prove whether folding actually occurs — only Power Query diagnostics or SQL Profiler can confirm that. Treat these as *review candidates*, not definitive findings.

| Table | Connector | Steps | Breakers Before Filter | Detail |
|---|---|---|---|---|
| JanTrips-NotFolded | `Sql.Database` | 9 | 3 | 3 transform(s) before filter: `Table.DuplicateColumn`, `Table.SplitColumn`, `Table.ReorderColumns` |

## Parameters & Shared Expressions

*No named expressions or parameters defined in this model.*

## Data Sources (from Power Query)
### Source 1 — Sql.Database
- **Connector:** `Sql.Database`
- **Host/Server:** `rddhezwna3wutjobbbcron4k6e-vepdvwfqpavevgmawk5vs6h23e.database.fabric.microsoft.com`
- **Database/Catalog:** `FoldingDemo-47385673-59b2-4c32-9e8e-4c383aa2b2a7`
- **Source Objects:** `dbo.Trip`
- **Raw (redacted):** `Sql.Database("rddhezwna3wutjobbbcron4k6e-vepdvwfqpavevgmawk5vs6h23e.database.fabric.microsoft.com", "FoldingDemo-47385673-59b2-4c32-9e8e-4c383aa2b2a7")`
- **Used by tables:** JanTrips-NotFolded

<details>
<summary>Power Query M Expression</summary>

```powerquery
let
    Source = Sql.Database("rddhezwna3wutjobbbcron4k6e-vepdvwfqpavevgmawk5vs6h23e.database.fabric.microsoft.com", "FoldingDemo-47385673-59b2-4c32-9e8e-4c383aa2b2a7"),
    dbo_Trip = Source{[Schema="dbo",Item="Trip"]}[Data],
    #"Duplicated Column" = Table.DuplicateColumn(dbo_Trip, "DateID", "DateID - Copy"),
    #"Split Column by Positions" = Table.SplitColumn(Table.TransformColumnTypes(#"Duplicated Column", {{"DateID - Copy", type text}}, "en-US"), "DateID - Copy", Splitter.SplitTextByPositions({0, 6}), {"DateID - Copy.1", "DateID - Copy.2"}),
    #"Changed Type" = Table.TransformColumnTypes(#"Split Column by Positions",{{"DateID - Copy.1", Int64.Type}, {"DateID - Copy.2", Int64.Type}}),
    #"Renamed Columns" = Table.RenameColumns(#"Changed Type",{{"DateID - Copy.1", "YYYYMM"}}),
    #"Removed Columns" = Table.RemoveColumns(#"Renamed Columns",{"DateID - Copy.2"}),
    #"Reordered Columns" = Table.ReorderColumns(#"Removed Columns",{"YYYYMM", "DateID", "MedallionID", "HackneyLicenseID", "PickupTimeID", "DropoffTimeID", "PickupGeographyID", "DropoffGeographyID", "PickupLatitude", "PickupLongitude", "PickupLatLong", "DropoffLatitude", "DropoffLongitude", "DropoffLatLong", "PassengerCount", "TripDurationSeconds", "TripDistanceMiles", "PaymentType", "FareAmount", "SurchargeAmount", "TaxAmount", "TipAmount", "TollsAmount", "TotalAmount"}),
    #"Filtered Rows" = Table.SelectRows(#"Reordered Columns", each ([YYYYMM] = 201301)),
    #"Removed Columns1" = Table.RemoveColumns(#"Fi
// ... (truncated)
```
</details>

## Tables
### JanTrips-NotFolded
- Sql.Database (rddhezwna3wutjobbbcron4k6e-vepdvwfqpavevgmawk5vs6h23e.database.fabric.microsoft.com / FoldingDemo-47385673-59b2-4c32-9e8e-4c383aa2b2a7)

## Measures
- No measures were retrieved.

