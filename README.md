# 情報数理入門 — 講義スライド集

平成国際大学 情報デザイン学部「情報数理入門」の講義スライドをまとめた monorepo。

各回は `lecNN/` ディレクトリ配下に独立した `index.html` 1枚として収めている。ルートの `index.html` は各回への目次。

## 構成

```
.
├── index.html        # 目次ページ（各回へのリンク）
├── lec02/            # 第2回 — 式で世界を動かす
│   ├── index.html
│   ├── 情報数理入門_第2回.pdf
│   └── 情報数理入門_第2回.xlsx
├── lec03/            # （今後追加）
└── ...
```

## 開き方

ブラウザで `index.html` を開く（macOS なら `open index.html`）。目次から各回へ遷移。

各回を直接開きたい場合は `open lec02/index.html` など。

## 新しい回を追加するには

1. `lecNN/` ディレクトリを作る（例：`lec03/`）
2. その中に `index.html`（必要なら PDF/xlsx も）を置く
3. ルートの `index.html` の `.lec-list` 内にカードを 1つ追加（既存の `lec02` のブロックをコピーして編集）

`lecNN/index.html` のスタイル・スクリプト規約は [CLAUDE.md](./CLAUDE.md) を参照。
