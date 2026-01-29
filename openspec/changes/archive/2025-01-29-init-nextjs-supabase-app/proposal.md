## Why

現在このリポジトリにはアプリケーションコードが存在しない。Next.jsとSupabaseを用いたアプリケーション開発を開始できる状態にするため、開発環境の基盤を整える必要がある。

## What Changes

- Next.js（App Router）プロジェクトの初期化
- Supabase CLIによるローカル開発環境のセットアップ
- Supabaseクライアントライブラリの設定
- 基本的なディレクトリ構造の作成

## Capabilities

### New Capabilities

- `dev-environment`: Next.js + Supabase（ローカル）の開発環境構成

### Modified Capabilities

（なし - 新規プロジェクト）

## Impact

- **依存関係**: Next.js, @supabase/supabase-js, @supabase/ssr, Tailwind CSS, supabase-cli
- **ローカル環境**: Supabase CLIでローカルSupabaseを起動して開発
- **環境変数**: ローカルSupabase用の接続情報を設定
