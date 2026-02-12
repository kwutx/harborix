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
│   └── me (query)
├── user
│   ├── get (query)
│   └── update (mutation)
├── settings
│   ├── get (query)
│   └── update (mutation)
└── apps
    └── list (query)
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

## 変更履歴

- 2026-02-12: tRPCベースに全面改訂
- 2026-02-11: 初版作成（テンプレート）
