## 1. Next.jsプロジェクト初期化

- [ ] 1.1 Next.jsプロジェクトを初期化する

```bash
pnpm create next-app@latest . --typescript --tailwind --biome --app --src-dir --use-pnpm --skip-install
```

※ 既存ファイルがある場合は確認プロンプトが表示される

- [ ] 1.2 依存関係をインストールする

```bash
pnpm install
```

- [ ] 1.3 追加のディレクトリを作成する

```bash
mkdir -p src/components src/lib src/types
```

## 2. Supabaseローカル環境セットアップ

- [ ] 2.1 aquaの設定にsupabase-cliを追加する

`aqua.yaml`に以下を追加:

```yaml
packages:
  - name: supabase/cli@latest
```

```bash
aqua install
```

- [ ] 2.2 Supabaseプロジェクトを初期化する

```bash
supabase init
```

## 3. Supabaseクライアント設定

- [ ] 3.1 Supabaseパッケージをインストールする

```bash
pnpm add @supabase/supabase-js @supabase/ssr
```

- [ ] 3.2 `src/lib/supabase/client.ts`を作成する（ブラウザ用クライアント）

```typescript
import { createBrowserClient } from "@supabase/ssr";

export function createClient() {
  return createBrowserClient(
    process.env.NEXT_PUBLIC_SUPABASE_URL!,
    process.env.NEXT_PUBLIC_SUPABASE_ANON_KEY!
  );
}
```

- [ ] 3.3 `src/lib/supabase/server.ts`を作成する（Server Components用クライアント）

```typescript
import { createServerClient } from "@supabase/ssr";
import { cookies } from "next/headers";

export async function createClient() {
  const cookieStore = await cookies();

  return createServerClient(
    process.env.NEXT_PUBLIC_SUPABASE_URL!,
    process.env.NEXT_PUBLIC_SUPABASE_ANON_KEY!,
    {
      cookies: {
        getAll() {
          return cookieStore.getAll();
        },
        setAll(cookiesToSet) {
          try {
            cookiesToSet.forEach(({ name, value, options }) =>
              cookieStore.set(name, value, options)
            );
          } catch {
            // Server Componentから呼び出された場合は書き込み不可
          }
        },
      },
    }
  );
}
```

- [ ] 3.4 `src/lib/supabase/middleware.ts`を作成する（Middleware用ヘルパー）

```typescript
import { createServerClient } from "@supabase/ssr";
import { NextResponse, type NextRequest } from "next/server";

export async function updateSession(request: NextRequest) {
  let supabaseResponse = NextResponse.next({
    request,
  });

  const supabase = createServerClient(
    process.env.NEXT_PUBLIC_SUPABASE_URL!,
    process.env.NEXT_PUBLIC_SUPABASE_ANON_KEY!,
    {
      cookies: {
        getAll() {
          return request.cookies.getAll();
        },
        setAll(cookiesToSet) {
          cookiesToSet.forEach(({ name, value }) =>
            request.cookies.set(name, value)
          );
          supabaseResponse = NextResponse.next({
            request,
          });
          cookiesToSet.forEach(({ name, value, options }) =>
            supabaseResponse.cookies.set(name, value, options)
          );
        },
      },
    }
  );

  // 認証トークンをリフレッシュ
  await supabase.auth.getUser();

  return supabaseResponse;
}
```

- [ ] 3.5 `src/middleware.ts`を作成する（認証トークンのリフレッシュ用）

```typescript
import { type NextRequest } from "next/server";
import { updateSession } from "@/lib/supabase/middleware";

export async function middleware(request: NextRequest) {
  return await updateSession(request);
}

export const config = {
  matcher: [
    /*
     * 以下で始まるパスを除くすべてのリクエストパスにマッチ:
     * - _next/static (静的ファイル)
     * - _next/image (画像最適化ファイル)
     * - favicon.ico (ファビコン)
     * - 画像ファイル
     */
    "/((?!_next/static|_next/image|favicon.ico|.*\\.(?:svg|png|jpg|jpeg|gif|webp)$).*)",
  ],
};
```

## 4. 環境変数設定

- [ ] 4.1 `.env.example`ファイルを作成する

```bash
cat << 'EOF' > .env.example
# Supabase設定
# ローカル開発時は `supabase start` の出力から取得
NEXT_PUBLIC_SUPABASE_URL=http://127.0.0.1:54321
NEXT_PUBLIC_SUPABASE_ANON_KEY=your-anon-key-here
EOF
```

- [ ] 4.2 `.gitignore`に`.env.local`が含まれていることを確認する

```bash
grep -q ".env.local" .gitignore || echo ".env.local" >> .gitignore
```

## 5. 動作確認

- [ ] 5.1 Next.js開発サーバーの起動確認

```bash
pnpm dev
```

→ `http://localhost:3000` でアクセス可能であることを確認

- [ ] 5.2 ローカルSupabaseの起動確認

```bash
supabase start
```

→ 起動後に出力される `API URL` と `anon key` を `.env.local` にコピー

---

## 参考リンク

- [Next.js Installation](https://nextjs.org/docs/app/getting-started/installation)
- [create-next-app CLI Reference](https://nextjs.org/docs/app/api-reference/cli/create-next-app)
- [Supabase CLI Getting Started](https://supabase.com/docs/guides/local-development/cli/getting-started)
- [Setting up Server-Side Auth for Next.js](https://supabase.com/docs/guides/auth/server-side/nextjs)
- [Creating a Supabase client for SSR](https://supabase.com/docs/guides/auth/server-side/creating-a-client)
