# 情報数理入門 — 講義スライド規約集

第2回 (`lec02/index.html`) から抽出した規約。新しい回もこの規約に厳密に従う。違反するとサイドバー / 目次 / 既存スタイルの一貫性が崩れる。

---

## ファイル構成

各回は `lecNN/` 配下に独立した 3 ファイル:

```
lecNN/
  index.html                       # 対話型 web スライド (この規約の対象)
  情報数理入門_第N回.pptx          # 配布/印刷用 PowerPoint
  情報数理入門_第N回.xlsx          # 演習ワークブック
```

`index.html` は HTML / CSS / JS を 1 枚に埋め込む構成。共通モジュールは作らず、各回が独立。

---

## 外部依存 (CDN)

```html
<link href="https://fonts.googleapis.com/css2?family=Zen+Kaku+Gothic+New:wght@400;500;700;900&family=DM+Serif+Display&family=JetBrains+Mono:wght@400;500;700&display=swap" rel="stylesheet">
<script src="https://cdn.jsdelivr.net/npm/chart.js@4.4.1/dist/chart.umd.min.js"></script>
```

**バージョンは固定**。Chart.js を上げる時は全回まとめて。

---

## :root CSS 変数 (必須・改変禁止)

新しい色や半径を導入する前に既存変数で表現できないか確認。

```css
:root {
  --bg: #FAFBFC;
  --surface: #FFFFFF;
  --surface-2: #F6F7F9;
  --surface-3: #EFF1F4;
  --ink: #1F2937;
  --ink-2: #4B5563;
  --ink-3: #9CA3AF;
  --line: #E5E7EB;
  --line-strong: #D1D5DB;
  --blue: #2563EB;
  --blue-dark: #1D4ED8;
  --blue-deep: #1E3A8A;
  --blue-bg: #EFF6FF;
  --blue-bg-2: #DBEAFE;
  --sky: #93C5FD;
  --amber: #F59E0B;
  --rose: #E11D48;
  --emerald: #059669;
  --radius-sm: 6px;
  --radius-md: 10px;
  --radius-lg: 16px;
  --shadow-sm: 0 1px 2px rgba(15, 23, 42, 0.04), 0 1px 3px rgba(15, 23, 42, 0.06);
  --shadow: 0 1px 2px rgba(15, 23, 42, 0.04), 0 4px 12px rgba(15, 23, 42, 0.06);
  --shadow-lg: 0 4px 6px rgba(15, 23, 42, 0.05), 0 20px 40px rgba(15, 23, 42, 0.08);
  --font-jp: "Zen Kaku Gothic New", sans-serif;
  --font-serif: "DM Serif Display", "Zen Kaku Gothic New", serif;
  --font-mono: "JetBrains Mono", ui-monospace, monospace;
}
```

---

## レイアウト

```html
<div class="shell">
  <aside class="sidebar"> ... ナビ ... </aside>
  <main class="main">
    <div class="slide-deck">
      <section class="slide active" id="part1"> ... </section>
      <section class="slide" id="part2"> ... </section>
      ...
    </div>
  </main>
</div>
```

- `.shell` は 280px サイドバー + 1fr メインの 2 カラム grid
- `.slide` は `position: absolute; inset: 0` で重ね、`.active` だけ `opacity: 1; visibility: visible`
- 切り替えはサイドバーの `.nav-link` クリックで `data-target` に対応する `.slide` を `active` にする JS

---

## ID 命名規約 (PART 別 IIFE と整合させる)

PART N に属する DOM 要素はすべて `cN-*` プレフィックス:

| 要素 | パターン | 例 (PART 1) |
|---|---|---|
| スライダー | `cN-{key}` | `c1-p`, `c1-r`, `c1-y` |
| 表示値 | `cN-{key}-val` | `c1-p-val` |
| 統計値 | `cN-stat-{key}` | `c1-stat-final` |
| Canvas | `chartN` | `chart1` |

**別 PART の ID と衝突させない**こと。新規入力やラベルを追加する時もこの規約に揃える。

---

## スクリプト構成 — PART 単位の IIFE

ページ末尾の単一 `<script>` 内に PART ごと独立した IIFE を並べる:

```js
(() => {
  const p = document.getElementById('cN-p');
  const pVal = document.getElementById('cN-p-val');
  const ctx = document.getElementById('chartN').getContext('2d');

  const chart = new Chart(ctx, { /* ... */ });

  function update() {
    pVal.textContent = p.value;
    chart.data.datasets[0].data = /* ... */;
    chart.update();
  }

  p.addEventListener('input', update);
  update();  // 初期描画
})();
```

