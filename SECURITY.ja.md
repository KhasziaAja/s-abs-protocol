# セキュリティポリシーおよび Tier 2 機関向けアクセス規定

*[English Version (SECURITY.en.md)](./SECURITY.en.md)*

---

## 1. 二層セキュリティ境界（公開仕様 vs. 秘匿コア）

敵対的なリバースエンジニアリング、流動性取り付け騒ぎの誘導、およびオラクルに対するスコア偽装から **S-ABS Protocol** の健全性を保護するため、コードベースと技術仕様は以下の二層に厳格に分離されている。

* **Tier 1 — 公開プロトコル仕様（本リポジトリ / MIT License）：**
  * 公開構造方程式（$\text{Cap}_i(t)$）、標準JSON-LDスキーマ、Threshold ZKP 回路インターフェース、2社間商契約テンプレート、および未解決のエンジニアリング課題（`ISSUES.ja.md`）。
  * 公開による監査可能性（オーディタビリティ）を確保することで、取引当事者および規制当局がブラックボックスを盲信することなくプロトコルの論理的整合性を検証できる。
* **Tier 2 — `[Foundry Core / Proprietary Calibration]`（非公開 / 制限付き機関アクセス）：**
  * 動的PID制御のゲイン行列（$K_p, K_i, K_d$）およびシステミック・サーキットブレーカーの発動曲線。
  * パーコレーション臨界集中閾値（$N_{\text{crit}}$）および指数関数的集中ペナルティの傾き係数。
  * 業種別の失敗報告基準分布（*沈黙の減点：Silence Haircut* の算出に使用）、信用スコア自然減衰率（$\lambda$）、および非対称 Exit Queue のジッター確率分布。
  * 検証ノードのマスター署名鍵および多国籍法域における閾値鍵セレモニーの運用仕様。

---

## 2. 脆弱性報告ポリシー（Vulnerability Disclosure Policy）

暗号学的回路（Threshold ZKP）、スマートコントラクト・エスクロー機構、非関連性グラフ検証の耐衝突性、および差分プライバシーエンドポイントに関する責任あるセキュリティ研究を歓迎する。

* **公開 Issue への脆弱性投稿の禁止：** ゼロデイ脆弱性、ZKPの健全性バグ、または経済的攻撃ベクトルを、公開の GitHub Issues や Pull Requests に投稿してはならない。
* **暗号化通信による報告：** 脆弱性レポート、形式的な概念実証（PoC）、および緩和策の提言は、下記のPGP公開鍵を用いた暗号化メール、または GitHub の非公開脆弱性報告機能（Security Advisories）を通じて提出すること。
* **対象範囲に関する注記：** Tier 1 インターフェースに対する理論的攻撃を実証した有効な報告については、リリースノートにて正式なクレジット表記（謝辞）を行う対象となる。

---

## 3. 機関・再保険向けアクセス規定（Foundry Core）

`[Foundry Core / Proprietary Calibration]`、本番環境の Reinsurance Actuarial Feeds（再保険数理フィード）、および Tier 1 保証金預託型検証ノードの統合は、適格性が検証された機関エンティティに限定される。

* **対象となる適格機関：**
  * 認可を受けた再保険会社、再保険シンジケート、およびアクチュアリー（保険数理）モデリングチーム。
  * 規制下の商業銀行、RWAカストディアン、および機関財務オペレーター。
  * 中央銀行および金融監督当局（無償のマクロ・システミック監視API認証情報の発行対象）。
  * `TIER_2_FULL_ESCROW` の導入要件を満たす事業法人。
* **検証要件：**
  * 匿名アカウントまたは未検証の個人アカウントからの問い合わせは自動的に破棄される。
  * 申請機関は、検証可能な法人ドメイン資格情報、規制上の登録識別子（LEI等）、および明確な統合スコープを提示する必要がある。

---

## 4. 暗号学的連絡窓口および PGP フィンガープリント

セキュリティ上の脆弱性報告、または機関導入に関するお問い合わせは、以下の指定 PGP 公開鍵ブロックを使用して暗号化通信にて連絡を開始すること。

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

*(注：機密性の高い法人資料や保険数理データセットは、送信前に必ず上記のPGP公開鍵で暗号化すること。)*