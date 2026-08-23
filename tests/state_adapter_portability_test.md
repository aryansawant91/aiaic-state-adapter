# State Adapter Portability Test

**Author:** Aryan Sawant  
**Task:** Test 2 — State Configuration / Adapter Model  
**Date:** August 2026  
**Status:** Isolation build — pre-VC review  

---

## Purpose

This document proves that the same AIAIC Core can operate against two
structurally different state configurations using the same schema,
without any modification to Core logic between deployments.

The test is pass/fail per criterion. A single silent semantic override
or schema violation constitutes a test failure.

---

## Test Setup
Same AIAIC Core
↓
state_adapter.schema.json v1.0.0
↓
├── Configuration A → state_a_madhya_pradesh.example.json
└── Configuration B → state_b_maharashtra.example.json


Both configurations must:
- Validate against the same schema
- Represent a deployable state context
- Not require any Core modification between them
- Not silently override any Core semantic

---

## Test A — Madhya Pradesh Configuration

**File:** `state_adapter/examples/state_a_madhya_pradesh.example.json`

### A1 — Schema Validation

| Check | Expected | Result |
|---|---|---|
| schema_version present | PASS | ✅ PASS |
| state_identity complete | PASS | ✅ PASS |
| geography tiers defined | PASS | ✅ PASS |
| agencies block present | PASS | ✅ PASS |
| policy_references present | PASS | ✅ PASS |
| data_sources array present | PASS | ✅ PASS |
| integration_endpoints array present | PASS | ✅ PASS |
| deployment block present | PASS | ✅ PASS |
| evidence block present | PASS | ✅ PASS |
| No additional properties outside schema | PASS | ✅ PASS |

### A2 — State Identity Check

| Field | Value | Valid |
|---|---|---|
| name | Madhya Pradesh | ✅ |
| code | IN-MP | ✅ |
| language | Hindi | ✅ |
| locale | hi-IN | ✅ |

### A3 — Geography Structure

| Tier | Label | Notes |
|---|---|---|
| 1 | State | ✅ data available |
| 2 | Division | ✅ data available |
| 3 | District | ✅ data available |
| 4 | Tehsil | ✅ data available — MP uses Tehsil, not Taluka |
| 5 | Gram Panchayat | ⚠️ data NOT available — marked correctly |
| 6 | Village | ⚠️ data NOT available — marked correctly |

Note: MP uses **Tehsil** at tier 4. This differs from Maharashtra which
uses **Taluka**. Both are valid — the Core does not hardcode tier names.
This is the adapter working correctly.

### A4 — Verification Status Audit

| Element | Verification Status | Acceptable |
|---|---|---|
| Agriculture department | VERIFIED | ✅ |
| MSP procurement agency (MARKFED) | VERIFIED | ✅ |
| Farmer registration body | UNVERIFIED | ✅ marked honestly |
| Mandi price data source | VERIFIED | ✅ |
| Kisan portal integration | KNOWN_UNKNOWN | ✅ marked correctly |
| Overall evidence status | PARTIAL | ✅ honest — not falsely VERIFIED |

### A5 — Workflow Override Check

| Override | Type | Additive Only | Rationale Present |
|---|---|---|---|
| mp-bby-eligibility-check | ADDITIONAL_NOTIFICATION | ✅ | ✅ |

No core workflow steps removed. Override is additive. ✅

### A6 — Constitutional Boundary Check

| Boundary Rule | Violated |
|---|---|
| Universal AIAIC semantics changed | ❌ Not violated |
| Income calculation logic overridden | ❌ Not violated |
| Provenance rules bypassed | ❌ Not violated |
| Core validation removed | ❌ Not violated |
| Unknown resolved as verified | ❌ Not violated |
| State name hardcoded into Core | ❌ Not violated |

**Test A Result: PASS**

---

## Test B — Maharashtra Configuration

**File:** `state_adapter/examples/state_b_maharashtra.example.json`

### B1 — Schema Validation

| Check | Expected | Result |
|---|---|---|
| schema_version present | PASS | ✅ PASS |
| state_identity complete | PASS | ✅ PASS |
| geography tiers defined | PASS | ✅ PASS |
| agencies block present | PASS | ✅ PASS |
| policy_references present | PASS | ✅ PASS |
| data_sources array present | PASS | ✅ PASS |
| integration_endpoints array present | PASS | ✅ PASS |
| deployment block present | PASS | ✅ PASS |
| evidence block present | PASS | ✅ PASS |
| No additional properties outside schema | PASS | ✅ PASS |

### B2 — State Identity Check

| Field | Value | Valid |
|---|---|---|
| name | Maharashtra | ✅ |
| code | IN-MH | ✅ |
| language | Marathi | ✅ |
| locale | mr-IN | ✅ |

### B3 — Geography Structure

| Tier | Label | Notes |
|---|---|---|
| 1 | State | ✅ data available |
| 2 | Division | ✅ data available |
| 3 | District | ✅ data available |
| 4 | Taluka | ✅ data available — MH uses Taluka, not Tehsil |
| 5 | Gram Panchayat | ⚠️ data NOT available — marked correctly |
| 6 | Village | ⚠️ data NOT available — marked correctly |

Note: Maharashtra uses **Taluka** at tier 4. MP uses **Tehsil**.
Same Core, different tier label — portability confirmed at this level.

### B4 — Verification Status Audit

| Element | Verification Status | Acceptable |
|---|---|---|
| Agriculture department | VERIFIED | ✅ |
| MSAMB procurement agency | VERIFIED | ✅ |
| 7/12 land records data source | VERIFIED | ✅ |
| PoCRA data source | UNVERIFIED | ✅ marked honestly |
| MahaBhulekh API integration | KNOWN_UNKNOWN | ✅ marked correctly |
| Overall evidence status | PARTIAL | ✅ honest — not falsely VERIFIED |

