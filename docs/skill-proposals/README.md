# スキル提案書 — 回り道の軌跡から学ぶ

作成日: 2026-07-09
対象リポジトリ: doragon115/harmonica-demo2

## 1. なにを分析したか

依頼は「4月1日からのClaude Code内の回り道した軌跡を見て、優秀なスキルを作る提案を書き出す」こと。
ローカルのClaude Codeセッションログ(`~/.claude/projects/`)はクラウド実行環境からは参照できないため、
**このリポジトリに記録された作業の軌跡(コミット履歴とファイルの変遷)** を一次資料として分析した。
幸い、このリポジトリは回り道がそのままファイル名とコミットとして保存されている。

## 2. 回り道の軌跡(証拠つき)

2025年6月9日 深夜 3:39〜5:12(JST)の約90分間に、8回のアップロードが行われた。

| 時刻 | ファイル | 行数 | 実態 |
|---|---|---|---|
| 03:39 | cloude4harmonica.html | 639 | 完全版アプリ |
| 04:19 | cloude4harmonica_full_fixed.html | 639 | **元ファイルとmd5完全一致(変更ゼロ)** |
| 04:25 | test.html | 55 | 音が鳴らない→最小テスト作成 |
| 04:37 | harmonica-ios.html | 68 | さらに別の最小テスト |
| 04:43 | cloude4harmonica_working_ios.html | 639 | **実質の修正は1行だけ** |
| 04:52 | cloude4harmonica_final_mobile.html | 94 | 縮小版を新規作成 |
| 05:05 | cloude4harmonica_final_restored.html | 639 | **working_iosとmd5完全一致(変更ゼロ)** |
| 05:12 | cloude4harmonica_ipad_ok.html | 93 | final_mobileの見た目違い版 |

md5チェックサムによる裏付け:

```
0f1517a2… cloude4harmonica.html            ┐ 同一
0f1517a2… cloude4harmonica_full_fixed.html ┘
97714bba… cloude4harmonica_final_restored.html ┐ 同一
97714bba… cloude4harmonica_working_ios.html    ┘
```

90分の格闘で本当に必要だった変更は、次の**1行**だった:

```js
// 修正前
const resume=()=>audioContext.resume().then(()=>msg.remove());
// 修正後(iOSでresume後に再描画が必要だった)
const resume=()=>audioContext.resume().then(()=>{ msg.remove(); renderHarmonica(); });
```

これは iOS Safari の Web Audio 音声アンロック(ユーザー操作なしでは AudioContext が
suspended のままになる)という**既知の定番問題**であり、知識があれば5分で解決できた。

## 3. 回り道のパターン分類

| # | パターン | 症状 | 失われたもの |
|---|---|---|---|
| A | iOS音声問題の知識不足 | 最小テスト2本、試行錯誤90分 | 時間の大半 |
| B | ファイル複製による版管理 | `_fixed` `_final` `_restored` `_ok` が乱立 | どれが正本か不明に |
| C | 変更確認なしの保存 | md5同一ファイルを2回も再アップ | 「直った気がする」だけの周回 |
| D | 実機確認ループが遅い | 修正→GitHubアップ→iPadで開く→失敗、を8周 | 1周あたり約10分 |
| E | 実験ファイルの放置 | test.html等が本番リポジトリに残留 | リポジトリの信頼性 |
| F | コミットメッセージ全滅 | 全部「Add files via upload」 | 後から軌跡を追う手段 |

## 4. スキル提案一覧(10件)

Claude Codeのスキル(`.claude/skills/<名前>/SKILL.md`)として実装する。
各スキルの作成指示書は `instructions/` 配下にあり、**作業はHaikuが実行する前提**で書いてある。

| 優先度 | # | スキル名 | 由来パターン | 一言でいうと |
|---|---|---|---|---|
| ★★★ | 01 | ios-audio-unlock | A | iOS/iPadで音が鳴らない問題を5分で解決する知識集 |
| ★★★ | 02 | single-source-versioning | B | `_final` `_fixed` 複製を禁止し、gitで版管理させる |
| ★★★ | 03 | verify-before-save | C | 保存・コミット前に「本当に変わったか」を差分で確認 |
| ★★ | 04 | mobile-preview | D | ローカルサーバ+QRコードで実機確認を1周1分にする |
| ★★ | 05 | repo-tidy | B,E | 重複・実験ファイルを検出し正本1本に統合 |
| ★★ | 06 | single-html-app | A,D | スマホ対応シングルHTMLアプリの雛形とチェックリスト |
| ★ | 07 | minimal-repro | E | 最小再現ファイルはscratchpadで作り、解決後に片付ける |
| ★ | 08 | commit-messages-ja | F | 「何をなぜ」が分かる日本語コミットメッセージ |
| ★ | 09 | device-compat-check | A,C | 「対応済み」と言う前のPlaywrightスモークテスト |
| ★ | 10 | session-retro | 全部 | セッション終了時に回り道を振り返り次のスキル候補を残す |

## 5. Haikuへの作業の流れ

1. まず `instructions/00-haiku-common-guide.md` を読む(全スキル共通のルール)
2. 優先度★★★の 01→02→03 の順に指示書どおり作成する
3. 1スキル作るごとに完了条件チェックリストを確認してからコミットする
4. 余力があれば★★、★の順に進める
5. **パソコン上のClaude Codeで実行する場合のみ**: `instructions/11-local-session-log-analysis.md`
   に従い、4月1日以降の実セッションログ(`~/.claude/projects/`)を分析して追加提案を書く。
   本物のセッションログはクラウド環境からは読めないため、この分析はパソコン上でしかできない

## 6. 期待効果

- パターンAだけで **90分→5分**。同種の「定番ハマり」をスキル化するたびに同じ倍率で効く
- パターンB/Cの複製・空振り防止で、リポジトリが常に「正本1本」の状態を保つ
- パターンDの高速ループで、モバイル系の試行錯誤全般が約10倍速になる
