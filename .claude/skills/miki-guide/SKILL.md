---
name: miki-guide
description: 情報数理入門の web スライド（lec05 テンプレ）に「みきコン ガイドツアー」を標準搭載する。みきコン(進行役 NPC)が台本に沿って資料を案内し、解説中の object をスポットライト+リングで照らす。操作部品ステップは「動かしてみて」待機+操作部品と出力をまとめて照明。ページ移動は2クリック(予告→移動)。Space/クリックで進む・Backspace/◀で戻る・P キーで強調文字を波打ち。みきコンはアバターのドラッグで移動、タップで折りたたみ/展開。canonical 実装は lec06。ユーザーが「みきコン」「ガイドツアー」「解説アドバイザー」「miki-guide」と言ったとき、または new-lecture の Phase 1 から呼ばれたときに起動する。
---

# miki-guide — みきコン ガイドツアーを標準搭載する (sub-skill)

「情報数理入門」の各回 web スライドに、**進行役 NPC「みきコン」が資料全体を案内するガイドツアー**を載せる。
**canonical 実装 = `lec06/index.html`**。同梱の `engine/miki-guide.block.html` が lec06 から抽出した drop-in ブロック(style + markup + script)で、**TOUR 配列だけを各回の内容に差し替えれば**そのまま動く（デッキ非依存）。

> このファイルを読まずに自前で実装し直さない。**必ず engine ブロックを貼り、TOUR を著述する**方式で再現する。

---

## 1. 何が載るか（機能仕様 = lec06 の作りを完全再現）

| 機能 | 仕様 |
|---|---|
| **みきコン ウィンドウ** | フローティング。アバター(モニター顔・口は「ε」・アンテナ)+ タイトル「みきコン」+ 章タグ + 吹き出し本文(タイプ表示) |
| **スポットライト** | 解説中の object の周囲を紺(`rgba(18,46,85,.52)`)で薄暗く + 対象に光るリング(`::after`、金/青パルス)。`pointer-events:none` で操作を妨げない |
| **ガイド台本(TOUR)** | 要所中心 2〜3 object/枚・全体で ~50 ステップ。各ステップ `{ s, sel, tag, say, union?, wait?, n? }` |
| **進行** | `▶ つづき`/`次へ`(ボタン)・**Space**・**画面クリック/タップ** で前進。**自動再生トグル**(⏯)あり |
| **もどる** | `◀ もどる`(ボタン)・**Backspace** で前の発話へ。先頭で無効化 |
| **2クリックでページ移動(フールプルーフ)** | ページが変わるステップでは 1クリック目=金色「もう一度で次ページ ≫」で予告(armed)、2クリック目で実遷移。自動再生中は予告を飛ばす |
| **インタラクション・ステップ** | 操作部品を狙うステップは `wait:true`(「動かしてみて」で自動再生停止)+ `union:true`(**操作部品と出力をまとめて**スポット) |
| **P コマンド(文字ポップ波)** | `P` キーで、現在スライドの強調文字(h2 の `<em>`・章扉の `.gate-accent`、無ければ見出し全文字)を 45ms ずつ左→右に波打ち(強調=強振幅 `popping-strong`、その他=`popping`) |
| **表示/非表示** | アバターを**タップ**で 展開(解説モード)/折りたたみ(その場にアイコンだけ残す)。lec10 方式 |
| **移動** | タイトルバー**または**アバターを**ドラッグ**でウィンドウ移動。折りたたみアイコンも掴んで移動可。`left/top` 直書き(トランジションなし=1:1 追従でカクつかない) |
| **状態保持** | 折りたたんでも TOUR 位置を保持。再展開で続きから |
| **配慮** | `prefers-reduced-motion` 対応・モバイル(全幅下部・タップ進行)・四隅リサイズ(PC) |

---

## 2. デッキ契約（host が満たすべき前提 ― lec05 テンプレは標準で満たす）

エンジンは **lecNN-slide-change という回ごとのイベント名に依存しない**。代わりに次を前提にする:

1. スライドは `.slide-deck`（or `#slide-deck`）配下の `<section class="slide" id="slide-N" data-title="…">`。
2. 表示中スライドだけが `.slide.active`（デッキの `go()` が付け替える）。エンジンは **`.active` の class 変化を MutationObserver で監視**して同期する。
3. ページ遷移は `location.hash = '#slide-N'`（デッキが hashchange を拾って `go()` する）。
4. 各 object に ID か修飾クラスがある（`#cN-*`、`.callout.amber/.rose/.purple/.emerald`、`.gate-accent`、`.story-hero`、`.tocbar` 等）。

