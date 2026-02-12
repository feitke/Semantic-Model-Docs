# NYC Taxi - Folded

**Workspace:** SMDocTest  
**Generated:** 2026-02-12T09:20:49-05:00

## Overview
- **Model Type:** Import
- **ModelName:** Model
- **DatabaseName:** NYC Taxi - Folded
- **CompatibilityLevel:** 1600
- **TableCount:** 1
- **TotalMeasureCount:** 5
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
    JanTrips-Folded {
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
| Total measures | 5 |
| Tables with measures | 1 |
| Avg measures per table | 5.0 |
| Avg DAX expression length | 35 chars |
| Max DAX expression length | 50 chars |

**Top Complex Measures** (ranked by composite score):

| Rank | Measure | DAX Length | Nesting Depth | Functions | Score |
|---|---|---|---|---|---|
| 1 | DurationMinutes | 50 | 1 | 1 | 120 |
| 2 | Passengers | 38 | 1 | 1 | 108 |
| 3 | TipPercent | 20 | 1 | 1 | 90 |
| 4 | Fare | 34 | 1 | 0 | 84 |
| 5 | Tip | 33 | 1 | 0 | 83 |

### Naming & Organization

**Measure Placement:**

| Table | Measures |
|---|---|
| JanTrips-Folded | 5 |


**Naming Conventions:**

- PascalCase: 5 names
- Mixed: 1 names

### Data Source Risk Flags

✅ No data-source risk flags detected.

## Parameters & Shared Expressions

*No named expressions or parameters defined in this model.*

## Data Sources (from Power Query)
### Source 1 — Sql.Database
- **Connector:** `Sql.Database`
- **Host/Server:** `rddhezwna3wutjobbbcron4k6e-vepdvwfqpavevgmawk5vs6h23e.database.fabric.microsoft.com`
- **Database/Catalog:** `FoldingDemo-47385673-59b2-4c32-9e8e-4c383aa2b2a7`
- **Source Objects:** `dbo.Trip`
- **Raw (redacted):** `Sql.Database("rddhezwna3wutjobbbcron4k6e-vepdvwfqpavevgmawk5vs6h23e.database.fabric.microsoft.com", "FoldingDemo-47385673-59b2-4c32-9e8e-4c383aa2b2a7")`
- **Used by tables:** JanTrips-Folded

<details>
<summary>Power Query M Expression</summary>

```powerquery
let
    Source = Sql.Database("rddhezwna3wutjobbbcron4k6e-vepdvwfqpavevgmawk5vs6h23e.database.fabric.microsoft.com", "FoldingDemo-47385673-59b2-4c32-9e8e-4c383aa2b2a7"),
    dbo_Trip = Source{[Schema="dbo",Item="Trip"]}[Data],
    #"Filtered Rows" = Table.SelectRows(dbo_Trip, each [DateID] >= 20130101 and [DateID] <= 20130131)
in
    #"Filtered Rows"
```
</details>

## Tables
### JanTrips-Folded
- Sql.Database (rddhezwna3wutjobbbcron4k6e-vepdvwfqpavevgmawk5vs6h23e.database.fabric.microsoft.com / FoldingDemo-47385673-59b2-4c32-9e8e-4c383aa2b2a7)

## Measures
### JanTrips-Folded
#### DurationMinutes
**DAX Expression:**
```dax
AVERAGE('JanTrips-Folded'[TripDurationSeconds])/60
```

#### Fare
**DAX Expression:**
```dax
sum('JanTrips-Folded'[FareAmount])
```

#### Passengers
**Format:** `0`

**DAX Expression:**
```dax
SUM('JanTrips-Folded'[PassengerCount])
```

#### Tip
**DAX Expression:**
```dax
sum('JanTrips-Folded'[TipAmount])
```

#### TipPercent
**Format:** `0.00%;-0.00%;0.00%`

**DAX Expression:**
```dax
DIVIDE([Tip],[Fare])
```


