# SMDocTest Review

## Table of Contents
1. Executive Summary  
2. Key Findings (What’s Working / What Needs Attention)  
3. Detailed Recommendations  
   - 3.1 Governance & Security (RLS/OLS)  
   - 3.2 Reliability & Refresh (portable sources, service readiness)  
   - 3.3 Performance (query folding, import vs DQ)  
   - 3.4 Data Modeling & Semantic Design  
   - 3.5 Reuse & Centralization (shared sources, shared artifacts)  
   - 3.6 Naming, Organization & Maintainability  
4. Implementation Steps (Phased Plan)  
5. Confidence Level & Assumptions  
6. Appendix: Evidence Snapshot by Model  

---

## 1) Executive Summary
The SMDocTest workspace contains 6 semantic models and 7 unique datasources, with one SQL datasource shared across 3 models—making it the highest-value consolidation candidate.

Overall, the workspace shows a mix of strong star-schema patterns in several models (e.g., 11-Solution-Sales Analysis, NYC Taxi - DQ, S&P Company Report) alongside non-dimensional single-table models (e.g., NYC Taxi - Folded, NYC Taxi - Not Folded) that have no relationships and therefore limited semantic robustness.

The most material risks to address are:
- Security posture: No RLS roles defined in 5/6 models (only 11-Solution-Sales Analysis defines a role).
- Refresh reliability: Multiple models use non-portable local file paths (service refresh risk).
- Performance risk: Evidence of likely query-folding breakers before filtering in at least one import model, and a case where no filter is detected and transformations may execute client-side.
- Standardization: Inconsistent naming (space-separated object names, mixed conventions) and an instance of bidirectional cross-filtering flagged as a risk signal.

If you implement the recommendations below, you’ll materially improve governance, reliability, and performance, and reduce duplicated work through centralized ingestion and shared artifacts.

---

## 2) Key Findings (What’s Working / What Needs Attention)

### What’s working well
- Star-schema designs are present and detected with “High” confidence in multiple models (good foundation for semantic consistency and performant filtering).
- The shared SQL datasource used by 3 models is clearly identified—this creates an immediate opportunity to reduce duplication via centralization.
- NYC Taxi - Folded shows an example of simple source filtering early in M, and no datasource risk flags are detected there.

### What needs attention (highest impact)
- RLS gaps: Most models explicitly show “No Row-Level Security roles are defined.”
- Non-portable source paths (local C:\Users\...), which are warned to prevent scheduled refresh in service without mitigation.
- Query folding risk: NYC Taxi - Not Folded shows multiple transformations before filtering and is explicitly flagged as a likely folding-breaker candidate (heuristic).
- Modeling limitations in single-table models: no relationships + “Not Dimensional” classification (indicating thin semantics).
- Relationship risk signals flagged in some models: bidirectional cross-filtering appears in 11-Solution-Sales Analysis and TableauWorkbooks.

---

## 3) Detailed Recommendations

### 3.1 Governance & Security (RLS/OLS)
**Recommendation A — Establish a workspace-wide RLS standard and apply it to every model that will be broadly shared.**

**Why:** Most models show no security roles defined, which is explicitly flagged as a governance risk.

**Observed pattern to reuse:** 11-Solution-Sales Analysis includes a role (“Salespeople”) using a UPN-based filter pattern (USERPRINCIPALNAME()).

**Implementation approach (practical):**
- Use the existing UPN-based pattern as a starting point where it matches your business rules.
- For models without roles, define roles based on business access rules (department, region, owner, etc.).

**Confidence:** High that security roles are missing and should be addressed (explicitly reported).

---

### 3.2 Reliability & Refresh (portable sources, service readiness)
**Recommendation B — Eliminate local file paths and migrate file-based sources to service-friendly storage (SharePoint/OneLake/gateway-backed).**

**Why:** Multiple models are flagged for non-portable local file paths, which can prevent scheduled refresh in the service.

**Implementation approach (practical):**
- Move CSV/Excel sources to a governed location (SharePoint/OneLake) and update Power Query connections accordingly.

**Confidence:** High (explicit flags).

**Recommendation C — Review and rationalize external Web.Contents dependencies.**

**Why:** One model uses Web.Contents to pull CSV resources from GitHub. This can introduce reliability, change-control, and network dependency risk.

**Implementation approach (practical):**
- If production-grade reliability is required, stage these resources into a managed storage location and source from there.

**Confidence:** Medium (impact depends on whether this workspace is demo/training vs production).

---

### 3.3 Performance (query folding, import vs DQ)
**Recommendation D — Prioritize query folding by pushing filters upstream and minimizing “folding breakers” before filters.**

**Why:** Poor folding can cause large data pulls and slower refresh. The documentation flags folding-risk candidates heuristically.

**Implementation approach (practical):**
- Reorder M steps so row filtering happens as early as possible.
- Move non-essential transformations (e.g., splitting/duplicating columns) after filtering where feasible.
- Validate folding using Power Query diagnostics or equivalent tooling.

**Confidence:** Medium–High (risk signals are explicit; folding itself requires diagnostic confirmation).