lec05 テンプレからコピーした回はこれを満たす。満たさないデッキに載せるときは ①②③ を先に用意する。

---

## 3. 手順（new-lecture の Phase 1 内 / 単独どちらでも）

1. **engine を貼る** — `engine/miki-guide.block.html` の中身を、対象 `lecNN/index.html` の **`</body>` 直前**（デッキの最後の `</script>` の後ろ）に丸ごと貼る。
2. **TOUR を著述** — ブロック内 `const TOUR = [ ... ];` を、その回の内容に**全面差し替え**する。書き方は §4。**TOUR 以外（CSS / markup / コントローラ / ドラッグ / P）は触らない**。
3. **トークン確認** — `--action` `--anchor` `--gold` 等が `:root` にあるか確認（lec05 テンプレにあり）。無ければ engine 内のフォールバック色（`var(--action, #4A78C8)` 等）が効くが、可能なら回の `:root` に合わせる。
4. **実機検証** — §5 の headless Chrome チェックを通す。
5. **voice 自己審査** — みきコンの台詞も講義 voice ルール(§6)に従う。`lec-voice-audit` の観点で読み直す。

---

## 4. TOUR の書き方（ステップ schema）

```js
{ s: 5, sel: '#c1-x, #c1-machine, #c1-stat-y', union: true, wait: true, tag: '入力 x',
  say: ['…説明…', '…操作するとこう変わる…', '…では動かしてみてください…'] }
```

| キー | 意味 |
|---|---|
| `s` | スライド番号（`#slide-{s}`）。**必ず昇順**で並べる（連続する同 s はそのページ内ステップ） |
| `sel` | 照らす対象の CSS セレクタ（active スライド内で評価）。ID 推奨。複数同種は §「曖昧回避」 |
| `union` | `true` なら `sel` にマッチする**全要素の合体バウンディングボックス**を照らす（操作部品＋出力をまとめる） |
| `wait` | `true` なら**操作待ち**。最終行で自動再生を停止し、ヒントを「動かしてみて、できたら ▶ で次へ」に |
| `n` | 同一セレクタが複数ヒットするとき何番目か（1始まり、union でないとき）。例: `.principle-card` の 1 枚目 |
| `tag` | 章タグ（吹き出し左上の小さいラベル）。短く（「入力 x」「定義」「外れ値に強い」） |
| `say` | 文字列 or 配列。配列は ▶ つづき で1行ずつ送る |

### 粒度
- **要所中心 2〜3 object/枚**。全 object を網羅しない（冗長）。各スライドの「主役 object（グラフ/主要数値/キーになる callout）」に絞る。
- 章扉(gate)は `sel: '.gate-title'` で 1〜2 ステップ（その PART の予告）。
- 全体で **~50 ステップ** を目安。

### インタラクション・ステップ（操作部品があるスライド）
操作部品（スライダー/ボタン/ドラッグ図）を持つスライドの主役ステップは **`wait:true` + `union:true`**:
- `sel` に **操作部品 + 出力**を列挙: 例 `'#c1-x, #c1-machine, #c1-stat-y'`（スライダー＋図＋出力数値）。
- `say` 最終行を「**〜を動かしてみてください。…が変わります**」の操作指示にする（命令形ではなく「〜してみてください」）。
- 検算した具体数値を入れる（例: ⌈100/30⌉=4台、√4=2、σ(0)=0.5、⌊−1.2⌋=−2）。**内容正確性は `lec-content-check` 同等の厳密さで**。

### セレクタの曖昧回避
- 同一スライドに同種要素が複数あるとき:
  - callout は修飾クラスで一意化: `.callout.amber` / `.callout.rose` / `.callout.purple` / `.callout.emerald` / `.callout`（無印）。
  - それでも複数なら `n`（1始まり）で指定（例 `.principle-card` を `n:1` / `n:3`）。
- グラフ canvas・スライダー・stat は ID（`#cN-*`）で一意。
- ドラッグ図（`#cAbs-line` 等）は `union` の構成要素に含めると操作部品として照らせる。

### P コマンドの強調対象
P は **見出し系のみ**を分割対象にする（本文はリフロー回避で分割しない）。強調＝`h2 em` / `.gate-accent`（必要なら見出し内 `strong`）。TOUR とは独立。**TOUR を変えても P は自動で効く**（見出しを走査するため、追加実装不要）。

