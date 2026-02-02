## Context

Next.js 16で`middleware.ts`が非推奨となり、`proxy.ts`への移行が必要。現在の`src/middleware.ts`はSupabaseのセッション更新処理のみを行うシンプルな実装で、認証状態の維持に使用されている。

現状の構成:
- ファイル: `src/middleware.ts`
- エクスポート関数: `middleware`
- ランタイム: Node.js（Edgeではない）
- 処理内容: `updateSession`によるSupabaseセッション更新

## Goals / Non-Goals

**Goals:**
- `pnpm dev`実行時の非推奨警告を解消する
- Next.js 16の新しい規約（`proxy.ts`）に準拠する
- 既存の認証フローを維持する

**Non-Goals:**
- 認証ロジックの変更や機能追加
- matcherパターンの変更
- Server Layout Guardsへの移行（将来的な検討事項）

## Decisions

### 1. ファイル配置: `src/proxy.ts`

**決定**: `src/middleware.ts`を`src/proxy.ts`にリネーム

**理由**: Next.js 16の公式ドキュメントによると、`proxy.ts`はプロジェクトルートまたは`src`ディレクトリに配置可能。既存の`src/`構成を維持することで、ファイル構造の一貫性を保つ。

**代替案**:
- ルート直下への配置 → 却下: 既存の`src/`ベースの構成から外れる

### 2. 関数エクスポート: named export

**決定**: `export async function proxy(request: NextRequest)`

**理由**: Next.js公式が推奨するnamed export形式を採用。codemodeと互換性があり、将来のアップグレードでも安定。

**代替案**:
- default export → 却下: named exportが公式推奨

### 3. ヘルパー関数の維持

**決定**: `@/lib/supabase/middleware`の`updateSession`はそのまま使用

**理由**: proxy.tsから呼び出す内部ヘルパーであり、ファイル規約の変更とは無関係。リネームするとSupabaseの公式ドキュメントや他の参照との整合性が損なわれる。

### 4. matcherの維持

**決定**: 既存のmatcherパターンをそのまま移行

**理由**: proxy.tsでもmatcher構文は同一。静的アセットとAPIを除外する現在のパターンは適切に機能している。

## Risks / Trade-offs

### リスク: codemodとの互換性

**リスク**: 手動移行時にcodemodが期待する形式と異なる可能性

**緩和策**: シンプルな移行のため、手動で確実に実施。変更後にビルドとdev serverで動作確認。

※ codemod: Next.jsが提供する自動コード変換ツール（`npx @next/codemod@canary middleware-to-proxy .`）

### リスク: Edgeランタイムへの誤設定

**リスク**: proxy.tsではruntimeオプションがサポートされないため、設定するとエラーになる

**緩和策**: 現在の実装にはruntime設定がないため問題なし。今後追加しないよう注意。