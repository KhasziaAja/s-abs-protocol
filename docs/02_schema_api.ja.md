# 標準JSONスキーマおよびAPIインターフェース仕様

[*English Version (02_schema_api.en.md)*](./02_schema_api.en.md)

> **文書ステータス**: RFC ドラフト v1.0（Public Tier 1 仕様）
>
> **対象範囲**: `Dynamic Credit Pass` の標準JSON-LDペイロード、`Threshold ZKP` 証明インターフェース、`Cryptographic Provenance Oracle` 取り込みスキーマ、および `Reinsurance Actuarial Feeds`（再保険数理フィード）。

## 1. システム相互運用性のための設計原則

オープンな低レイヤー信用・リスクプロトコルとして機能するため、S-ABSは厳密に型定義されたステートレスなJSON/RESTおよびgRPCインターフェースを公開する。Open Banking API、ERP元帳、R&D失敗リポジトリ、暗号学的来歴オラクル、再保険数理エンジンなどの外部システムは、`[Foundry Core / Proprietary Calibration]` へのアクセスを必要とせず、これらの公開スキーマのみを介して連携する。

## 2. 標準スキーマ：`Dynamic Credit Pass` (v1.0)

`Dynamic Credit Pass` は、時点 $t$ においてエンティティ $i$ に発行される機械検証可能なクレデンシャルである。背後にある生の資産残高を秘匿したまま、検証済みの支払能力ランクおよび有効な確定自前補償枠（$\text{Cap}$）のコミットメントを公開する。

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

### 主要フィールド定義

* `verified_cap_floor`: 正確な準備金の上限額を明かすことなく、ゼロ知識証明（ZKP）によって証明された最低保証自前補償額（$\text{Cap}_i(t)$）を宣言する。
* `multi_jurisdictional_dispersion`: 単一国家による凍結リスクを無力化するため、担保が非相関の複数法域に分散配置されていることを確認するブール値フラグ。
* `silence_penalty_status`: エンティティの運用失敗報告頻度が業種別統計ベースラインと整合しているかを示すステータス（`CLEAR`、`WARNING`、`HAIRCUT_APPLIED`）。
* `validator_quorum_signatures`: 監査失敗時に預託保証金が100%没収（スラッシング）される検証ノード群による暗号学的署名。

## 3. 取り込みスキーマ：来歴オラクルおよび失敗テレメトリ

**負のエントロピー還元ループ（Negative Entropy Feedback）** 係数 $\mathcal{F}(\text{Logs}_i)$ の算出および **沈黙の減点（Silence Haircut）** の執行を行うため、S-ABSは外部の **暗号学的来歴オラクル（Cryptographic Provenance Oracles）** および **R&D失敗リポジトリ（R&D Failure Repositories）** から標準化されたプライバシー保護テレメトリを受信する。

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

## 4. コアAPIエンドポイント (REST / JSON)

### 4.1 `POST /v1/zkp/verify-threshold` (閾値ゼロ知識証明検証)

取引相手候補が、エンティティ $i$ の生の貸借対照表データを閲覧することなく、特定の取引要求額 $R_{\text{req}}$ をカバーするのに十分な $\text{Cap}_i(t)$ を保有しているか（`YES` / `NO`）を検証するためのエンドポイント。

**リクエストペイロード:**

```json
{
  "target_entity_did": "did:web:enterprise-alpha.example.com",
  "counterparty_did": "did:web:supplier-beta.example.com",
  "required_cap_usd": 25000000,
  "settlement_currency": "USD",
  "proposed_duration_hours": 720
}
```

**レスポンスペイロード:**

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

*(注：`graph_independence_check` において `target_entity_did` と `counterparty_did` の間に実質的支配者（UBO）や取引履歴の高度な相関が検知された場合、即時自動清算は無効化され、第三者監査を伴うマイルストーン型段階解放エスクローへルーティングされる。)*

### 4.2 `POST /v1/escrow/lock-commitment` (動的質権および利回り透過ロック)

検証済みバッファーの一部を特定の **2社間事前損害賠償予定約定（Bilateral Liquidated Damages Agreement）** に紐付け、預託者への100%利回り還元を維持したままプログラマブル・タイムロックを有効化する。

**リクエストペイロード:**

```json
{
  "pass_id": "sabs:pass:8f92a1c4-77b0-4e91-9d2a-1104c8b9e012",
  "contract_hash": "0x5d41402abc4b2a76b9719d911017c592",
  "committed_cap_usd": 25000000,
  "lock_duration_hours": 720,
  "arbitration_clause_id": "SIAC-MODEL-2026-V1"
}
```

### 4.3 `GET /v1/actuarial/reinsurance-feed/{entity_did}` (再保険数理フィード)

認可された **再保険数理エンジン（Reinsurance Actuarial Engines）** に対し、エンティティの自己吸収枠 $\text{Cap}$ を超える超過損害再保険（XoL）のテールリスク料率を算定するための標準化・プライバシー保護リスク指標を提供する。

**レスポンスペイロード:**

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

### 4.4 `GET /v1/regulator/macro-systemic-monitor` (プライバシー保護型・規制当局監視API)

中央銀行および金融監督当局に対して無償で提供されるエンドポイント。個別企業の取引先グラフを漏洩させることなく、差分プライバシー処理を施したシステミック・ストレス指標の集計値を公開する。

**レスポンスペイロード:**

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

*注：これらのエンドポイントを駆動する内部スコアリングの重み付け、PIDゲイン行列、および差分プライバシーノイズ生成器は `[Foundry Core / Proprietary Calibration]` 内に隔離されている。*