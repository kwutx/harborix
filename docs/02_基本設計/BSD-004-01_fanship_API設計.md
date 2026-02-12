# BSD-004-01: fanship API設計

## 概要

fanship（推し活管理アプリ）のAPI設計を定義する。

## 設計方針

| 項目 | 方針 |
| ---- | ---- |
| 言語 | Go 1.21+ |
| フレームワーク | Gin |
| 認証 | AWS Cognito JWT |
| バリデーション | go-playground/validator |
| DB接続 | sqlx |
| ホスティング | Fly.io |

## API一覧

### 推し管理

| メソッド | エンドポイント | 概要 |
| -------- | -------------- | ---- |
| GET | /oshi | 推し一覧取得 |
| POST | /oshi | 推し登録 |
| POST | /oshi/bulk | 推し一括登録 |
| GET | /oshi/:id | 推し詳細取得 |
| PUT | /oshi/:id | 推し更新 |
| DELETE | /oshi/:id | 推し削除 |

### イベント管理

| メソッド | エンドポイント | 概要 |
| -------- | -------------- | ---- |
| GET | /events | イベント一覧取得 |
| POST | /events | イベント登録 |
| GET | /events/:id | イベント詳細取得 |
| PUT | /events/:id | イベント更新 |
| DELETE | /events/:id | イベント削除 |
| PUT | /events/:id/status | ステータス変更 |
| GET | /events/calendar | カレンダー取得 |

### グッズ管理

| メソッド | エンドポイント | 概要 |
| -------- | -------------- | ---- |
| GET | /goods | グッズ一覧取得 |
| POST | /goods | グッズ登録 |
| POST | /goods/bulk | グッズ一括登録 |
| GET | /goods/:id | グッズ詳細取得 |
| PUT | /goods/:id | グッズ更新 |
| DELETE | /goods/:id | グッズ削除 |

### 支出管理

| メソッド | エンドポイント | 概要 |
| -------- | -------------- | ---- |
| GET | /expenses | 支出一覧取得 |
| POST | /expenses | 支出登録 |
| GET | /expenses/:id | 支出詳細取得 |
| PUT | /expenses/:id | 支出更新 |
| DELETE | /expenses/:id | 支出削除 |
| GET | /expenses/stats | 支出統計取得 |

### 共有管理

| メソッド | エンドポイント | 概要 |
| -------- | -------------- | ---- |
| POST | /share | 共有リンク生成 |
| GET | /share | 共有リンク一覧取得 |
| DELETE | /share/:id | 共有リンク削除 |
| GET | /p/:token | 公開ページ取得 |

### フォロー管理

| メソッド | エンドポイント | 概要 |
| -------- | -------------- | ---- |
| GET | /following | フォロー一覧取得 |
| POST | /following | フォロー登録 |
| DELETE | /following/:id | フォロー解除 |

### カテゴリ管理

| メソッド | エンドポイント | 概要 |
| -------- | -------------- | ---- |
| GET | /genres | ジャンル一覧取得 |
| POST | /genres | ジャンル追加 |
| GET | /goods-types | グッズ種類一覧取得 |
| POST | /goods-types | グッズ種類追加 |

## API詳細

（各APIの詳細仕様は詳細設計時に記載）

## 認証・認可

### JWT検証

```go
func VerifyCognitoToken(tokenString string) (*Claims, error) {
    // Cognito JWKSから公開鍵を取得して検証
}
```

### ミドルウェア

```go
func AuthMiddleware() gin.HandlerFunc {
    return func(c *gin.Context) {
        // Authorization ヘッダーからJWT取得・検証
        token := c.GetHeader("Authorization")
        // ...
    }
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
    "message": "推しの名前は必須です"
  }
}
```

## WebSocket API

### リアルタイム更新

| イベント | 概要 |
| -------- | ---- |
| follow:update | フォロー先の更新通知 |

## 変更履歴

- 2026-02-12: Go/Gin技術詳細を追加
- 2026-02-11: 初版作成（テンプレート）