### B5 — Workflow Override Check

| Override | Type | Additive Only | Rationale Present |
|---|---|---|---|
| mh-7-12-land-verification | ADDITIONAL_VALIDATION | ✅ | ✅ |
| mh-pocra-zone-check | ADDITIONAL_STEP | ✅ | ✅ |

Both overrides are additive. No core steps removed. ✅

Maharashtra has 2 workflow overrides vs MP's 1 — this is expected and
correct. The schema supports any number of additive overrides.

### B6 — Constitutional Boundary Check

| Boundary Rule | Violated |
|---|---|
| Universal AIAIC semantics changed | ❌ Not violated |
| Income calculation logic overridden | ❌ Not violated |
| Provenance rules bypassed | ❌ Not violated |
| Core validation removed | ❌ Not violated |
| Unknown resolved as verified | ❌ Not violated |
| State name hardcoded into Core | ❌ Not violated |

**Test B Result: PASS**

---

## Portability Comparison — MP vs Maharashtra

| Dimension | Madhya Pradesh | Maharashtra | Same Core Required |
|---|---|---|---|
| Schema version | 1.0.0 | 1.0.0 | ✅ Same schema |
| Tier 4 label | Tehsil | Taluka | ✅ Core doesn't hardcode |
| Language | Hindi | Marathi | ✅ Core doesn't hardcode |
| Unique data source | MP Fasal Girdawari | MH 7/12 Land Records | ✅ Both valid references |
| Unique scheme | Bhavantar Bhugtan Yojana | PoCRA | ✅ Both are references only |
| Workflow overrides | 1 | 2 | ✅ Additive — Core unchanged |
| MSP enforced | true | true | ✅ Both reference MSP |
| Connectivity mode | HYBRID | HYBRID | ✅ |
| Evidence status | PARTIAL | PARTIAL | ✅ Both honest |

**Portability Test Result: PASS**
Same Core, same schema, two structurally different valid state deployments.

---

## Negative Tests

These tests attempt to violate the schema or constitutional boundaries.
Each must be rejected or flagged — not silently accepted.

---

### N1 — Insert Unknown Capability

**Attempt:** Add a field `"core_capability_override": "custom_income_logic"`
to the configuration.

**Expected behaviour:** Schema validation fails —
`additionalProperties: false` rejects any field not declared in the schema.

**Result:** ✅ REJECTED — schema has `additionalProperties: false`
at root level and on all nested objects.

---

### N2 — Reference Unsupported Configuration Field

**Attempt:** Add `"unsupported_field": "some_value"` inside `deployment`.

**Expected behaviour:** Schema validation fails —
`deployment` object also has `additionalProperties: false`.

**Result:** ✅ REJECTED — nested objects are also locked.

---

### N3 — Alter Core Semantic Through Configuration

**Attempt:** Add a workflow override of type `"REMOVE_STEP"` to remove
a core validation step.

**Expected behaviour:** Schema validation fails — `workflow_overrides`
type enum only allows:
`ADDITIONAL_STEP`, `ADDITIONAL_VALIDATION`, `ADDITIONAL_NOTIFICATION`.
`REMOVE_STEP` is not a valid enum value.

**Result:** ✅ REJECTED — enum constraint prevents removal type.

---

### N4 — Omit Required Provenance Metadata

**Attempt:** Submit a configuration without the `evidence` block.

**Expected behaviour:** Schema validation fails —
`evidence` is in the `required` array at root level.

**Result:** ✅ REJECTED — required field missing causes validation failure.

---

### N5 — Use Unresolved Source as Verified Configuration

**Attempt:** Set `verification_status: "VERIFIED"` on a data source
that has no `source_url` and no `last_verified` date, with a note
claiming it is confirmed.

**Expected behaviour:** This is a governance concern, not purely a
schema concern. The schema requires `verification_status` and `id`
and `name` — but cannot automatically verify the truthfulness of
a VERIFIED claim. This is by design — configuration legitimacy
is governed by GC / TMS, not by the Adapter schema alone.

**Result:** ⚠️ PARTIAL — schema accepts the field value but
legitimacy verification remains an external governance responsibility.
This is a known limitation documented in `known_limitations.md`.

---

### N6 — Missing Required schema_version

**Attempt:** Submit configuration without `schema_version` field.

**Expected behaviour:** Validation fails — `schema_version` is required.

**Result:** ✅ REJECTED — required field check fails.

---

### N7 — Invalid Status Enum Value

**Attempt:** Set a data source `status` to `"MAYBE"` instead of
a valid enum value.

**Expected behaviour:** Schema validation fails — status enum only
allows `AVAILABLE`, `RESTRICTED`, `UNAVAILABLE`, `KNOWN_UNKNOWN`.

**Result:** ✅ REJECTED — enum constraint rejects invalid value.

---

## Summary

| Test | Description | Result |
|---|---|---|
| Test A | MP configuration validates and passes all checks | ✅ PASS |
| Test B | Maharashtra configuration validates and passes all checks | ✅ PASS |
| Portability | Same Core + same schema works for both states | ✅ PASS |
| N1 | Unknown capability field rejected | ✅ PASS |
| N2 | Unsupported nested field rejected | ✅ PASS |
| N3 | Core step removal attempt rejected | ✅ PASS |
| N4 | Missing provenance block rejected | ✅ PASS |
| N5 | Unverified source claimed as verified | ⚠️ PARTIAL — governance gap |
| N6 | Missing schema_version rejected | ✅ PASS |
| N7 | Invalid enum value rejected | ✅ PASS |

**Overall: 9/10 PASS, 1 PARTIAL (governance gap — by design, documented)**