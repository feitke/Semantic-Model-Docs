# Datasource Inventory — SMDocTest

**Workspace:** SMDocTest  
**Generated:** 2026-02-12T10:25:55-05:00  
**Semantic Models Analyzed:** 6

---

## Table of Contents

1. [Executive Summary](#executive-summary)
2. [Datasource Summary](#datasource-summary)
3. [Shared Datasource Detail](#shared-datasource-detail)
4. [Datasource-to-Model Matrix](#datasource-to-model-matrix)
5. [Centralization Candidates](#centralization-candidates)
6. [All Datasources by Model](#all-datasources-by-model)

---

## Executive Summary

| Metric | Value |
|---|---|
| Total unique datasources | 7 |
| Datasources used by multiple models | 1 |
| Datasources used by a single model | 6 |
| Total semantic models | 6 |

> **1 datasource(s)** are accessed by multiple semantic models 
> and may be candidates for consolidation into shared Fabric artifacts.

---

## Datasource Summary

| # | Connector | Host / Server | Database / Catalog | Models Using | Shared? |
|---|---|---|---|---|---|
| 1 | `Sql.Database` | `rddhezwna3wutjobbbcron4k6e-vepdvwfqpavevgmawk5vs6h23e.database.fabric.microsoft.com` | `FoldingDemo-47385673-59b2-4c32-9e8e-4c383aa2b2a7` | 3 | **Yes** |
| 2 | `Csv.Document` | `sp500_companies.csv` | `—` | 1 | No |
| 3 | `Csv.Document` | `sp500_index.csv` | `—` | 1 | No |
| 4 | `Csv.Document` | `sp500_stocks.csv` | `—` | 1 | No |
| 5 | `Csv.Document` | `ResellerSalesTargets.csv` | `—` | 1 | No |
| 6 | `Excel.Workbook` | `Working Sessions List.xlsx` | `—` | 1 | No |
| 7 | `Sql.Databases` | `—` | `—` | 1 | No |

---

## Shared Datasource Detail

The following datasources are used by **two or more** semantic models. 
These represent the highest-value candidates for query centralization.

### Sql.Database / rddhezwna3wutjobbbcron4k6e-vepdvwfqpavevgmawk5vs6h23e.database.fabric.microsoft.com / FoldingDemo-47385673-59b2-4c32-9e8e-4c383aa2b2a7

- **Connector:** `Sql.Database`
- **Host/Server:** `rddhezwna3wutjobbbcron4k6e-vepdvwfqpavevgmawk5vs6h23e.database.fabric.microsoft.com`
- **Database/Catalog:** `FoldingDemo-47385673-59b2-4c32-9e8e-4c383aa2b2a7`
- **Used by 3 model(s)**

| Semantic Model | Tables Using This Source | Source Objects Queried |
|---|---|---|
| NYC Taxi - DQ | Date, Geography, HackneyLicense, Medallion, Time, Trip, Weather | `dbo.Weather` |
| NYC Taxi - Folded | JanTrips-Folded | `dbo.Trip` |
| NYC Taxi - Not Folded | JanTrips-NotFolded | `dbo.Trip` |

---

## Datasource-to-Model Matrix

| Datasource |11-Solution-Sales Analysis | NYC Taxi - DQ | NYC Taxi - Folded | NYC Taxi - Not Folded | S&P Company Report | TableauWorkbooks |
|---|---|---|---|---|---|---|
| Sql.Database / rddhezwna3wutjobbbcron4k6e-vepdvwfqpavevgm... | — | ✅ (7 tables) | ✅ (1 tables) | ✅ (1 tables) | — | — |
| Csv.Document / sp500_companies.csv | — | — | — | — | ✅ (1 tables) | — |
| Csv.Document / sp500_index.csv | — | — | — | — | ✅ (1 tables) | — |
| Csv.Document / sp500_stocks.csv | — | — | — | — | ✅ (1 tables) | — |
| Csv.Document / ResellerSalesTargets.csv | ✅ (1 tables) | — | — | — | — | — |
| Excel.Workbook / Working Sessions List.xlsx | — | — | — | — | — | ✅ (1 tables) |
| Sql.Databases | ✅ (6 tables) | — | — | — | — | — |

---

## Centralization Candidates

The following datasources are recommended for evaluation. They are ranked by 
the number of consuming semantic models (highest first).

| Priority | Datasource | Models | Recommendation |
|---|---|---|---|
| 1 | Sql.Database / rddhezwna3wutjobbbcron4k6e-vepdv... | 3 | Consider a **Dataflow Gen2** to stage into a Lakehouse, or use **Direct Lake** with a mirrored database. |

### Recommended Centralization Patterns

| Pattern | When to Use |
|---|---|
| **Dataflow Gen2** | Multiple models query the same external source with different transformations. Centralize ETL, output to Lakehouse tables. |
| **Lakehouse / Warehouse** | Source data should be staged in Fabric for reuse by multiple models via Direct Lake or SQL endpoint. |
| **Direct Lake** | Models can point directly at Delta tables in a Lakehouse, eliminating import duplication. |
| **Shared Semantic Model** | Downstream models can use live-connect or composite-model chaining against a single authoritative model. |
| **Composite Model** | Some tables come from a shared model (live) while others are imported locally for augmentation. |

---

## All Datasources by Model

### 11-Solution-Sales Analysis

- **Sql.Databases** → `—` / `—`
  - Source objects: `dbo.FactResellerSales`
  - Model tables: Product, Region, Reseller, Sales, Salesperson (Performance), SalespersonRegion
- **Csv.Document** → `—` / `—`
  - Model tables: Targets

### NYC Taxi - DQ

- **Sql.Database** → `rddhezwna3wutjobbbcron4k6e-vepdvwfqpavevgmawk5vs6h23e.database.fabric.microsoft.com` / `FoldingDemo-47385673-59b2-4c32-9e8e-4c383aa2b2a7`
  - Source objects: `dbo.Weather`
  - Model tables: Date, Geography, HackneyLicense, Medallion, Time, Trip, Weather

### NYC Taxi - Folded

- **Sql.Database** → `rddhezwna3wutjobbbcron4k6e-vepdvwfqpavevgmawk5vs6h23e.database.fabric.microsoft.com` / `FoldingDemo-47385673-59b2-4c32-9e8e-4c383aa2b2a7`
  - Source objects: `dbo.Trip`
  - Model tables: JanTrips-Folded

### NYC Taxi - Not Folded

- **Sql.Database** → `rddhezwna3wutjobbbcron4k6e-vepdvwfqpavevgmawk5vs6h23e.database.fabric.microsoft.com` / `FoldingDemo-47385673-59b2-4c32-9e8e-4c383aa2b2a7`
  - Source objects: `dbo.Trip`
  - Model tables: JanTrips-NotFolded

### S&P Company Report

- **Csv.Document** → `—` / `—`
  - Model tables: DimCompany
- **Csv.Document** → `—` / `—`
  - Model tables: FactS&PClose
- **Csv.Document** → `—` / `—`
  - Model tables: FactStockPrices

### TableauWorkbooks

- **Excel.Workbook** → `—` / `—`
  - Source objects: `Workbook List`
  - Model tables: Workbook List

---

*Generated by FabricDocGen — 2026-02-12T10:25:55-05:00*
