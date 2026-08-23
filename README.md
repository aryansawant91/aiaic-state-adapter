# AIAIC State Adapter — Configuration Model

**Author:** Aryan Sawant  
**Task:** Test 2 — State Configuration / Adapter Model  
**Intake:** AIAIC 7-4-3  
**Status:** Isolation build — pre-VC review  
**Date:** August 2026  

---

## What This Repository Contains

The State Adapter configuration model that allows AIAIC to operate
across different Indian states without modifying Core product logic.

This is not a running system. It is a configuration design — a schema,
two example state configurations, architecture documentation, and
portability evidence.

---

## The Core Problem This Solves

Without a State Adapter, state-specific logic creeps into Core:

```python
# WRONG — what this adapter prevents
if state == "MP":
    run_bhavantar_scheme_logic()
elif state == "Maharashtra":
    run_pocra_zone_check()
```

With the State Adapter, Core stays universal:
AIAIC Core
↓
Load State Adapter config
↓
Core capabilities execute with state context
↓
State A or State B — same Core, different config

---

## Repository Structure

aiaic-state-adapter/
├── README.md ← This file
├── docs/
│ └── architecture/
│ ├── state_adapter_model.md ← What the adapter is + design rules
│ └── core_vs_state_adapter_matrix.md ← What belongs where
├── state_adapter/
│ ├── README.md ← Schema usage guide
│ ├── schema/
│ │ └── state_adapter.schema.json ← Machine-readable config schema
│ └── examples/
│ ├── state_a_madhya_pradesh.example.json
│ └── state_b_maharashtra.example.json
└── tests/
└── state_adapter_portability_test.md ← Two-state portability proof

---

## Key Design Rules

**What the State Adapter MAY configure:**
- State identity, language, locale
- Geography tier names and data availability
- Department and agency mappings
- Scheme and policy references (references only — not logic)
- Dataset availability and access status
- Integration endpoint availability
- Additive workflow steps
- Deployment and infrastructure constraints

**What the State Adapter must NEVER do:**
- Change universal AIAIC income calculation logic
- Redefine provenance rules
- Bypass validation steps
- Remove core workflow steps
- Resolve unknown information as verified
- Hard-code state names into Core logic

---

## Configuration Categories

```json
{
  "schema_version": "1.0.0",
  "state_identity": {},
  "geography": {},
  "agencies": {},
  "policy_references": {},
  "data_sources": [],
  "integration_endpoints": [],
  "workflow_overrides": [],
  "deployment": {},
  "evidence": {}
}
```

---

## Two-State Portability Test Results

| State | Schema Valid | Boundary Check | Result |
|---|---|---|---|
| Madhya Pradesh | ✅ | ✅ | PASS |
| Maharashtra | ✅ | ✅ | PASS |
| Same Core required | — | — | ✅ CONFIRMED |

Full test results in `tests/state_adapter_portability_test.md`

---

## Handover Note

**WHAT IS CONFIGURABLE**
Geography hierarchy, department mappings, scheme references, dataset
availability, integration endpoints, additive workflow steps,
deployment constraints, procurement route references.

**WHAT IS CORE**
Income calculation logic, provenance rules, validation steps,
governance, capability definitions, replay traceability.

**WHAT REMAINS UNKNOWN**
Core workflow identifiers (pending Hemanth / repo access),
integration endpoint details (pending Sakshi),
dataset identifiers used by AIAIC internally (pending Aman),
provenance field structure (pending Nupur),
policy classification outputs (pending Sangyan / Ankita).

**WHAT NEEDS EXTERNAL INTEGRATION**
Government system APIs (MSAMB, MARKFED, MahaBhulekh),
farmer registration portals, mandi price feeds.

**WHAT MUST NOT BE HARD-CODED**
State names, tier labels, scheme names, department names,
policy logic, geography boundaries — all of these belong
in the State Adapter, never in Core product logic.

---

## Known Limitations

See `docs/architecture/state_adapter_model.md` Section 9 and
`known_limitations.md` (in review packet if applicable) for full
list of known unknowns pending team inputs.

This configuration was built in isolation without access to the
AIAIC repository or other team members' outputs. Schema and
architecture will require alignment with Hemanth at the VC review.