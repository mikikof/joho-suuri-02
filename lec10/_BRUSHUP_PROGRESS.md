# lec10「変化をとらえる、微分」全面ブラッシュアップ — 作業ログ

開始: 2026-06-29 深夜（ユーザー就寝中・ノンストップ自律作業の許可あり）
方針: education/university = Opus[1M] + ultrathink + 鬼教諭。voice ルール厳守・図は文字と重ねない・幾何は正確に。

## ユーザーの指示（要約）
1. 仕上がりが甘い。グラフと線の位置ズレ・表示が分かりにくい・全然仕上がってない。
2. **「小さいものを捨てる」のが微分の本筋を最初に伝える**。いきなり接線は違う。
   幅 Δx どうしの積（Δx²）は他に比べてゴミ → 捨てよう、という**一次元・二次元の面積で捉える微分**を最初に入れる。
3. 全体的にブラッシュアップ。`/audit-review → /brushup → /visual → /audit-review` を連続実行。
4. グラフ/イラストの位置・見た目の整合・日本語の改行位置・日本語の使い方まで徹底。時間・エージェント無制限。

## 現状診断（実機 headless 確認済み）
- **構成**: 25 スライド・5 PART（平均変化率→微分=瞬間→増減→最適化→勾配降下→AI）。Chart.js 5 canvas + 自作 SVG 60 + HOSOKU 補足 + みきコン TOUR 搭載。
- **致命: 16:9 縦あふれ（systemic）**。1920×1080 でも slide21 等はチャート下半分・stat・callout がフォールド外。`h2{margin-top:64px}`+チャート 320px+ゆるい余白が原因。
- **致命: 自作 SVG の幾何バグ**。slide10 の y=x² が「上に凸の丘・上端で見切れ」（形が逆）＋2 本の接線分が曲線に触れず宙に浮く。
- **みきコン「クリックで解説」チップがチャート/数式/タイトルに重なる**（slide21 はチャート中央, slide5 は数式, slide7 は点A）。
- **ラベルと線の衝突**（slide6「平均+10円/日」が曲線＋割線の上に重なる）。データが右 2/3 に偏り左に死に空間。
- canvas 描画図（7,9,13,17,21）は幾何的に正しい。バグは自作 SVG に集中。

## 実行計画
- [x] Phase0 診断: 全 25 スライド並列ビジュアル監査（workflow w121dn4cw）→ 全バグカタログ（scratchpad/FIXLIST.md）。
- [x] Phase1 リフレーム: **新 PART1「微分の正体＝小さいものを捨てる」**（扉＋面積で分解 c0 interactive＋ゴミを捨てて2x・一般化＋WHYDISCARD補足）を先頭挿入。6 PART・28 枚へ renumber 完了（id/href/data-slide/range/target===/TOUR s:/PART番号/nav-total 厳密更新・整合検証 OK）。
- [x] Phase2 systemic fit 手術: `<style id=brushup-fit-2026>` で section-sub行間/block/callout/chart 232px/原理カード圧縮＋みきチップ右下退避＋stat-label大文字化解除。1080p で図＋数値＋まとめが1画面に収まることを slide12/24/27 で確認。
- [x] Phase3 図の幾何修正: slide13 二画面(放物線凸下化＋接線接点＋f′原点)、slide17 山谷接線、slide25 学習率ボール、slide2 振り返り3カード、slide3 tocbar アイコン、扉アイコン4枚、slide18 位置グリフ、slide27 FLOW box2。株価ラベル衝突＋カーブ。capstone 数学誤り修正(費用=x²−8x+20で降下収束)。slide1 一つ。日本語 keep-all で語中改行防止。
- [x] Phase3.5 自己検証スイープ（workflow wemkiqkmw・22/28 clean）→ 残課題（rule-tbl th 大文字化・table overflow・原理アイコン・サイドバー改行）修正。
- [x] Phase4 `/audit-review` rev1（codex review-paper・教科書準拠）→ 批判的キュレート（採用18・却下0）→ 適用。数学厳密化（捨てる×極限の両立で核は死守）/TOUR PART番号バグ/c5範囲/非凸/voice/推奨。ログ= audit/reviews/2026-06-29/0323-joho-lec10-deck-rev1/。
- [x] Phase5 /brushup・/visual: 本デッキは自作SVG superelite 様式で既に視覚主体＋全図修正済。汎用skill（lectures/articles の OSS必須ルール）は本デッキ様式と非整合のため、ブラッシュアップ/ビジュアル強化は本デッキ様式に沿って inline 実施（reframe の新インタラクティブ図・全図幾何・fit・配色）でカバー。
- [x] Phase6 `/audit-review` rev2（収束確認）→ 要再修正（TOUR台本/補足に旧表現の複製が残存）→ 10件修正（過剰一般化・候補性・非凸・大主語・数値微分→自動微分・c5玉Yクランプ）。grep で旧表現 0 件を確認。ログ= rev2/。
- [x] Phase6.5 `/audit-review` rev3 → 実体4件（c5 L(w)バグ・TOUR大主語・原理03非凸・候補性）適用、残りは過剰hedgeとして Claude 判断で温存→**収束**。c5 範囲外を左右とも実機検証（玉＋「範囲外」が端に出て統計値一致）。ログ= rev3/。
- [x] Phase7 完了。整合性 all green（JSエラー0・section28・div550/550・id欠落なし・chips==keys・TOUR 1..28）。**未commit**。バックアップ3点（scratchpad/index.BACKUP/PRE_AUDIT/FINAL.html）。

---
## ✅ 完了（2026-06-29 未明）
全フェーズ完了。lec10 は「微分の正体＝小さいものを捨てる」を先頭に置く 6PART・28枚へ刷新し、16:9フィット・図の幾何・capstone数学・voice・日本語改行まで仕上げ、codex 3 ラウンド監査を収束。**push は起床後に承認**（就寝中はしない方針）。

### 保全コマンド（起床後・承認のうえ実行）
```bash
# 1) submodule(joho-suuri)で commit（安全パターン: add→即commit→検証）
cd /Users/mikiokofune/my-company/.company/education/university/joho-suuri
git add lec10/index.html lec10/_BRUSHUP_PROGRESS.md
git commit -m "lec10: 微分を「小さいものを捨てる」起点に再構成(6PART/28枚)＋16:9フィット・図の幾何・voice・codex監査収束"
git show --stat HEAD            # 巻き込み確認
git push origin main           # ← 承認後

# 2) 親(my-company)で submodule ポインタ＋監査ログを commit
cd /Users/mikiokofune/my-company
git add .company/education/university/joho-suuri .company/audit/reviews/2026-06-29
git commit -m "joho-suuri lec10 微分デッキ全面ブラッシュアップ＋audit ログ(rev1-3)"
git show --stat HEAD
git push origin main           # ← 承認後（submodule push 完了後）
```
### リバート
万一戻すなら scratchpad/index.BACKUP.html（リフレーム前25枚）。

## バックアップ
- 原本: scratchpad/index.BACKUP.html（リフレーム前 25枚 4125行）。リバートはこれ。

## 注意（メモリ準拠）
- submodule。push は submodule→親の順・整合性レポート承認後（就寝中は push しない）。
- 機密・著作物の混入なし（数学教材）。