グローバルに置いてよいのは:
- `fmtYen`, `fmtMan` (金額フォーマッタ)
- Chart.js のデフォルト設定 (`Chart.defaults.font.family` など)

---

## 共通 PART 構造 (5 ステップ)

各 PART は同じ流れを踏むこと:

1. **身近なお題** (例: バイト代、料金プラン、ガチャ)
2. **スライダー** (`<input type="range">`、`.controls .control-row` 内)
3. **数式表示** (`<div class="formula">` 内、和文 + 数式)
4. **Chart.js グラフ** (`<canvas>` を `<div class="chart-wrap">` で包む、高さは親 div の CSS で固定)
5. **解釈テキスト** (`<div class="interpret">` 等で、現在のスライダー値の意味を言語化)

---

## Chart.js 規約

```js
new Chart(ctx, {
  type: 'line',                  // 折れ線
  // type: 'scatter',            // 座標プロット（lec03 の PART 1, 2, 4, 5）
  data: { /* ... */ },
  options: {
    responsive: true,
    maintainAspectRatio: false,  // 高さは親 div の CSS で（折れ線・棒・通常の散布図）
    // maintainAspectRatio: true, aspectRatio: 1,  // 円・領域は正方形（後述）
    plugins: {
      tooltip: {
        backgroundColor: '#1F2937',
        titleFont: { family: 'Zen Kaku Gothic New' },
        bodyFont: { family: 'Zen Kaku Gothic New' },
      }
    },
    scales: {
      y: {
        ticks: {
          callback: v => '¥' + fmtYen.format(v)  // 金額の場合
        }
      }
    }
  }
});
```

PART 3 / 4 のように補助線・交点マーカーを描きたい時は **カスタムプラグイン** を定義 (`currentMarker`, `intersectionPlugin`, `labelPlugin`, `regionPlugin` 等を参考)。

### 円・領域は aspectRatio: 1 で正方形に固定

円や領域判定など、**縦横比が崩れると意味が変わる**グラフは canvas を正方形に固定する:

```js
options: {
  responsive: true,
  maintainAspectRatio: true,
  aspectRatio: 1,
  scales: {
    x: { min: -10, max: 10 },
    y: { min: -10, max: 10 },  // x と y の数値範囲も等しく
  }
}
```

加えて HTML 側の `.chart-wrap` を `max-width: 520px; margin: 0 auto;` 等で幅を絞ると、画面が広いときも楕円化しない。

### 円を描くときは「閉じた点列」で 1 dataset

scatter の dataset を 2 つ（上半・下半）に分けて `fill: '+1'` でつなぐと、塗りつぶしが暴れる（lec03 で発覚）。**円周を 1 周分の閉じた点列**として 1 dataset に入れて描く:

```js
const ring = [];
for (let i = 0; i <= N; i++) {
  const t = (i / N) * 2 * Math.PI;
  ring.push({ x: A + R * Math.cos(t), y: B + R * Math.sin(t) });
}
// dataset: { data: ring, showLine: true, fill: true, ... }
```

### 領域モード（チェックボックスでオン/オフ）

スライダーで動かす自分の点に「許容範囲」を重ねるオプションが、座標 → 領域 の橋渡しに有効（lec03 PART 1-2）。実装は `beforeDatasetsDraw` のカスタムプラグインで半透明矩形/円を描画:

```js
const regionPlugin = {
  id: 'cNRegion',
  beforeDatasetsDraw(chart) {
    if (!regionToggle.checked) return;
    // 自分の点 (X, Y) を中心に ±DX, ±DY の長方形を半透明で塗る
    const left = chart.scales.x.getPixelForValue(X - DX);
    const right = chart.scales.x.getPixelForValue(X + DX);
    const top = chart.scales.y.getPixelForValue(Y + DY);
    const bottom = chart.scales.y.getPixelForValue(Y - DY);
    chart.ctx.fillStyle = 'rgba(225, 29, 72, 0.10)';
    chart.ctx.fillRect(left, top, right - left, bottom - top);
  }
};
```

---

## HTML 内の Excel-box は Google スプレッドシート前提で書く

各 PART の `<div class="excel-box">` は「**スプレッドシートで再現してみる**」と表示し、Google スプレッドシートの UI 用語で書く（Excel ではない）:

