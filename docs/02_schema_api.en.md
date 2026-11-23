# Standard JSON Schemas & API Specifications

[*日本語版はこちら (02_schema_api.ja.md)*](./02_schema_api.ja.md)

> **Document Status**: RFC Draft v1.0 (Public Tier 1 Specification)  
> **Scope**: Standard JSON-LD payloads for `Dynamic Credit Pass`, `Threshold ZKP` attestation interfaces, `Cryptographic Provenance Oracle` ingestion schemas, and `Reinsurance Actuarial Feeds`.

---

## 1. Design Principles for System Interoperability

To function as an open, low-layer credit and risk protocol, S-ABS exposes strictly typed, stateless JSON/REST and gRPC interfaces. External systems—including Open Banking APIs, ERP ledgers, R&D Failure Repositories, Cryptographic Provenance Oracles, and Reinsurance Actuarial Engines—interact exclusively through these public schemas without requiring access to `[Foundry Core / Proprietary Calibration]`.

---

## 2. Standard Schema: `Dynamic Credit Pass` (v1.0)

The `Dynamic Credit Pass` is the machine-verifiable credential issued to an entity $i$ at timestamp $t$. It exposes verified solvency tiering and active `Cap` commitments while masking underlying raw asset balances.

```json
{
  "$schema": "https://s-abs.org/schemas/credit-pass/v1.0.json",
  "pass_id": "sabs:pass:8f92a1c4-77b0-4e91-9d2a-1104c8b9e012",
  "protocol_version": "1.0.0",
  "issued_at": "2026-11-22T06:00:00Z",
  "expires_at": "2026-11-25T06:00:00Z",
  "demurrage_half_life_active": true,
  "entity": {
    "did": "did:web:enterprise-alpha.example.com",
    "jurisdiction_primary": "CH",
    "graph_independence_verified": true
  },
  "solvency_attestation": {
    "tier": "TIER_2_FULL_ESCROW",
    "credit_rank": "AAA",
    "consecutive_clean_days": 1460,
    "verified_cap_floor": {
      "amount": 50000000,
      "currency": "USD",
      "attestation_mode": "THRESHOLD_ZKP_BOUND"
    },
    "collateral_profile": {
      "multi_jurisdictional_dispersion": true,
      "jurisdictions": ["CH", "SG", "AE"],
      "asset_classes": ["SOVEREIGN_DEBT", "PHYSICAL_BULLION_RWA", "INFRA_CAPACITY"],
      "yield_pass_through_enabled": true,
      "min_timelock_hours": 72
    }
  },
  "telemetry_metabolism": {
    "provenance_oracle_bound": true,
    "negative_entropy_bonus_active": true,
    "silence_penalty_status": "CLEAR"
  },
  "legal_settlement_binding": {
    "framework": "BILATERAL_LIQUIDATED_DAMAGES",
    "arbitration_seat": "SIAC_SINGAPORE",
    "ny_convention_enforceable": true
  },
  "cryptographic_proof": {
    "zkp_circuit_id": "sabs-cap-threshold-v1",
    "zkp_proof_hex": "0x19a8f0c3e7b2...94d1",
    "validator_quorum_signatures": [
      {
        "validator_id": "did:sabs:validator:node-ch-01",
        "staked_collateral_verified": true,
        "signature": "0x8b4e21...f09a"
      }
    ]
  }
}
```

### Key Field Definitions
* `verified_cap_floor`: Declares the minimum guaranteed self-coverage amount ($\text{Cap}_i(t)$) proven via ZKP without revealing exact upper-bound reserves.
* `multi_jurisdictional_dispersion`: Boolean flag confirming collateral is distributed across non-correlated jurisdictions to neutralize single-state freeze risks.
* `silence_penalty_status`: Indicates whether the entity's operational failure reporting frequency aligns with sector statistical baselines (`CLEAR`, `WARNING`, `HAIRCUT_APPLIED`).
* `validator_quorum_signatures`: Cryptographic signatures from bonded validator nodes whose staked collateral is subject to 100% slashing upon audit failure.

---

## 3. Ingestion Schema: Provenance Oracle & Failure Telemetry

To compute the **Negative Entropy Feedback** multiplier $\mathcal{F}(\text{Logs}_i)$ and enforce the **Silence Haircut**, S-ABS ingests standardized, privacy-preserving telemetry packets from external **Cryptographic Provenance Oracles** and **R&D Failure Repositories**.

