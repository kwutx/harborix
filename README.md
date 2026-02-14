# Harborix

拡張可能なダッシュボード基盤（Harbor + ix = 港のように様々なアプリが集まる拠点）

## プロジェクト概要

harborix は複数アプリを統合管理するダッシュボード基盤である。
統一された認証・UI基盤の上に、目的別のアプリ（ship）を搭載する構成をとる。

```text
harborix（港）
├── coinship ⛵ 家計簿アプリ
├── libship  ⛵ 読書管理アプリ
└── fanship  ⛵ 推し活管理アプリ
```

## アプリ一覧

| アプリ | 概要 | フロントエンド | バックエンド |
| ------ | ---- | -------------- | ------------ |
| harborix基盤 | ダッシュボード・認証・共通UI | React + tRPC | Node.js (Fastify + tRPC) |
| coinship | 家計簿（パートナー共有対応） | React | Rust (Axum) |
| libship | 読書管理（ISBN検索・読書記録） | Next.js (App Router) | Next.js Server Actions |
| fanship | 推し活管理（イベント・グッズ・支出） | React | Go (Gin) |

## 技術スタック

### 共通基盤

| 項目 | 技術 |
| ---- | ---- |
| 認証 | AWS Cognito + Amplify |
| SSO | サブドメイン共有 httpOnly Cookie |
| DB | Supabase PostgreSQL |
| 共通UI | @harborix/ui (React + Tailwind CSS) |
| バリデーション | Zod |

### インフラ

| 項目 | 技術 |
| ---- | ---- |
| FEホスティング | Vercel |
| BEホスティング | Fly.io (Docker) |
| IaC | Terraform |
| CI/CD | GitHub Actions |

## リポジトリ構成

```text
harborix/
├── packages/
│   ├── ui/              # @harborix/ui - 共通UIライブラリ
│   ├── harborix-web/    # ダッシュボード (React + tRPC)
│   ├── harborix-api/    # ダッシュボードAPI (Node.js + Fastify + tRPC)
│   ├── coinship-web/    # 家計簿フロント (React)
│   ├── coinship-api/    # 家計簿API (Rust + Axum)
│   ├── libship/         # 読書管理 (Next.js App Router + Server Actions)
│   ├── fanship-web/     # 推し活フロント (React)
│   └── fanship-api/     # 推し活API (Go + Gin)
├── infra/               # Terraform
├── docs/                # ドキュメント
└── scripts/             # 共通スクリプト
```

## ドキュメント

設計ドキュメントは `docs/` 配下にフェーズごとに管理している。

| フェーズ | ディレクトリ | プレフィックス | 状態 |
| -------- | ------------ | -------------- | ---- |
| 管理 | `docs/00_管理/` | - | 作成中 |
| 要件定義 | `docs/01_要件定義/` | REQ-XXX_ | 完了 |
| 基本設計 | `docs/02_基本設計/` | BSD-XXX_ | 完了 |
| 詳細設計 | `docs/03_詳細設計/` | DSD-XXX_ | 未着手 |
| テスト | `docs/04_テスト/` | TST-XXX_ | 未着手 |

### ドキュメント一覧

#### 要件定義（REQ）

| ID | 内容 |
| -- | ---- |
| REQ-000 | 共通（非機能要件） |
| REQ-001 | harborix基盤（概要・機能一覧・ユースケース・画面一覧・データ一覧） |
| REQ-002 | coinship（概要・機能一覧・ユースケース・画面一覧・データ一覧） |
| REQ-003 | libship（概要・機能一覧・ユースケース・画面一覧・データ一覧） |
| REQ-004 | fanship（概要・機能一覧・ユースケース・画面一覧・データ一覧） |

#### 基本設計（BSD）

| ID | 内容 |
| -- | ---- |
| BSD-000 | 共通（技術スタック・アーキテクチャ・UI設計・セキュリティ設計・インフラ設計） |
| BSD-001 | harborix基盤（API設計・DB設計・画面設計） |
| BSD-002 | coinship（API設計・DB設計・画面設計） |
| BSD-003 | libship（API設計・DB設計・画面設計） |
| BSD-004 | fanship（API設計・DB設計・画面設計） |

## 開発ステータス

- [x] 要件定義
- [x] 基本設計
- [ ] 詳細設計
- [ ] 実装
- [ ] テスト

## License

MIT
