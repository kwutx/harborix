# BSD-003-01: libship API設計

## 概要

libship（読書管理アプリ）のAPI設計を定義する。Next.js App RouterのServer Actionsを使用する。

## 設計方針

| 項目 | 方針 |
| ---- | ---- |
| スタイル | Next.js Server Actions |
| 認証 | AWS Cognito JWT（サブドメイン共有httpOnly Cookie） |
| バリデーション | Zod |
| データ取得 | React Server Components |

## Server Actions構成

### ディレクトリ構成

```text
app/
├── actions/
│   ├── books.ts
│   ├── categories.ts
│   ├── reviews.ts
│   ├── quotes.ts
│   └── stats.ts
└── lib/
    ├── auth.ts
    └── db.ts
```

## Actions一覧

### 本の管理 (books.ts)

| Action | 概要 | 入力 |
| ------ | ---- | ---- |
| getBooks | 本一覧取得 | { status?, categoryId? } |
| getBook | 本詳細取得 | { id } |
| createBook | 本登録 | BookInput |
| updateBook | 本更新 | { id, data: BookInput } |
| deleteBook | 本削除 | { id } |
| searchByIsbn | ISBN検索 | { isbn } |
| updateProgress | 進捗記録 | { id, currentPage } |
| updateStatus | 状態変更 | { id, status } |

### カテゴリ管理 (categories.ts)

| Action | 概要 | 入力 |
| ------ | ---- | ---- |
| getCategories | カテゴリ一覧取得 | なし |
| createCategory | カテゴリ作成 | CategoryInput |
| updateCategory | カテゴリ更新 | { id, data: CategoryInput } |
| deleteCategory | カテゴリ削除 | { id } |

### 感想管理 (reviews.ts)

| Action | 概要 | 入力 |
| ------ | ---- | ---- |
| getReview | 感想取得 | { bookId } |
| upsertReview | 感想保存 | { bookId, content } |

### 引用管理 (quotes.ts)

| Action | 概要 | 入力 |
| ------ | ---- | ---- |
| getQuotes | 引用一覧取得 | { bookId } |
| createQuote | 引用追加 | { bookId, content, page?, memo? } |
| updateQuote | 引用更新 | { id, data: QuoteInput } |
| deleteQuote | 引用削除 | { id } |

### 統計 (stats.ts)

| Action | 概要 | 入力 |
| ------ | ---- | ---- |
| getReadingStats | 読書統計取得 | { year?, month? } |
| getCategoryStats | カテゴリ別統計取得 | なし |

## 認証・認可

### サーバーサイド認証

```typescript
// lib/auth.ts
import { cookies } from 'next/headers'
import { verifyCognitoToken } from '@/lib/cognito'

export async function getUser() {
  const cookieStore = await cookies()
  const token = cookieStore.get('harborix_access_token')?.value
  if (!token) return null
  // Cognito公開鍵で署名検証・有効期限確認
  return verifyCognitoToken(token)
}
```

### Action内での認証チェック

```typescript
'use server'

import { getUser } from '@/lib/auth'

export async function getBooks() {
  const user = await getUser()
  if (!user) throw new Error('Unauthorized')

  // データ取得処理
}
```

## バリデーション

### Zodスキーマ例

```typescript
import { z } from 'zod'

export const bookInputSchema = z.object({
  title: z.string().min(1).max(200),
  author: z.string().max(100).optional(),
  isbn: z.string().max(20).optional(),
  totalPages: z.number().positive().optional(),
})

export type BookInput = z.infer<typeof bookInputSchema>
```

## エラーハンドリング

### Action戻り値

```typescript
type ActionResult<T> =
  | { success: true; data: T }
  | { success: false; error: string }
```

## 変更履歴

- 2026-02-12: Cookie認証をサブドメイン共有httpOnly Cookie方式に更新
- 2026-02-12: Server Actionsベースに全面改訂
- 2026-02-11: 初版作成（テンプレート）