---

## 5. 実機検証プロトコル（headless Chrome + CDP）

静的な `node --check` だけでは視覚バグ・遷移バグが出ない。**必ず実機**で確認する（reference_miki_npc_hosoku_lec10 / superelite v3 と同じ教訓）。

最低限のチェック（Chrome `--headless=new` + remote-debugging で driving）:
1. **初期** = `#mikibar.collapsed`・`body` に `miki-on` なし（デッキ通常）。
2. **アバター タップ** → 展開・`body.miki-on`・`#tour-spot.on`、TOUR 先頭ステップ表示。
3. **全ステップ踏破**（`mbNext` 連打）→ 各ステップで `spotOn && width>0`、`elementFromPoint(spot中心)` が意図 object に一致、最終 `47/47` で `slide-N(最終)` に到達。
4. **union ステップ** = スポット矩形が操作部品と出力の両方を内包。
5. **wait ステップ** = 自動再生 ON で最終行に来たら `auto` が止まる。
6. **2クリック** = ページ末で 1click → `mbNext.classList.contains('armed')` かつ slide 不変、2click → slide 変化。
7. **Space/クリック前進・Backspace/◀ もどる**（先頭で `mbPrev.disabled`）。
8. **P キー** → 強調文字に `popping-strong`、強調なしスライドで見出し全体に `popping`、`animationDelay` が 0/45/90… と staggered。
9. **移動** = アバター/タイトルバー ドラッグで `left/top` 変化、折りたたみアイコンもドラッグ可。
10. **コンソール JS 例外ゼロ**（`navigator.vibrate` の警告は headless 由来で無害）。

> テストで `mbNext` を高速連打するとタイプ表示が「全文表示」に消費され step が進みにくい。**クリック間隔を空ける**か「finish→advance」の2クリックにする（挙動バグではない）。

---

## 6. voice（みきコンの台詞）

大学講義の**自然な口語**。`lec-voice-audit` と同じ規範に従う:
- 「皆さん」+ 中立動詞、宣言文。**命令形/体言止め/スローガン/抽象動詞メタファー**を避ける。
- 操作指示は「**〜してみてください**」（「〜せよ」ではない）。
- AI 臭・誇大表現（「武器」「叩き台」等）禁止。
- 参照メモリ: `feedback_natural_spoken_japanese_for_lectures` / `feedback_no_ai_tone_in_lectures` / `feedback_lecture_emphasis_color_contrast`。

---

## 7. ハマりどころ（lec06 制作で確定）

- **カクつき**: 起点の中央寄せ `transform: translateX(-50%)` と「展開ポップ」アニメ(transform scale)が干渉するとドラッグがガタつく。**移動は `left/top` 直書き・トランジションなし**（lec10 方式）。展開アニメは入れない。
- **アイコンを掴めない**: ドラッグ判定を**タイトルバー限定にしない**。`onAva`（アバター）も drag 対象にし、`moved` フラグで「動かさずタップ＝折りたたみ/展開」を区別（lec10 `mikiMin` 相当）。
- **union スポットが巨大**: 操作部品＋出力の合体は意図どおり。ただし対象が縦に長いスライドでは中央寄せスクロールで収める。
- **スポットが追従しない**: スライド内スクロール/リサイズで `getBoundingClientRect` 再計測（capture phase の scroll + resize で reposition）。
- **TOUR は昇順必須**: ページ移動判定（次ステップの `s` 比較）と手動同期がこれに依存。
- **本文を P 分割しない**: 段落 `<p>`/callout を文字分割するとリフロー・行折り返しが乱れる。見出しのみ分割。
- **デッキの `lecNN-slide-change` リスナ（チャート再描画）には触らない**: それはデッキ側。みきコンは MutationObserver で独立に同期する。

---

## 8. canonical / 関連

- **canonical 実装**: `lec06/index.html`（全機能の生きた参照）。
- **drop-in**: `engine/miki-guide.block.html`（lec06 から抽出。TOUR 差し替えで再現）。
- **由来**: 高校 mikikof-lab `lectures/articles/10-web-pages`（みきコン NPC + 文字ポップ波の元）/ 東進 superelite `_slides/v3`（P コマンド charPop の規範）。
- **呼び出し元**: `new-lecture`（Phase 1 の標準工程）。voice は `lec-voice-audit`、内容は `lec-content-check` と同基準。
