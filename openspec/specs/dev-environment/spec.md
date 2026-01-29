### Requirement: Next.jsプロジェクト構成

システムはNext.js 16のApp Router構成で初期化されなければならない（MUST）。`src/`ディレクトリを使用し、設定ファイルとアプリケーションコードを分離しなければならない（MUST）。

#### Scenario: プロジェクト構造が正しく作成されている
- **WHEN** プロジェクトを初期化した後
- **THEN** `src/app/`ディレクトリが存在する
- **THEN** `src/app/layout.tsx`が存在する
- **THEN** `src/app/page.tsx`が存在する
- **THEN** `src/components/`ディレクトリが存在する
- **THEN** `src/lib/`ディレクトリが存在する
- **THEN** `src/types/`ディレクトリが存在する

#### Scenario: Next.js開発サーバーが起動できる
- **WHEN** `npm run dev`を実行する
- **THEN** 開発サーバーが起動する
- **THEN** `http://localhost:3000`でアプリケーションにアクセスできる

### Requirement: Supabaseローカル開発環境

システムはSupabase CLIによるローカル開発環境をサポートしなければならない（MUST）。開発者はローカルでSupabaseを起動して開発できなければならない（MUST）。

#### Scenario: Supabase CLIの初期化が完了している
- **WHEN** プロジェクトをクローンした後
- **THEN** `supabase/`ディレクトリが存在する
- **THEN** `supabase/config.toml`が存在する

#### Scenario: ローカルSupabaseを起動できる
- **WHEN** `supabase start`を実行する
- **THEN** ローカルのSupabaseサービスが起動する
- **THEN** PostgreSQL、Auth、Storage等のサービスが利用可能になる
- **THEN** 接続情報（URL、anon key等）が出力される

### Requirement: Supabaseクライアント設定

システムはServer Components、Client Components、Middlewareそれぞれで利用可能なSupabaseクライアントを提供しなければならない（MUST）。`@supabase/ssr`パッケージを使用しなければならない（MUST）。

#### Scenario: Server Components用クライアントが利用できる
- **WHEN** Server Componentからデータベースにアクセスする
- **THEN** `src/lib/supabase/server.ts`のクライアントを使用できる
- **THEN** Cookie経由で認証情報を取得できる

#### Scenario: Client Components用クライアントが利用できる
- **WHEN** Client Componentからデータベースにアクセスする
- **THEN** `src/lib/supabase/client.ts`のクライアントを使用できる
- **THEN** ブラウザのCookie経由で認証情報を取得できる

#### Scenario: Middleware用クライアントが利用できる
- **WHEN** Middlewareで認証チェックを行う
- **THEN** `src/lib/supabase/middleware.ts`のクライアントを使用できる
- **THEN** リクエスト/レスポンスのCookieを操作できる

### Requirement: 環境変数設定

システムは環境変数のテンプレートファイルを提供しなければならない（MUST）。開発者はテンプレートを参照して環境変数を設定できなければならない（MUST）。

#### Scenario: 環境変数テンプレートが存在する
- **WHEN** プロジェクトをクローンした後
- **THEN** `.env.example`ファイルが存在する
- **THEN** `NEXT_PUBLIC_SUPABASE_URL`の説明が記載されている
- **THEN** `NEXT_PUBLIC_SUPABASE_ANON_KEY`の説明が記載されている

#### Scenario: ローカル開発用の環境変数を設定できる
- **WHEN** `.env.example`をコピーして`.env.local`を作成する
- **WHEN** `supabase start`の出力から接続情報を取得する
- **THEN** ローカルSupabaseに接続するための環境変数を設定できる

### Requirement: 依存パッケージ

システムは必要な依存パッケージがインストールされた状態で提供されなければならない（MUST）。

#### Scenario: 必要なパッケージがインストールされている
- **WHEN** `npm install`を実行した後
- **THEN** `next`パッケージがインストールされている
- **THEN** `@supabase/supabase-js`パッケージがインストールされている
- **THEN** `@supabase/ssr`パッケージがインストールされている
- **THEN** `tailwindcss`パッケージがインストールされている

### Requirement: Tailwind CSS設定

システムはTailwind CSSが設定された状態で提供されなければならない（MUST）。

#### Scenario: Tailwind CSSが利用できる
- **WHEN** コンポーネントでTailwind CSSのクラスを使用する
- **THEN** スタイルが正しく適用される

#### Scenario: Tailwind CSS設定ファイルが存在する
- **WHEN** プロジェクトを確認する
- **THEN** `tailwind.config.ts`が存在する
- **THEN** `src/`ディレクトリがcontent設定に含まれている
