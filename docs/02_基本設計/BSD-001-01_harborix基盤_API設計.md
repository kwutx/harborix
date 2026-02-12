# BSD-001-01: harborix基盤 API設計

## 概要

harborix基盤のAPI設計を定義する。tRPCを使用したエンドツーエンドの型安全なAPIを実装する。

## API設計方針

| 項目 | 方針 |
| ---- | ---- |
| スタイル | tRPC v11 |
| 認証 | AWS Cognito JWT |
| クライアント | @trpc/react-query |
| バリデーション | Zod |

## tRPC構成

### Router構成

```text
appRouter
├── auth
│   ├── me (query)                    # 現在のユーザー情報取得
│   └── refreshToken (mutation)       # トークンリフレッシュ・Cookie再設定
├── user
│   ├── get (query)                   # ユーザー情報取得
│   ├── update (mutation)             # プロフィール更新
│   └── delete (mutation)             # アカウント削除
├── settings
│   ├── get (query)                   # 設定取得
│   └── update (mutation)             # 設定更新（テーマ等）
├── apps
│   ├── list (query)                  # アプリ一覧取得
│   └── userSettings (router)         # ユーザーアプリ設定
│       ├── list (query)              # 設定一覧取得
│       └── update (mutation)         # 並び替え・非表示設定
└── admin
    └── apps (router)                 # 管理者専用（is_admin=true）
        ├── create (mutation)         # アプリ登録
        ├── update (mutation)         # アプリ編集
        ├── delete (mutation)         # アプリ削除
        └── togglePublish (mutation)  # 公開/非公開切替
```

### Context

| プロパティ | 型 | 説明 |
| ---------- | -- | ---- |
| user | User \| null | 認証済みユーザー情報 |
| session | Session \| null | Cognitoセッション |

### Procedure種別

| 種別 | 説明 | 認証 |
| ---- | ---- | ---- |
| publicProcedure | 公開API | 不要 |
| protectedProcedure | 認証必須API | 必要 |

### Cognitoが担当する機能

以下の機能はAWS Cognito / Amplify UIが提供し、harborix APIでは実装しない。

| 機能 | 担当 | 備考 |
| ---- | ---- | ---- |
| ユーザー登録（AUTH-001） | Cognito + Amplify UI | Authenticatorコンポーネント |
| ログイン（AUTH-002） | Cognito + Amplify UI | メール/パスワード認証 |
| ログアウト（AUTH-003） | Cognito + Amplify UI | Cookieの削除はharborix APIで処理 |
| パスワードリセット（AUTH-004） | Cognito + Amplify UI | ForgotPasswordコンポーネント |
| Googleログイン（AUTH-006） | Cognito Federation | OAuthプロバイダー設定 |
| メールアドレス変更（USER-004） | Cognito User Pool API | Amplify SDK経由 |
| パスワード変更（USER-005） | Cognito User Pool API | Amplify SDK経由 |

### 管理者ミドルウェア

```typescript
const isAdmin = t.middleware(({ ctx, next }) => {
  if (!ctx.user?.isAdmin) {
    throw new TRPCError({ code: 'FORBIDDEN' })
  }
  return next({ ctx: { user: ctx.user } })
})

const adminProcedure = t.procedure.use(isAuthed).use(isAdmin)
```

## 認証・認可

### JWT検証フロー

```text
1. リクエストヘッダーからJWT取得
2. Cognito公開鍵で署名検証
3. トークンの有効期限確認
4. user_idをcontextに設定
```

### ミドルウェア

```typescript
const isAuthed = t.middleware(({ ctx, next }) => {
  if (!ctx.user) {
    throw new TRPCError({ code: 'UNAUTHORIZED' })
  }
  return next({ ctx: { user: ctx.user } })
})

const protectedProcedure = t.procedure.use(isAuthed)
```

## エラーハンドリング

### tRPCエラーコード

| コード | 用途 |
| ------ | ---- |
| BAD_REQUEST | リクエスト不正 |
| UNAUTHORIZED | 認証エラー |
| FORBIDDEN | 権限エラー |
| NOT_FOUND | リソース未存在 |
| INTERNAL_SERVER_ERROR | サーバーエラー |

## API詳細

### auth.me

| 項目 | 内容 |
| ---- | ---- |
| 種別 | query |
| 認証 | 必要 |
| 入力 | なし |
| 出力 | User |

### user.update

| 項目 | 内容 |
| ---- | ---- |
| 種別 | mutation |
| 認証 | 必要 |
| 入力 | { name?: string, avatarUrl?: string } |
| 出力 | User |

### settings.get

| 項目 | 内容 |
| ---- | ---- |
| 種別 | query |
| 認証 | 必要 |
| 入力 | なし |
| 出力 | UserSettings |

### settings.update

| 項目 | 内容 |
| ---- | ---- |
| 種別 | mutation |
| 認証 | 必要 |
| 入力 | { theme?: 'light' \| 'dark' \| 'system' } |
| 出力 | UserSettings |

### apps.list

| 項目 | 内容 |
| ---- | ---- |
| 種別 | query |
| 認証 | 必要 |
| 入力 | なし |
| 出力 | App[] |

### apps.userSettings.list

| 項目 | 内容 |
| ---- | ---- |
| 種別 | query |
| 認証 | 必要 |
| 入力 | なし |
| 出力 | UserAppSetting[] |

### apps.userSettings.update

| 項目 | 内容 |
| ---- | ---- |
| 種別 | mutation |
| 認証 | 必要 |
| 入力 | { appId: string, isVisible?: boolean, sortOrder?: number } |
| 出力 | UserAppSetting |

### admin.apps.create

| 項目 | 内容 |
| ---- | ---- |
| 種別 | mutation |
| 認証 | 必要（管理者のみ） |
| 入力 | { name: string, slug: string, description?: string, icon?: string, url: string } |
| 出力 | App |

### admin.apps.update

| 項目 | 内容 |
| ---- | ---- |
| 種別 | mutation |
| 認証 | 必要（管理者のみ） |
| 入力 | { id: string, name?: string, description?: string, icon?: string, url?: string } |
| 出力 | App |

### admin.apps.delete

| 項目 | 内容 |
| ---- | ---- |
| 種別 | mutation |
| 認証 | 必要（管理者のみ） |
| 入力 | { id: string } |
| 出力 | void |

### admin.apps.togglePublish

| 項目 | 内容 |
| ---- | ---- |
| 種別 | mutation |
| 認証 | 必要（管理者のみ） |
| 入力 | { id: string, isActive: boolean } |
| 出力 | App |

## 変更履歴

- 2026-02-12: Cognito責務分担を明記、管理者API・ユーザーアプリ設定APIを追加
- 2026-02-12: tRPCベースに全面改訂
- 2026-02-11: 初版作成（テンプレート）
