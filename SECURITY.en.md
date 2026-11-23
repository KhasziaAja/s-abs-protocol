# Security Policy & Tier 2 Institutional Access

*[日本語版はこちら (SECURITY.ja.md)](./SECURITY.ja.md)*

---

## 1. Two-Tier Security Boundary (Public vs. Proprietary)

To protect the integrity of the **S-ABS Protocol** against adversarial reverse-engineering, liquidity-run manipulation, and oracle gaming, the codebase and specifications are strictly partitioned into two tiers:

* **Tier 1 — Public Specification (This Repository / MIT License):**
  * Open mathematical structures ($\text{Cap}_i(t)$), standard JSON-LD schemas, Threshold ZKP circuit interfaces, bilateral commercial contract templates, and open engineering challenges (`ISSUES.en.md`).
  * Public auditability ensures that counterparties and regulators can verify protocol logic without trusting a black box.
* **Tier 2 — `[Foundry Core / Proprietary Calibration]` (Private / Restricted Access):**
  * Dynamic PID control gain matrices ($K_p, K_i, K_d$) and systemic circuit-breaker trigger curves.
  * Percolation critical concentration thresholds ($N_{\text{crit}}$) and exponential concentration tax slopes.
  * Sector-specific failure reporting baselines (used to compute the *Silence Haircut*), demurrage decay rates ($\lambda$), and asymmetric exit-queue jitter distributions.
  * Master validator signing keys and multi-jurisdictional threshold key ceremonies.

---

## 2. Vulnerability Disclosure Policy

We welcome responsible security research covering our cryptographic circuits (Threshold ZKP), smart-contract escrow mechanics, graph-independence collision resistance, and differential-privacy endpoints.

* **Do Not File Public Issues:** Never disclose zero-day vulnerabilities, ZKP soundness bugs, or economic exploit vectors in public GitHub Issues or Pull Requests.
* **Encrypted Channel:** Submit vulnerability reports, formal proofs-of-concept (PoCs), and suggested mitigations via PGP-encrypted email to the protocol security contact below, or via GitHub Private Vulnerability Reporting (Security Advisories).
* **Scope Note:** Valid reports demonstrating theoretical exploits against Tier 1 interfaces are eligible for formal attribution in release notes.

---

## 3. Institutional & Reinsurance Access (Foundry Core)

Access to `[Foundry Core / Proprietary Calibration]`, production Reinsurance Actuarial Feeds, and Tier 1 bonded validator integration is restricted to verified institutional entities:

* **Eligible Institutions:**
  * Licensed reinsurers, reinsurance syndicates, and actuarial modeling teams.
  * Regulated commercial banks, RWA custodians, and institutional treasury operators.
  * Central banks and financial supervisory authorities (eligible for zero-cost macro-systemic monitoring API credentials).
  * Enterprise entities onboarding onto `TIER_2_FULL_ESCROW`.
* **Verification Requirements:**
  * Inquiries from anonymous or unverified personal accounts are automatically discarded.
  * Applicants must provide verifiable corporate domain credentials, regulatory registration identifiers (e.g., LEI), and an explicit integration scope.

---

## 4. Cryptographic Contact & PGP Fingerprint

For security disclosures or institutional onboarding inquiries, initiate contact using the designated PGP public key block below:

```text
Contact Channel : KhasziaAja@proton.me
PGP Fingerprint : DA07 BEED A199 9007 EACB  A185 8841 4523 4142 CE3C
```

```pgp
-----BEGIN PGP PUBLIC KEY BLOCK-----

xjMEawLERhYJKwYBBAHaRw8BAQdAQyBAhWSx+9U/2joh4uwJNuaXgc3G96FX
SLRorFqXyf7NK0toYXN6aWFBamFAcHJvdG9uLm1lIDxLaGFzemlhQWphQHBy
b3Rvbi5tZT7CwBAEExYKAIIFgmsCxEYDCwkHCRCIQUUjQULOPEUUAAAAAAAc
ACBzYWx0QG5vdGF0aW9ucy5vcGVucGdwanMub3Jnc+PMJPqPNAC2L+0NehP2
Xp76A1KxtbxriuRGYc9A7A4DFQoIAxYAAgIZAQKbAwIeARYhBNoHvu2hmZAH
6suhhYhBRSNBQs48AAC8AwEAjzXxD80M7SWvBQ80OVVnm4kg0JyfFZdwxJpu
azAaDoYBANIfgw2ZouFzaCgxn+WDM6BX8/O6Ev3G12m/zgleI04GzjgEawLE
RhIKKwYBBAGXVQEFAQEHQLXF0rfqlBxAjjBXvY1X7nc4Yrk3LjP8mPqATYmj
tE9IAwEKCcK+BBgWCgBwBYJrAsRGCRCIQUUjQULOPEUUAAAAAAAcACBzYWx0
QG5vdGF0aW9ucy5vcGVucGdwanMub3Jn50yg5mwf5XH3Q4muUAUBnVh2kN7J
kP0Mx6rAvXjqD7cCmwwWIQTaB77toZmQB+rLoYWIQUUjQULOPAAAMecBAPQ3
Kc402vsGNeltpCs/3wUt75qunT52pnB8EXhZHBFqAQDrOPi2alh6mRvreAqQ
1YQzhgApxbzfuyQR3XrZFVRkAg==
=70sD
-----END PGP PUBLIC KEY BLOCK-----
```

*(Note: Ensure all sensitive corporate documentation or actuarial datasets are encrypted using the PGP key above prior to transmission.)*