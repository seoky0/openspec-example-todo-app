## 1. ファイル移行

- [x] 1.1 `src/middleware.ts`を`src/proxy.ts`にリネーム
- [x] 1.2 エクスポート関数名を`middleware`から`proxy`に変更

## 2. 動作確認

- [x] 2.1 `pnpm dev`を実行し、非推奨警告が解消されていることを確認
- [x] 2.2 `pnpm build`が成功することを確認
