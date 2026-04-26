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
  type: 'line',
  data: { /* ... */ },
  options: {
    responsive: true,
    maintainAspectRatio: false,  // 高さは親 div の CSS で
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

PART 3 / 4 のように補助線・交点マーカーを描きたい時は **カスタムプラグイン** を定義 (`currentMarker`, `intersectionPlugin` を参考)。

---

## 共通汎用クラス (PART 間で共有)

これらを書き換えると全 PART に波及するので注意:

- `.block` — セクションのカード枠
- `.controls` — スライダー群のコンテナ
- `.control-row` — スライダー 1 本 + ラベル + 表示値
- `.stat` — 統計値カード
- `.stat-row` — 統計値の横並び

---

## 第2回 (lec02) の PART 構成 — 参考

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

新しい回も「**身近なお題 → 抽象化 → グラフで体感 → 解釈**」のループを 4〜5 回繰り返す構造に揃えること。
