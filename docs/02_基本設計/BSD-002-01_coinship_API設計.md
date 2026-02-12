# BSD-002-01: coinship API設計

## 概要

coinship（家計簿アプリ）のAPI設計を定義する。

## 設計方針

| 項目 | 方針 |
| ---- | ---- |
| 言語 | Rust |
| フレームワーク | Axum |
| 認証 | AWS Cognito JWT |
| バリデーション | validator crate |
| シリアライズ | serde |
| DB接続 | sqlx |
| ホスティング | Fly.io |

## API一覧

### 口座管理

| メソッド | エンドポイント | 概要 |
| -------- | -------------- | ---- |
| GET | /accounts | 口座一覧取得 |
| POST | /accounts | 口座作成 |
| GET | /accounts/:id | 口座詳細取得 |
| PUT | /accounts/:id | 口座更新 |
| DELETE | /accounts/:id | 口座削除 |
| POST | /accounts/:id/adjust | 残高調整 |
| POST | /accounts/transfer | 口座間振替 |

### 支払い方法管理

| メソッド | エンドポイント | 概要 |
| -------- | -------------- | ---- |
| GET | /payment-methods | 支払い方法一覧取得 |
| POST | /payment-methods | 支払い方法作成 |
| GET | /payment-methods/:id | 支払い方法詳細取得 |
| PUT | /payment-methods/:id | 支払い方法更新 |
| DELETE | /payment-methods/:id | 支払い方法削除 |

### 取引管理

| メソッド | エンドポイント | 概要 |
| -------- | -------------- | ---- |
| GET | /transactions | 取引一覧取得 |
| POST | /transactions | 取引作成 |
| GET | /transactions/:id | 取引詳細取得 |
| PUT | /transactions/:id | 取引更新 |
| DELETE | /transactions/:id | 取引削除 |

### カテゴリ管理

| メソッド | エンドポイント | 概要 |
| -------- | -------------- | ---- |
| GET | /categories | カテゴリ一覧取得 |
| POST | /categories | カテゴリ作成 |
| PUT | /categories/:id | カテゴリ更新 |
| DELETE | /categories/:id | カテゴリ削除 |

### パートナー共有

| メソッド | エンドポイント | 概要 |
| -------- | -------------- | ---- |
| GET | /partnerships | パートナー一覧取得 |
| POST | /partnerships/invite | パートナー招待 |
| POST | /partnerships/:id/accept | 招待承諾 |
| DELETE | /partnerships/:id | 共有解除 |

### 集計

| メソッド | エンドポイント | 概要 |
| -------- | -------------- | ---- |
| GET | /reports/summary | 月別サマリー取得 |
| GET | /reports/category | カテゴリ別集計取得 |
| GET | /reports/balance-history | 残高推移取得 |

## API詳細

（各APIの詳細仕様は詳細設計時に記載）

## 認証・認可

### JWT検証

```rust
use jsonwebtoken::{decode, DecodingKey, Validation};

pub async fn verify_cognito_token(token: &str) -> Result<Claims, Error> {
    // Cognito JWKSから公開鍵を取得して検証
}
```

### ミドルウェア

```rust
pub async fn auth_middleware(
    State(state): State<AppState>,
    mut request: Request,
    next: Next,
) -> Result<Response, StatusCode> {
    // Authorization ヘッダーからJWT取得・検証
}
```

## レスポンス形式

### 成功時

```json
{
  "data": { },
  "meta": { "total": 100, "page": 1 }
}
```

### エラー時

```json
{
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "金額は1以上を指定してください"
  }
}
```

## 変更履歴

- 2026-02-12: Rust/Axum技術詳細を追加
- 2026-02-11: 初版作成（テンプレート）