| ✗ Excel 寄り | ✓ Sheets 寄り |
|---|---|
| 散布図（直線あり） | 散布図 → ダブルクリック → [カスタマイズ] → [系列] → 「データポイントを線で結ぶ」にチェック |
| グラフ右クリック → データの選択 → 系列追加 | グラフをダブルクリック → [設定] → 「系列を追加」 |
| グラフの軸の最小最大を固定 | ダブルクリック → [カスタマイズ] → [横軸/縦軸] → 最小値・最大値 |
| セル右下の■をドラッグ | セル右下の **フィルハンドル**（青い四角）をドラッグ |
| フィルター | メニュー [データ] → [フィルタを作成] |

**散布図で「座標」を再現するときは、選択範囲を数値列だけ**にする（名前列を含めると X 軸が文字列カテゴリになって座標として機能しない）。

## 共通汎用クラス (PART 間で共有)

これらを書き換えると全 PART に波及するので注意:

- `.block` — セクションのカード枠
- `.controls` — スライダー群のコンテナ
- `.control-row` — スライダー 1 本 + ラベル + 表示値
- `.stat` — 統計値カード
- `.stat-row` — 統計値の横並び

---

## 過去の回の構成 — 参考

### lec02 (24 スライド)

```
PART 1   複利            お題: 100万円を r% で n 年運用
ASIDE    浮動小数点誤差   小話
PART 2   1次方程式        お題: バイト代の損益分岐
PART 3   1次不等式        お題: 料金プラン比較
PART 4   連立方程式       お題: 2 商品の最適購入数
PART 5   指数爆発         お題: 30 日チャレンジ (倍々)
SUMMARY  まとめ
NEXT     次回予告
```

### lec03 (23 スライド) — 推奨パターン

```
イントロ 3 枚    表紙 / 全体像 / ティーザー
PART 1-1         身近な現象（地図のお店）で「座標」を導入
PART 1-2         同じ発想で金融題材（リスクリターン散布図）
PART 2           概念の主軸（距離 = 三平方の定理）
PART 3           発展（円 = 等距離の点）
PART 4           発展（領域 = 不等号で内・外）
PART 5 GATE      AI/データサイエンスへの締め PART の扉
PART 5-A         シンプル例（映画レコメンド散布図）
PART 5-B         リッチ例 1（Spotify 風カードグリッド + リアルタイム並び替え）
PART 5-C         リッチ例 2（SVG パラメトリック顔の認証シミュレーション）
SUMMARY          3 つの統一原理で締め
                 ＊ NEXT は廃止
```

### 新しい回を作るときの骨格

- **「身近な現象 → 数式 → 金融への展開 → AI/データサイエンスへの締め」** が骨格
- **メインの PART を 1-1 / 1-2 に分割**（身近 → 金融）するパターンが効く
- **最後の PART は AI で締める** — 距離 / 確率 / 関数など概念の汎用性を、リッチなインタラクティブ（カードグリッド、SVG パラメトリック描画）2-3 枚で示す
- 各 PART は「**身近なお題 → 抽象化 → グラフで体感 → 解釈**」のループ
- **NEXT スライドは作らない**。SUMMARY で締める

## リッチなインタラクティブのパターン (lec03 で確立)

最後の PART で「AI/DS への接続」を見せるとき、座標散布図だけでは弱いので、以下のパターンを使い分け。

### カードグリッド + リアルタイム並び替え (Spotify 風)

- N 個のアイテム（曲、映画、商品）を CSS Grid で並べる
- 各アイテムに複数の特徴量を持たせ、ユーザーの 2-3 軸スライダーから距離を計算
- 距離が近い順に CSS の `order` で並び替え
- 上位 3 件にバッジ・縁取り・グロー
- ダーク背景 + ゴールド系アクセントで「メディアアプリらしさ」を出す

### SVG パラメトリック描画 (顔認証風)

- 4-5 個のスライダーで特徴量を直接動かす
- スライダー値に応じて SVG（楕円、円、パス）を再描画
- 登録済み N 人と多次元距離を計算 → 上位 3 件をハイライト
- しきい値で「認証 OK / NG」を判定する UI を添える

### 共通設計

- 通常の Chart.js グラフ（散布図）と並べて使うことで、「同じ式でも見せ方が変わると印象が変わる」を体感させる
- 「**実物の◯◯（Spotify, 顔認証）は数十〜数百次元を使うが、距離の式は今日と同じ**」という締めの一文を必ず入れる
