---
updated: 2026-06-19
prev_session: lec09「音・画像のデジタル表現」を新規作成(HTML 25枚) → インタラクティブ6・/hosoku 補足11・みきコン TOUR30step → voice/content 自己審査(ナイキスト用語厳密化) → cascade-brushup(動く図+対数バー) → submodule→親の順で push、GitHub Pages 公開済み
---

# 次セッション resume — joho-suuri lec09(第9回 音・画像のデジタル表現)

## 30 秒 status
lec09「音・画像のデジタル表現」**25 スライド完成・公開済み**(submodule joho-suuri `f4a7ccf` push 済 / 親 my-company `2c45069` push 済 → `https://mikikof.github.io/joho-suuri/lec09/`、目次トップにも第9回カード)。HTML(new-lecture Phase1)・インタラクティブ6・voice/content 自己審査・みきコンガイド・/hosoku 補足11件・cascade-brushup まで完了。**残るは任意工程の Excel 演習(Phase2)・PPTX・codex audit-review のみ**。

## 確定事項 / 意思決定(変えない前提)
- lec09 は `lec08/index.html` を基底にコピーし、不変ブロック(CSS / スライド制御 / Chart.js infra / みきコン engine / hosoku engine)を流用して作成。テーマ＝ユーザー指定「音・画像のデジタル表現(標本化定理・量子化・RGB・データ量計算)」。lec08 で次回送りにした範囲をそのまま接続。
- **5 PART・25枚**: intro3(表紙/前回lec08の振り返り/全体像) + PART1 標本化定理(扉/標本化周波数/標本化定理/折り返し雑音) + PART2 量子化と符号化(扉/量子化/量子化誤差/PCM) + PART3 音のデジタル化(扉/CD品質/音データ量/圧縮入口) + PART4 画像のデジタル化(扉/画素解像度/RGB/画像データ量) + PART5 データ量と圧縮(扉/単位の桁感/圧縮/AIへ) + SUMMARY(24) + 提出課題(25)。
- **インタラクティブ ID**: c1=折り返し雑音(slide7, 信号5Hz固定・PH=0.6位相付き), c2=量子化(slide9), c3=音データ量計算機(slide14, pick-btns), c4=解像度モザイク(slide17, canvas), c5=RGBミキサー(slide18), c6=画像データ量(slide19, pick-btns)。スライド遷移イベントは **`lec09-slide-change`**(canvas系 c1/c2/c4 が購読)。
- /hosoku 補足11件(チップ⇄`HOSOKU_SUPP` キー 1:1): SAMPLING/ALIAS/QUANT/QERROR/PCM/CDQ/AUDIODATA/PIXEL/RGB/IMGDATA/COMPRESS。各々に自己完結 SVG 図+検算済み数値+鋭い視点。hosoku engine(suppFig + モーダル)は lec08 から流用(lec09 content は suppFig 型を使わず inline SVG)。
- **みきコン TOUR**: 30 step、全セレクタが対象スライドに実在を機械検証済み。engine は lec08 と同一バイト(TOUR 配列のみ差し替え)。mikibar は既定 `collapsed`、アバター click で展開。
- **検算済み数値(再検算不要)**: CD品質 44100×16×2=1,411,200bps → 176,400B/s ≈172KB/s、1分≈10MB、180秒=31,752,000B=30.3MB、74分≈24曲 / フルHD 1920×1080×24÷8=6,220,800B=5.93MB / 4000×3000×24÷8=36,000,000B≈34MB / RGB 2²⁴=16,777,216色 / 1024=2¹⁰。
- **用語の厳密化(確定・戻さない)**: 「最高周波数の2倍」=**ナイキストレート**(必要な最低標本化周波数)、「標本化周波数の半分(fs/2)」=**ナイキスト周波数**、と書き分け済み(混同しない)。
- **cascade-brushup 済(維持)**: 動く図 CSS(`lec09Float`=章扉アイコン浮遊 / `lec09Pulse`=`#s5-dots` 標本点パルス / `lec09Flow`=`.lec09-flowg line` フロー矢印ダッシュ流れ12本 / `lec09Flick`=`.lec09-flick` slide23 RGB数値明滅)。全て `.slide.active` 限定 + `prefers-reduced-motion` で停止。slide21「単位の桁感」は 4 stat カード→**対数バー図**に差し替え済み。
- push 済: submodule joho-suuri `f4a7ccf`(両 ahead/behind 0)、親 my-company `2c45069`(submodule ポインタ1行のみ commit、他の無関係差分は温存)。

