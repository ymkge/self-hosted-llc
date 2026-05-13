# self-hosted-llc

エンジニアが民間サービス（マネーフォワード/freee等）を使わず、AIと政府公式ツールのみを利用して、法定費用最小構成で合同会社（LLC）を設立・運用するためのナレッジベースです。

## 🚀 Concept
- **Zero Subscription**: 設立・維持のための民間サービス課金を回避する。
- **As Code**: 定款や事業目的をドキュメントとして管理し、AIで生成する。
- **Direct Access**: 政府の「法人設立ワンストップサービス」を直接叩く。

## 💰 Expected Cost (合同会社)
| 項目 | 費用 | 備考 |
| :--- | :--- | :--- |
| 定款印紙代 | 0円 | 電子定款のため |
| 登録免許税 | 60,000円 | 法定費用（最低額） |
| 法人実印作成 | 約5,000円〜 | オンラインショップ等 |
| **合計** | **約65,000円** | 民間サービス利用料(年約4万円)をカット |

## 🛠 Tech Stack / Tools
- **AI (Gemini / ChatGPT)**: 定款ドラフト生成、事業目的の最適化
- **法人設立ワンストップサービス**: 電子申請・登記・税務署届出の一括処理
- **マイナポータルアプリ**: 電子署名用（マイナンバーカード使用）
- **VS Code / Markdown**: 書類管理・タスク管理

## 📋 Roadmap

### Phase 1: Preparation (設計)
- [ ] 会社基本情報の決定（商号、本店所在地、資本金、決算期）
- [ ] AIを活用した事業目的（Business Objectives）の策定
- [ ] 法人実印（代表社員之印）の発注（詳細は [Setup Guide](./docs/setup_guide.md) 参照）

### Phase 2: Build (書類作成)
- [ ] AIによる定款ドラフトの生成
- [ ] 代表社員就任承諾書、払込証明書の作成
- [ ] 資本金の入金と通帳コピー（PDF）の準備

### Phase 3: Deploy (電子申請)
- [ ] 法人設立ワンストップサービスへのログイン
- [ ] 電子定款のアップロードと電子署名（詳細は [Setup Guide](./docs/setup_guide.md) 参照）
- [ ] 登録免許税（6万円）の納付（ペイジー等）

### Phase 4: Post-Deployment (事後処理)
- [ ] 登記簿謄本（履歴事項全部証明書）の取得
- [ ] 法人銀行口座の開設
- [ ] 税務署・自治体への設立届出（ワンストップサービス内で完結可能）

## 📝 Directory Structure
```text
.
├── documents/          # AIで生成した定款や書類のテンプレート
|    ├── 01_articles_of_incorporation.md  # 定款ドラフト
|    ├── 02_business_objectives.md        # 事業目的メモ
|    ├── 03_registration_forms/           # 添付書類テンプレート
|    │   ├── README.md                    # 資料作成の注意点など
|    │   ├── acceptance_of_office.md      # 就任承諾書
|    │   ├── certificate_of_payment.md    # 払込証明書
|    │   └── location_decision.md         # 本店所在地決定書
|    └── 04_post_registration/            # 設立後のタスク
|        ├── bank_account_opening.md      # 銀行口座開設リサーチ
|        └── tax_notifications.md         # 税務署への届出項目
├── docs/               # 詳細ガイド・マニュアル
│   └── setup_guide.md  # 物理準備と電子署名手順
├── scripts/            # 手続き自動化やリサーチ用メモ
└── README.md
```

# ⚠️ Disclaimer
本リポジトリの内容は個人の経験に基づくものであり、法的助言を構成するものではありません。実際の設立にあたっては、法務局や専門家の最新情報を必ず確認してください。