**Recommendation E — For the shared SQL datasource used by 3 models, centralize ingestion to reduce repeated work and improve performance consistency.**

**Why:** Reuse enables consistent transformations, reduces duplication, and improves maintainability.

**Implementation approach (practical):**
- Centralize the shared source using one of these patterns:
  - Dataflow Gen2 to stage into a Lakehouse
  - Lakehouse/Warehouse staging with reuse via Direct Lake or SQL endpoint
  - Shared Semantic Model with downstream live-connect/composite models

**Confidence:** High (shared datasource usage is explicit).

---

### 3.4 Data Modeling & Semantic Design
**Recommendation F — Move single-table “not dimensional” models toward a minimal star shape when they are intended for analysis beyond simple reporting.**

**Why:** Single-table models with no relationships limit semantic navigation, reuse, and consistent filtering.

**Implementation approach (practical):**
- Introduce core dimensions (e.g., Date) and relationship structure if the model is intended for broader analytical use.

**Confidence:** Medium (structure is clear; whether to change depends on intended usage).

**Recommendation G — Review and reduce risky relationship settings (bidirectional cross-filter) unless there’s a clear necessity.**

**Why:** Bidirectional cross-filtering is flagged as a relationship risk signal and can cause ambiguous filter propagation and performance issues.

**Implementation approach (practical):**
- Keep bidirectional filtering only when required and well-understood; otherwise prefer single-direction star patterns.

**Confidence:** Medium (it’s flagged; actual impact depends on measure logic and usage patterns not captured here).

---

### 3.5 Reuse & Centralization (shared sources, shared artifacts)
**Recommendation H — Implement a centralization pattern for the shared SQL source and standardize downstream consumption.**

**Why:** Centralization reduces duplication and supports consistent modeling.

**Implementation approach (practical):**
- Select a single “authoritative” staging pattern (Dataflow Gen2 → Lakehouse or Direct Lake) and update all consuming models to reuse it.

**Confidence:** High.

---

### 3.6 Naming, Organization & Maintainability
**Recommendation I — Standardize object naming (avoid spaces; prefer PascalCase or underscores) and align measure naming conventions.**

**Why:** Multiple models have objects with spaces and mixed naming, reducing consistency and programmatic compatibility.

**Implementation approach (practical):**
- Establish naming rules (tables/columns/measures), apply to new artifacts first, then remediate existing models as time allows.

**Confidence:** High (explicit warnings).

---

## 4) Implementation Steps (Phased Plan)

### Phase 1 — Stabilize (Security + Refresh)
1. Inventory who consumes each semantic model and identify which require security boundaries.
2. Implement RLS for models that will be shared broadly, starting with those currently showing no roles.
3. Migrate local file dependencies (CSV/Excel) to service-compatible storage and update queries.

### Phase 2 — Performance & Modeling Improvements
4. Refactor M queries to place filters earlier and reduce pre-filter transformations in models flagged for folding risk.
5. For the taxi examples, adopt the “folded” pattern where feasible and validate with diagnostics.
6. Review bidirectional relationships and remove unless required for specific analytic scenarios.

### Phase 3 — Reuse & Standardization
7. Centralize the shared SQL datasource using a selected pattern (Dataflow Gen2 → Lakehouse; Direct Lake; shared semantic model).
8. Apply naming standards (no spaces; consistent casing; consistent measure naming) across models.
9. Establish a lightweight semantic model checklist for future additions (RLS present, refresh path portable, relationships reviewed, naming compliant).

---

## 5) Confidence Level & Assumptions

### Overall confidence: High
This review is grounded in explicit signals in the generated documentation: RLS absence, non-portable paths, folding-risk heuristics, relationship risk signals, and shared datasource counts.

### Where confidence is lower
- Query folding: the folding analysis is heuristic and cannot prove folding without diagnostics; confirm with testing.
- Model redesign (single-table → star): whether necessary depends on business reporting goals not included in the files.

---

## 6) Appendix: Evidence Snapshot by Model

- Datasource Inventory: 6 semantic models, 7 unique datasources, 1 shared datasource used by 3 models; centralization patterns listed.
- 11-Solution-Sales Analysis: Import; star (high confidence); 1 RLS role; bidirectional cross-filter flagged; folding-risk note for Sales.
- NYC Taxi - DQ: DirectQuery; star (high confidence); no RLS roles; naming includes space-separated objects.
- NYC Taxi - Folded: Import; 1 table; no relationships; no RLS roles; early filtering; no datasource risk flags.
- NYC Taxi - Not Folded: Import; 1 table; no relationships; no RLS roles; folding breaker candidates before filter.
- S&P Company Report: Import; star (high confidence); no RLS roles; multiple non-portable local CSV paths flagged; multiple object names contain spaces.
- TableauWorkbooks: Import; star (low confidence); bidirectional cross-filter flagged; non-portable local Excel path flagged; object name contains spaces.

---

<footer>

Generated by GPT-5.2 Thinking based on data as of 2026-02-12T14:35:39-05:00 (earliest “Generated” timestamp found in the provided .md files).

</footer>
