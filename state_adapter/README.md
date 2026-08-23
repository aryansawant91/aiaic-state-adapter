# State Adapter — Schema Usage Guide

---

## How to Use the Schema

Every state configuration must validate against:
`state_adapter/schema/state_adapter.schema.json`

Validate using any JSON Schema validator (draft-07):

```bash
# Using ajv-cli (Node.js)
npx ajv validate -s state_adapter/schema/state_adapter.schema.json \
  -d state_adapter/examples/state_a_madhya_pradesh.example.json

# Using jsonschema (Python)
pip install jsonschema
python -c "
import json, jsonschema
schema = json.load(open('state_adapter/schema/state_adapter.schema.json'))
config = json.load(open('state_adapter/examples/state_a_madhya_pradesh.example.json'))
jsonschema.validate(config, schema)
print('VALID')
"
```

---

## Verification Status Values

| Value | Meaning |
|---|---|
| `VERIFIED` | Confirmed from official source — source URL required |
| `UNVERIFIED` | Referenced but not yet confirmed |
| `KNOWN_UNKNOWN` | Known gap — deliberately marked, not guessed |

**Never use VERIFIED without a source reference.**
**Never resolve KNOWN_UNKNOWN by guessing.**

---

## Adding a New State

1. Copy an existing example file
2. Replace all state-specific values
3. Validate against the schema
4. Mark all unconfirmed fields as UNVERIFIED or KNOWN_UNKNOWN
5. Set evidence.verification_status to PARTIAL until fully confirmed
6. Submit for GC authority review before marking as canonical