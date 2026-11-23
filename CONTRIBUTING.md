# Contributing to S-ABS Protocol

*[日本語版はこちら (CONTRIBUTING.ja.md)](./CONTRIBUTING.ja.md)*

Thank you for your interest in contributing to the **S-ABS Protocol** (Dynamic Credit Buffer Attestation & Actuarial Risk Protocol). We welcome contributions from quantitative finance researchers, control theorists, cryptographers, legal scholars, and enterprise FinTech engineers.

---

## 1. Scope of Contributions (Two-Tier Boundary)

Before submitting an Issue or Pull Request, please review our two-tier architecture boundary (`SECURITY.en.md`):

* **In Scope (Tier 1 — Public Specification):**
  * **Open Engineering Challenges:** Reference implementations, agent-based models (ABM), ZKP circuit optimizations, and SDK adapters addressing `ISSUES.en.md` (`ISSUE-01` through `ISSUE-16`).
  * **Multi-Jurisdictional Legal Localization:** Comparative legal analysis, bilateral liquidated damages contract templates, and international arbitration seat mappings (e.g., NY, SG, CH, JP, EU).
  * **Documentation & Schema Validation:** Clarifications, translations, and JSON Schema validation test suites (`schemas/*.json`).
* **Out of Scope (Tier 2 — Proprietary Calibration):**
  * Pull Requests attempting to hardcode specific production PID gain constants ($K_p, K_i, K_d$), percolation critical thresholds ($N_{\text{crit}}$), sector-specific silence-penalty baselines, or exit-queue jitter seeds into Tier 1 specifications will be declined to preserve systemic security against adversarial gaming.

---

## 2. Contribution Tracks & Submission Standards

### Track A: Quantitative Finance & Control Engineering (`ISSUE-05` to `ISSUE-08`)
* **Format:** Reproducible simulation code (Python / Rust / Julia) accompanied by a concise Markdown report summarizing assumptions, stress scenarios (e.g., 2008 GFC, 2020 liquidity freeze), and stability metrics.
* **Directory:** Place simulation proposals under `simulations/proposals/<issue-id>-<short-name>/`.
* **Requirement:** All models must use parameterized placeholders for Tier 2 calibration constants so they can be independently evaluated by the Protocol Stewardship Council.

### Track B: Cryptography, ZKP Circuits & Oracles (`ISSUE-09` to `ISSUE-12`)
* **Format:** Circuit implementations (Circom, Halo2, Noir) or formal verification proofs demonstrating soundness, zero-knowledge properties, and sub-second verification latency.
* **Requirement:** Include deterministic test vectors verifying compliance with `schemas/dynamic-credit-pass-v1.0.json` and `schemas/telemetry-ingest-v1.0.json`.

### Track C: Legal Localization & Regulatory Mapping (`ISSUE-01` to `ISSUE-04`)
* **Format:** Structured legal memoranda or localized contract schedules saved under `legal/localizations/<jurisdiction-code>/` (e.g., `US-NY`, `SG`, `CH`, `JP`).
* **Requirement:** Explicitly cite relevant statutory provisions, commercial contract precedents, and New York Convention (1958) enforceability considerations, maintaining strict separation from regulated insurance underwriting.

### Track D: Enterprise Connectors & Actuarial Adapters (`ISSUE-13` to `ISSUE-16`)
* **Format:** Read-only Open Banking / ERP connectors, R&D failure telemetry ETL adapters, or Excess-of-Loss (XoL) reinsurance actuarial mapping specifications.
* **Requirement:** Validate all sample payloads against `schemas/*.json`.

---

## 3. Pull Request Workflow

1. **Fork & Branch:** Fork this repository and create a descriptive branch (`feat/issue-09-zkp-circuit` or `legal/jp-commercial-schedule`).
2. **Verify Schemas:** If modifying or referencing data payloads, ensure strict compliance with JSON Schema Draft 2020-12 definitions in `schemas/`.
3. **Neutral Tone:** Maintain objective, specification-grade technical and legal prose consistent with existing RFC drafts.
4. **Submit PR:** Reference the target Issue number (`Closes #XX` or `Addresses ISSUE-XX`) in your Pull Request description.

---

## 4. Security & Responsible Disclosure

* **Never submit zero-day vulnerabilities, ZKP soundness flaws, or economic exploit PoCs via public Pull Requests or Issues.**
* Follow the PGP-encrypted disclosure process detailed in [`SECURITY.en.md`](./SECURITY.en.md).

---

## 5. License

By contributing to this repository, you agree that your contributions will be licensed under the **MIT License** (`LICENSE`).