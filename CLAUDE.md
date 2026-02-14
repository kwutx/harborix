# CLAUDE.md

このファイルはClaude Codeがこのリポジトリで作業する際のガイドラインを提供する。

## プロジェクト概要

**Harborix** - 拡張可能なダッシュボード基盤（Harbor + ix = 港のように様々なアプリが集まる拠点）

- モノレポ構成（npm workspaces）
- 開発体制: 1人 + Claude Code
- 開発手法: ウォーターフォール

## 技術スタック

| レイヤー | 技術 |
| -------- | ---- |
| 認証 | AWS Cognito + Amplify |
| フロントエンド | React + Vite + TanStack |
| バックエンド | Rust (Axum) / Go (Gin) / Next.js Server Actions |
| データベース | Supabase PostgreSQL |
| UIライブラリ | @harborix/ui（自作） |
| インフラ | Vercel (FE) + Fly.io (BE) + Terraform |

## ディレクトリ構成

```text
harborix/
├── packages/
│   ├── ui/              # 共通UIライブラリ
│   ├── harborix-web/    # ダッシュボード (React + tRPC)
│   ├── harborix-api/    # ダッシュボードAPI (Node.js + Fastify + tRPC)
│   ├── coinship-web/    # 家計簿フロント (React)
│   ├── coinship-api/    # 家計簿API (Rust + Axum)
│   ├── libship/         # 読書管理 (Next.js App Router + Server Actions)
│   ├── fanship-web/     # 推し活フロント (React)
│   └── fanship-api/     # 推し活API (Go + Gin)
├── infra/               # Terraform
├── docs/                # ドキュメント
│   ├── 00_管理/
│   ├── 01_要件定義/
│   ├── 02_基本設計/
│   ├── 03_詳細設計/
│   └── 04_テスト/
└── scripts/             # 共通スクリプト
```

## セキュリティガイドライン

### 機密情報の取り扱い

- `.env`ファイル、APIキー、パスワード、トークンは絶対にコミットしない
- 機密情報は環境変数で管理する
- サンプル設定ファイルは`.env.example`として提供する

### コーディング時のセキュリティ

- **入力バリデーション**: すべてのユーザー入力を検証・サニタイズする
- **SQLインジェクション対策**: パラメータ化クエリを使用する
- **XSS対策**: 出力時に適切にエスケープする
- **認証・認可**: 適切なアクセス制御を実装する

## コミットルール

- 日本語で記述
- 変更内容を簡潔に説明
- 1コミット1目的（小さく頻繁にコミット）
- 機密情報を含むコミットは禁止

## マークダウン記法ルール

### 空行ルール

- リストの前後には空行を入れる
- 見出し（`##`, `###`）の前後には空行を入れる
- テーブルの前後には空行を入れる

### テーブル記法

- パイプ（`|`）の両側にスペースを入れる

```markdown
| 項目 | 内容 |
| ---- | ---- |
| A    | B    |
```

## ドキュメント命名規則

| フェーズ | プレフィックス |
| -------- | -------------- |
| 要件定義 | REQ-XXX_ |
| 基本設計 | BSD-XXX_ |
| 詳細設計 | DSD-XXX_ |
| テスト | TST-XXX_ |

## 関連ドキュメント

- docs/00_管理/ - プロジェクト管理ドキュメント
- docs/01_要件定義/ - 要件定義書
- docs/02_基本設計/ - 基本設計書
- docs/03_詳細設計/ - 詳細設計書
- docs/04_テスト/ - テスト関連ドキュメント