```json
{
  "$schema": "https://s-abs.org/schemas/telemetry-ingest/v1.0.json",
  "telemetry_id": "sabs:tel:3c71e9a0-12b4-4f88-a019-5541d2e8c901",
  "entity_did": "did:web:enterprise-alpha.example.com",
  "timestamp": "2026-11-22T05:45:12Z",
  "sector_code": "ISIC_2100_PHARMA_RND",
  "provenance_verification": {
    "hardware_tee_attested": true,
    "thermodynamic_cost_verified": true,
    "causal_lineage_root": "0x7f83b1657ff1fc53b92dc18148a1d65dfc2d4b1fa3d677284addd200126d9069",
    "integrity_score_normalized": 0.96
  },
  "failure_entropy_metrics": {
    "event_type": "BOUNDARY_EXPLORATION_FAILURE",
    "payload_disclosed": false,
    "zkp_frontier_expansion_proof": "0x44c2a9...11e0"
  }
}
```

---

## 4. Core API Endpoints (REST / JSON)

### 4.1 `POST /v1/zkp/verify-threshold` (Threshold ZKP Attestation)
Allows a prospective counterparty to verify whether Entity $i$ holds sufficient $\text{Cap}_i(t)$ to cover a specific transaction requirement $R_{\text{req}}$ (`YES` / `NO`), without exposing raw balance-sheet data.

**Request Payload:**
```json
{
  "target_entity_did": "did:web:enterprise-alpha.example.com",
  "counterparty_did": "did:web:supplier-beta.example.com",
  "required_cap_usd": 25000000,
  "settlement_currency": "USD",
  "proposed_duration_hours": 720
}
```

**Response Payload:**
```json
{
  "request_id": "req-99281-ab4f",
  "evaluated_at": "2026-11-22T06:05:00Z",
  "threshold_satisfied": true,
  "graph_independence_check": "PASS",
  "macro_governor_status": "NORMAL_REGIME",
  "zkp_attestation_receipt": {
    "circuit": "sabs-cap-threshold-v1",
    "public_inputs_hash": "0x9e21...44b8",
    "proof": "0x3a7f...88c1"
  }
}
```
*(Note: If `graph_independence_check` detects high UBO or transactional correlation between `target_entity_did` and `counterparty_did`, instant automated settlement is disabled and routed to milestone-based escrow release.)*

### 4.2 `POST /v1/escrow/lock-commitment` (Dynamic Lien & Yield Pass-Through)
Binds a verified buffer slice to a specific **Bilateral Liquidated Damages Agreement**, activating the programmable time-lock while maintaining 100% native yield routing to the depositor.

**Request Payload:**
```json
{
  "pass_id": "sabs:pass:8f92a1c4-77b0-4e91-9d2a-1104c8b9e012",
  "contract_hash": "0x5d41402abc4b2a76b9719d911017c592",
  "committed_cap_usd": 25000000,
  "lock_duration_hours": 720,
  "arbitration_clause_id": "SIAC-MODEL-2026-V1"
}
```

### 4.3 `GET /v1/actuarial/reinsurance-feed/{entity_did}` (Reinsurance Actuarial Feed)
Provides standardized, privacy-preserving risk metrics to authorized **Reinsurance Actuarial Engines** for pricing excess-of-loss (XoL) tail coverage above the entity's self-absorbed $\text{Cap}$.

**Response Payload:**
```json
{
  "entity_did": "did:web:enterprise-alpha.example.com",
  "feed_timestamp": "2026-11-22T06:10:00Z",
  "self_insured_retention_floor_usd": 50000000,
  "empirical_risk_metrics": {
    "continuous_audit_uptime_ratio": 0.9998,
    "verified_failure_logging_percentile": 88.5,
    "hub_concentration_index": "LOW_DISPERSION",
    "actuarial_loss_probability_modifier": 0.72
  },
  "oracle_signature": "0x7c11...e402"
}
```

### 4.4 `GET /v1/regulator/macro-systemic-monitor` (Privacy-Preserving Regulatory API)
Provided free of charge to central banks and financial supervisory authorities. Exposes aggregated, differentially private systemic stress indicators without leaking individual corporate counterparty graphs.

**Response Payload:**
```json
{
  "timestamp": "2026-11-22T06:15:00Z",
  "network_aggregate_metrics": {
    "total_verified_buffer_liquidity_band": "10B_TO_50B_USD",
    "macro_pid_governor_regime": "NOMINAL",
    "percolation_hub_stress_index": 0.14,
    "circuit_breaker_proximity": "GREEN"
  },
  "differential_privacy_epsilon": 0.1
}
```

---

*Note: Internal scoring weights, PID gain matrices, and differential privacy noise generators powering these endpoints reside within `[Foundry Core / Proprietary Calibration]`.*