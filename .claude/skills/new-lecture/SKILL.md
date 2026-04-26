---
name: new-lecture
description: 「情報数理入門」の新しい回 (lecNN) を 3 ファイル(pptx + 対話型 web スライド + Excel 演習) セットで scaffold する。テーマと参考資料(画像/URL/既存スライド)を受け取り、第2回(lec02)を参照モデルとして lecNN/ ディレクトリに生成し、ルートの目次ページも更新する。ユーザーが「第N回を作って」「new-lecture」「新しい回を追加」と言ったときに起動する。
---

# new-lecture — 情報数理入門の新しい回を scaffold

このプロジェクトは「情報数理入門」の各回を `lecNN/` 配下に **pptx + html + xlsx** の3点セットで管理している。新しい回を追加する時はこの skill を使って、第2回 (`lec02/`) と同じ品質・スタイルで scaffold する。

## 動作原則

1. **テンプレートは lec02** — 構造・配色・命名規約・PART 構成パターンは lec02 を必ず先に読み込んでから真似る。新しいスタイルを発明しない
2. **ユーザーに確認してから生成** — 入力を集めて構成案を提示 → 承認後に生成
3. **3 ファイルは互いに対応** — pptx の章立て = html の PART = xlsx の Part別シート、になるよう揃える

## 入力収集 (対話で確認)

最初に以下を聞く。提供されない情報は提案して埋める:

- **回数 N** (整数、2桁ゼロ詰めで `lec0N` を作る)
- **テーマ** (例: 「確率と統計の基礎」「微分入門」)
- **参考資料**:
  - 既存 PPTX/PDF/画像があれば Read で取り込んで構造を抽出
  - 参考 URL があれば WebFetch で内容を取り込む
- **PART 構成** (章立て): 提案するか、ユーザー指定。lec02 と同じく 5 章前後 + 補足 (ASIDE) が標準
- **各 PART の身近なお題** (例: バイト代、料金プラン、確率なら宝くじやガチャ): ユーザーから引き出す or 提案

## 生成ステップ

### 0. 事前確認

- `lec02/index.html` を読んで規約を確認 (`.claude/skills/new-lecture/TEMPLATE_GUIDE.md` も参照)
- `lec02/情報数理入門_第2回.xlsx` のシート構成を `.venv/bin/python` で確認

### 1. ディレクトリ作成

```bash
mkdir -p lec0N
```

### 2. PPTX 生成 (`lec0N/情報数理入門_第N回.pptx`)

`.venv/bin/python` + `python-pptx` で生成。lec02 の PDF 構成に倣う:

- **24 スライド前後**
- 表紙 → 目次 → 各 PART の章扉 + 本文 + 演習 → まとめ → 次回予告
- 配色: メイン青 `#2563EB`、深青 `#1E3A8A`、文字 `#1F2937`
- 和文フォント: Yu Gothic / Hiragino Sans / Zen Kaku Gothic New
- 画像が提供された場合は対応するスライドに埋め込む

スクリプトは Bash heredoc でその場で書いて実行する (テンプレート化はしない — 回ごとに内容が違うため)。

### 3. Web スライド生成 (`lec0N/index.html`)

**`lec02/index.html` をコピーして書き換える**:

```bash
cp lec02/index.html lec0N/index.html
```

その後、`TEMPLATE_GUIDE.md` の規約に従って書き換え:

- `<title>` を「情報数理入門 第N回 — {テーマ}」に
- サイドバーのナビ項目とアンカーを新 PART に合わせる
- 各 PART の中身 (お題、スライダー、Chart.js の設定、解釈テキスト) を差し替え
- ID プレフィックス `cN-*` を維持 (PART 1 → `c1-*`, PART 2 → `c2-*`)
- IIFE は PART 単位で独立させる
- pptx の章立て = web の PART になるよう順序を揃える

### 4. Excel 生成 (`lec0N/情報数理入門_第N回.xlsx`)

`.venv/bin/python` + `openpyxl` で生成。lec02 の構成に倣う:

- **PART 別シート** (各 PART に 1 シート、PART タイトルをシート名に反映)
- **演習① 基本** (ウォームアップ問題)
- **演習② 応用** (条件を整理して式を立てる問題)
- **挑戦① 直感 → 計算 → 検証**
- **挑戦② 複合分析・試行錯誤型**

各シートの先頭行に PART タイトル、罫線・色・列幅は lec02 を真似る。lec02 をコピーしてシート内容を書き換える方が早い場合は `cp lec02/情報数理入門_第2回.xlsx lec0N/...` してから openpyxl で編集。

### 5. ルート目次 `index.html` の更新

- `<div class="placeholder">` の `0N` 部分を `<a class="lec-card" href="lec0N/index.html">...</a>` に置換
- 説明文に PART のキーワードを並べる (lec02 のカードと同じ粒度)
- 次の回 `0(N+1)` の placeholder を新規追加

### 6. 動作確認 → コミット

- `git status` で全変更を確認
- ユーザーに「`open index.html` で目次から第N回が開けるか確認してほしい」と案内
- 確認 OK 後にコミット (1コミットで OK、メッセージは「第N回を追加: {テーマ}」)
- プッシュは聞いてから

## 注意

- **lec02 を壊さない**: 既存ファイルは読むだけ
- **CDN/フォントの URL は変えない**: lec02 と同じ Chart.js 4.4.1 / Google Fonts を使う
- **日本語のファイル名**: `情報数理入門_第N回.pptx` のように半角アンダースコア + 全角「第N回」で統一
- **ID 衝突**: PART N の DOM ID は `cN-*` プレフィックス必須 (詳細は TEMPLATE_GUIDE.md)
- **生成スクリプトは保存しない**: pptx/xlsx 生成用の python スクリプトは Bash で一回実行するだけ。ファイルとしてコミットしない
