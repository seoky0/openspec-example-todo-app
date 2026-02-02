## Why

Next.js 16で`middleware.ts`が非推奨となり、`proxy.ts`への移行が推奨されている。`pnpm dev`実行時に警告ログが表示されるため、新しい規約に対応する必要がある。

## What Changes

- `src/middleware.ts`を`src/proxy.ts`にリネーム
- エクスポート関数名を`middleware`から`proxy`に変更
- Supabaseのセッション更新ヘルパー関数名を`updateSession`のまま維持（内部実装のため変更不要）
- matcherの設定はそのまま維持

## Capabilities

### New Capabilities

なし（既存機能の移行のみ）

### Modified Capabilities

なし（振る舞いの変更なし、ファイル規約の変更のみ）

## Impact

- **コード**: `src/middleware.ts` → `src/proxy.ts`
- **依存関係**: 変更なし（`@supabase/ssr`、`next/server`は引き続き使用）
- **API**: 変更なし（プロキシの振る舞いは同一）
- **ランタイム**: Node.jsランタイムで実行（Edge Runtimeは非サポート）