## 実行環境 / コマンド(コピペ可)
```bash
cd /Users/mikiokofune/my-company/.company/education/university/joho-suuri
# ブラウザ確認
open lec09/index.html
# HTML 整合チェック(タグ/JS ID/hosoku chips==keys)
python3 - <<'PY'
import re; s=open('lec09/index.html',encoding='utf-8').read()
print('div',len(re.findall(r'<div\b',s)),'/',s.count('</div>'),'| section',len(re.findall(r'<section\b',s)),'/',s.count('</section>'))
ids=set(re.findall(r'id="([^"]+)"',s)); refs=set(re.findall(r"getElementById\('([^']+)'\)",s)); print('missing ids:',sorted(refs-ids) or 'none')
chips=sorted(set(re.findall(r'data-supp="([A-Z]+)"',s))); keys=sorted(set(re.findall(r'\n  ([A-Z]+): \{',s.split('HOSOKU_SUPP = {',1)[1]))); print('hosoku chips==keys:',chips==keys)
PY
# headless スクショ(任意スライド)
CHROME="/Applications/Google Chrome.app/Contents/MacOS/Google Chrome"
"$CHROME" --headless=new --disable-gpu --hide-scrollbars --force-device-scale-factor=2 \
  --window-size=1280,1500 --virtual-time-budget=3500 \
  --screenshot=/tmp/s.png "file://$PWD/lec09/index.html#slide-7"
# JS エラー確認
"$CHROME" --headless=new --disable-gpu --virtual-time-budget=3000 --enable-logging=stderr \
  --dump-dom "file://$PWD/lec09/index.html#slide-7" 2>&1 | grep -iE "Uncaught|SyntaxError" | grep -v gpu
# hosoku/みきコンの実機テストは「最後の </body> の直前」に注入(rfind を使う)。
# 実行時 hash 変更は deck に伝わらない → URL に #slide-N を付けてロードしてからチップ click
# Excel 生成用 venv(openpyxl)
ls .venv 2>/dev/null && source .venv/bin/activate || echo "venv 無ければ python3 -m venv .venv && pip install openpyxl"
```

## 残工程チェックリスト(すべて任意・ユーザー判断)
- [ ] **codex audit-review**(教材系＝web検索禁止・lecture と教科書/正本準拠で `/audit-review`)。lec09 は初稿+brushup 済だが外部監査は未実施。5件以上修正したら再 audit。
- [ ] **Excel 演習 `lec09/情報数理入門_第9回.xlsx`**(new-lecture Phase2)。`.claude/skills/new-lecture/EXCEL_GUIDE.md` 規約・Google スプレッドシート前提。HTML とタイアップ: 標本化周波数×量子化ビット×チャンネル×秒(音データ量)、横×縦×色深度(画像データ量)、2進数⇄10進、2ⁿ。HTML の例題より一段レベルアップ。
- [ ] (任意)PPTX `lec09/情報数理入門_第9回.pptx` — ユーザーが明示要求した時のみ。
- [ ] (参考)lec07/lec08 の Excel/PPTX も未着手のまま(着手要否はユーザー判断)。

## 作業 / 執筆方針(ユーザー明示の制約)
- education/university 配下 = **Opus 4.7[1M] 死守 + 毎ターン ultrathink + 鬼教諭スタンス**。audit は codex `-p review-paper`、教科書準拠(web検索禁止)。
- voice: 大学講義として自然な口語。AI 臭・誇大表現・抽象動詞メタファー・スローガン調・体言止め命令形を禁止。例えは身近を優先。
- 図のクオリティ規律: **文字と図を重ねない / 精密な幾何 / ラベルは極力 HTML / 動きは意味あるときだけ**。joho-suuri は自作 SVG 許容(lectures/articles の「OSS必須」ルールは非適用)。
- /hosoku: 図+検算済み数値+鋭い視点。数値は自分で検算。チップは button。
- Excel は HTML と数値・PART を一致させる。
- 大規模な near-全面書き換えは「一時ファイルに新コンテンツ → python で正確なアンカー間に splice」が安全(旧テキスト再入力を避ける)。**splice アンカーはコメント行に先にヒットしないか必ず確認**(lec09 で `window.HOSOKU_SUPP = {` がエンジンのコメントに誤ヒットしエンジン消失→lec08 から復元した実例あり)。

## 直近 commit / ポインタ・関連ファイル
- submodule joho-suuri: `f4a7ccf`(lec09 + ルート目次カード)。親 my-company: `2c45069`(submodule ポインタ更新)。両 origin 同期済。
- lec09 本体: `lec09/index.html`(25 スライド、インタラクティブ6・補足11・みきコン搭載・brushup済)。
- 目次: ルート `index.html`(第9回カード追加)。公開 URL: `https://mikikof.github.io/joho-suuri/`。
- 規約: `.claude/skills/new-lecture/`(SKILL/TEMPLATE_GUIDE/EXCEL_GUIDE)、`hosoku`、`miki-guide/engine/miki-guide.block.html`(正本)、`lec-voice-audit`/`lec-content-check`/`examplus`。
- このセッションの作業: lec08 を基底に lec09 を全面新規作成 → 検証 → brushup → commit/push。
