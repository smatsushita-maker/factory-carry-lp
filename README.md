# 製造転職キャリー LP (factory-carry.com)

製造業特化 転職エージェント「製造転職キャリー」のランディングページ。

- 本番URL: https://www.factory-carry.com/
- ホスティング: Vercel (静的配信)
- ドメイン管理: お名前.com

## ディレクトリ構成

```
.
├── index.html          # トップページ (LP本体)
├── legal.html          # 特定商取引法に基づく表記
├── privacy.html        # プライバシーポリシー
├── terms.html          # 利用規約
├── 404.html            # 404 エラーページ
├── robots.txt          # クローラ制御
├── sitemap.xml         # サイトマップ
├── favicon.svg         # ファビコン (SVG)
├── vercel.json         # Vercel 設定 (www 正規化・セキュリティヘッダ)
├── assets/
│   └── images/         # OGP画像等の配置先 (ogp.png をここへ)
├── docs/
│   └── deploy.md       # 公開手順書
├── .gitignore
└── README.md
```

## 公開手順の概要

詳細は [`docs/deploy.md`](docs/deploy.md) を参照してください。

1. GitHub リポジトリに push
2. Vercel で Import → Deploy
3. Vercel にカスタムドメインを追加
4. お名前.com で DNS レコードを設定
5. SSL 発行待ち (数分〜数十分) → 公開完了

## 技術スタック

- HTML (静的)
- Tailwind CSS (CDN)
- Font Awesome (CDN)
- Google Fonts (Noto Sans JP)
