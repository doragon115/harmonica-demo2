# 指示書06【優先度★★】single-html-app スキルの作成

## 目的

スマホ・タブレット対応のシングルHTMLアプリ(このリポジトリのハーモニカアプリのような物)を
新規に作る・大きく改修する時に、モバイル対応の定番項目を最初から入れておく。

## 背景

ハーモニカアプリはPCでは動いたが、iOS対応(音声アンロック・タッチ操作・ビューポート)を
後付けする過程で90分の回り道が発生した。最初からチェックリストがあれば防げた。

## 作成するファイル

`.claude/skills/single-html-app/SKILL.md`

## フロントマター(このまま使う)

```markdown
---
name: single-html-app
description: 1ファイル完結のHTMLアプリ(ツール・ゲーム・音アプリ・練習用ページ)を新規作成する時、またはスマホ対応・iPad対応にする時に読む。モバイル定番対応の抜け漏れ防止チェックリスト。
---
```

## SKILL.md 本文に含める内容(全部入れる)

1. **新規作成時の必須項目チェックリスト**:
   - `<meta name="viewport" content="width=device-width, initial-scale=1.0">`
   - タップ対象は最小 44×44px(Appleのガイドライン)
   - タップ要素に `touch-action: manipulation;`(ダブルタップズーム防止)
   - `click` と `touchstart` の両方に対応(touchstartでは `preventDefault()` を検討)
   - 音を使うなら **ios-audio-unlock スキルを必ず併読**
   - 横幅が広いレイアウトは `overflow-x: auto` のコンテナに入れる
   - 文字コードは UTF-8、`lang="ja"`

2. **ファイル構成の原則**: 1アプリ=1ファイル。CSS/JSも同一ファイル内に書く
   (配布とGitHub Pages公開が楽になるため)。ファイル名は `index.html` を推奨
   (GitHub PagesでURL直下から開けるため)。バリエーションを作る時は
   single-source-versioning スキルに従い、複製ではなくgitで管理する。

3. **動作確認の順序**: PCブラウザ → PCブラウザのデバイスモード → 実機
   (mobile-preview スキル参照)。「PCで動いた」だけで完成と言わない。
   特に音・バイブ・センサー系はデバイスモードでは確認できない。

4. **よくある落とし穴**(過去の実例として記載):
   - iOSの音声アンロック(→ ios-audio-unlock)
   - `resume()` 後の処理を `.then()` の外に書いてしまう
   - `position: absolute` の行レイアウトはスマホの文字サイズ変更で崩れやすい

## 完了条件

- [ ] `.claude/skills/single-html-app/SKILL.md` が存在する
- [ ] 上記1〜4がすべて本文に入っている
- [ ] 他スキル(ios-audio-unlock, single-source-versioning, mobile-preview)への参照が入っている
- [ ] コミットメッセージが「スキル追加: single-html-app — …」形式である
