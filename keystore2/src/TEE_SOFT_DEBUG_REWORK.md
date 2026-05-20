# TEE Soft Debug Rework Plan

This document tracks the in-tree refactor of keystore2 TEE soft debug toward
TEESimulator-equivalent outcomes (effect-level parity, not byte-level parity).

## Goals

1. Keep existing PATCH flow stable for target apps.
2. Add explicit decision layer for `PATCH | GENERATE | AUTO`.
3. Introduce ATTEST_KEY-specific path that can evolve into software key+chain generation.
4. Preserve chain consistency and observability during migration.

## Current Status

- Runtime config and keybox loading are in place.
- Chain replacement and validation are in place.
- Export-side chain telemetry is in place.
- Decision layer has been introduced in `tee_soft_debug.rs`:
  - `TeeSoftDebugMode`
  - `TeeSoftDebugAction`
  - `decide_action(...)`
- `GenerateSoftwareChain` action currently logs and returns (placeholder).

## Phase Breakdown

### Phase A: Architecture (in progress)

- Centralize mode/action decision in one place.
- Keep behavior compatible with current patch implementation.
- Add logs that indicate which action was chosen and why.

### Phase B: ATTEST_KEY software generation

- Intercept ATTEST_KEY request path and provide software-generated key + cert chain.
- Ensure the generated attest key can be referenced by later key generation.
- Persist state with deterministic key identity mapping.

### Phase C: PATCH semantic parity

- Align keybox algorithm matching and leaf re-sign behavior.
- Align attestation extension patch semantics (RoT / patch levels / IDs).
- Ensure chain verification behavior matches client verifier expectations.

### Phase D: Mode hardening

- Implement AUTO strategy gates.
- Add robust fallback policy when software generation fails.
- Minimize debug logging and keep only actionable diagnostics.

## Immediate Next Tasks

1. Thread key descriptor context into soft-debug decision where needed.
2. Implement Phase-B software generation backend for ATTEST_KEY path.
3. Gate target-app behavior so attestation key and business key flows stay coherent.
