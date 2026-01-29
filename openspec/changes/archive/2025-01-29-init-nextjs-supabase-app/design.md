## Context

新規プロジェクトとしてNext.js + Supabaseを用いたアプリケーション開発を開始するための基盤を構築する。現在リポジトリにはOpenSpecの設定ファイルのみが存在する状態。

このChangeでは開発環境のセットアップのみを行い、具体的な機能実装（認証、Todo CRUDなど）は後続のChangeで対応する。

主要な技術スタック:
- **フロントエンド**: Next.js 15 (App Router)
- **バックエンド/DB**: Supabase（ローカル開発はSupabase CLI）
- **スタイリング**: Tailwind CSS

## Goals / Non-Goals

**Goals:**
- Next.js App Routerプロジェクトを初期化する
- Supabase CLIでローカル開発環境を構築する
- Supabaseクライアントをサーバー/クライアント両方で利用可能にする
- 開発を開始できる状態にする

**Non-Goals:**
- ユーザー認証機能の実装（後続Changeで対応）
- Todo CRUD機能の実装（後続Changeで対応）
- データベースマイグレーションの作成（後続Changeで対応）
- テストコードの実装
- CI/CDパイプラインの構築

## Decisions

### 1. ディレクトリ構成

`src/`ディレクトリを使用したApp Router構成を採用する。

```
src/
├── app/                    # App Routerのルート
│   ├── layout.tsx
│   └── page.tsx
├── components/            # UIコンポーネント（空）
├── lib/                   # ユーティリティ
│   └── supabase/
│       ├── client.ts      # ブラウザ用クライアント
│       ├── server.ts      # Server Components用クライアント
│       └── middleware.ts  # Middleware用クライアント
└── types/                 # 型定義（空）
```

**理由**: `src/`ディレクトリは設定ファイルとアプリケーションコードを明確に分離でき、大規模化した際の保守性が高い。

### 2. Supabaseローカル開発環境

Supabase CLIを使用してローカルにSupabaseを起動する。

```bash
supabase init      # 初期化
supabase start     # ローカルSupabase起動
```

**理由**:
- 本番環境に依存せずに開発可能
- チームメンバー間で同一の開発環境を共有可能
- マイグレーションのテストが容易

### 3. Supabaseクライアントの構成

`@supabase/ssr`パッケージを使用し、Server Components/Client Components/Middlewareそれぞれに適したクライアントを提供する。

**代替案**: `@supabase/supabase-js`のみを使用
- 却下理由: SSR環境でのCookie管理が煩雑になる

### 4. 環境変数

ローカル開発用の環境変数を`.env.local`に設定する。

```
NEXT_PUBLIC_SUPABASE_URL=http://127.0.0.1:54321
NEXT_PUBLIC_SUPABASE_ANON_KEY=<supabase startで出力されるanon key>
```

`.env.example`をテンプレートとして用意する。

## Risks / Trade-offs

**[Supabase CLIのインストールが必要]** → miseでのバージョン管理を設定し、READMEにセットアップ手順を記載

**[Dockerが必要]** → Supabase CLIはDockerを使用するため、事前にDockerのインストールが必要。READMEに前提条件として記載
