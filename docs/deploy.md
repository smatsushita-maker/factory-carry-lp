# 公開手順書 (Vercel + お名前.com)

本書の通りに進めれば、`https://www.factory-carry.com/` で公開できます。

---

## 0. 前提

- GitHub アカウント取得済み
- Vercel アカウント取得済み (GitHub 連携推奨)
- ドメイン `factory-carry.com` をお名前.com で取得済み
- Git / Node 環境は不要 (静的サイトのため Vercel 側でビルド無し)

---

## 1. GitHub にリポジトリを作成して push

### 1-1. GitHub 側でリポジトリを作成

1. https://github.com/new を開く
2. Repository name: `factory-carry-lp` (任意)
3. Private / Public どちらでも可
4. README / .gitignore は **追加しない** (ローカル側にあるため)
5. 「Create repository」をクリック

### 1-2. ローカルから push (コピペ実行)

作業ディレクトリで以下を順に実行:

```bash
cd "C:/Users/client20211001/Desktop/製造転職キャリー"

git init
git add .
git commit -m "chore: initial commit for factory-carry.com LP"
git branch -M main
git remote add origin https://github.com/<YOUR_GITHUB_USER>/factory-carry-lp.git
git push -u origin main
```

`<YOUR_GITHUB_USER>` は自分の GitHub ユーザー名に置き換えてください。

---

## 2. Vercel にデプロイ

### 2-1. プロジェクト作成

1. https://vercel.com/new を開く
2. 「Import Git Repository」から先ほどのリポジトリを選択
3. Framework Preset: **Other** のまま
4. Build Command: **空欄**
5. Output Directory: **空欄** (ルート直下を配信)
6. 「Deploy」をクリック

数十秒で初回デプロイが完了し、`xxx.vercel.app` の URL が発行されます。

### 2-2. カスタムドメイン追加

1. Vercel プロジェクト → **Settings → Domains**
2. `www.factory-carry.com` を追加 → 「Add」
3. 続けて `factory-carry.com` (apex) も追加 → 「Add」
4. Vercel 側で「`www` を primary に設定するか」尋ねられた場合は **www を primary** に設定
   - これにより apex (`factory-carry.com`) → `www` へ 308 リダイレクトが自動で有効化されます
5. ドメインを追加すると、Vercel が **必要な DNS レコードの値** を画面に表示します。その値で次の手順 3 に進みます。
   - 一般的には次の値が案内されます:
     - `factory-carry.com` → **A** `76.76.21.21`
     - `www` → **CNAME** `cname.vercel-dns.com`

> Vercel の画面に実際に表示された値が最終的に正しいです。表示値と本書の想定値が違う場合は、**Vercel の表示値を優先** してください。

---

## 3. お名前.com 側の DNS 設定 (人が手動で行う)

1. https://www.onamae.com/navi/login/ にログイン
2. 上部メニュー「ドメイン」→ 対象ドメイン `factory-carry.com` の「DNS」をクリック
3. 「DNSレコード設定を利用する」→ 「設定する」
4. 既存の不要なデフォルトレコード(A / CNAME / MX 等) は必要に応じて削除
5. 以下の 2 レコードを追加:

| ホスト名 | TYPE  | VALUE                  | TTL  |
|----------|-------|------------------------|------|
| (空欄)   | A     | 76.76.21.21            | 3600 |
| www      | CNAME | cname.vercel-dns.com   | 3600 |

- 「ホスト名」の空欄は apex (`factory-carry.com` そのもの) を意味します。お名前.com の UI では「@」ではなく **空欄のまま登録** します。
- CNAME の VALUE 末尾のドット (`.`) は、お名前.com の UI では **入力しない** でください (自動付与されます)。
- 「DNSレコード設定用ネームサーバー変更確認」にチェック → 「確認画面へ進む」 → 「設定する」

### 反映時間
- DNS 反映は通常 数分〜1時間程度 (最大 48時間)
- 反映後、Vercel 側で自動的に Let's Encrypt の SSL 証明書が発行されます

---

## 4. www へのリダイレクト確認

以下の挙動になっていれば成功です:

| 入力 URL                          | 期待される結果                                |
|-----------------------------------|-----------------------------------------------|
| `http://factory-carry.com/`       | `https://www.factory-carry.com/` へリダイレクト |
| `https://factory-carry.com/`      | `https://www.factory-carry.com/` へリダイレクト |
| `http://www.factory-carry.com/`   | `https://www.factory-carry.com/` へリダイレクト |
| `https://www.factory-carry.com/`  | 200 OK (LPが表示)                             |

Vercel のドメイン設定で `www` を primary にすると、apex → www の 308 リダイレクトは自動有効化されます。本リポジトリの `vercel.json` にも保険のリダイレクト設定を入れています。

---

## 5. 公開後の確認チェックリスト

- [ ] `https://www.factory-carry.com/` でトップが表示される
- [ ] `https://factory-carry.com/` が `www` にリダイレクトされる
- [ ] 鍵マーク (SSL) が有効
- [ ] スマホ (iPhone / Android) でレイアウト崩れなし
- [ ] ヘッダーの「LINEで無料相談」ボタンから LINE へ遷移する
- [ ] `terms.html` / `privacy.html` / `legal.html` が表示される
- [ ] 存在しない URL (例: `/abc`) で 404.html が表示される
- [ ] `https://www.factory-carry.com/robots.txt` が表示される
- [ ] `https://www.factory-carry.com/sitemap.xml` が表示される
- [ ] OGP: `https://www.opengraph.xyz/url/https%3A%2F%2Fwww.factory-carry.com%2F` でプレビュー確認
- [ ] Google Search Console にサイトを登録し、`sitemap.xml` を送信

---

## 6. 更新フロー

LP を更新したいときは、ファイルを編集して以下を実行するだけで自動デプロイされます:

```bash
git add .
git commit -m "update: <変更内容>"
git push
```

---

## 7. トラブルシュート

| 症状 | 対処 |
|---|---|
| `DNS_PROBE_FINISHED_NXDOMAIN` | DNS 未反映。数時間待つ。`nslookup www.factory-carry.com` で確認。 |
| Vercel で「Invalid Configuration」 | DNS の値が Vercel の指示と一致しているか確認。 |
| SSL がまだ発行されない | DNS 反映待ち。Vercel の Domains 画面でステータス確認。 |
| apex → www にリダイレクトされない | Vercel の Domains 画面で `www` を primary に設定。 |
