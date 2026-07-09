---
name: single-html-app
description: 1ファイル完結のHTMLアプリ(ツール・ゲーム・音アプリ・練習用ページ)を新規作成する時、またはスマホ対応・iPad対応にする時に読む。モバイル定番対応の抜け漏れ防止チェックリスト。
---

## 新規作成時の必須項目チェックリスト

- `<meta name="viewport" content="width=device-width, initial-scale=1.0">`
- タップ対象は最小 44×44px(Appleのガイドライン)
- タップ要素に `touch-action: manipulation;`(ダブルタップズーム防止)
- `click` と `touchstart` の両方に対応(touchstartでは `preventDefault()` を検討)
- 音を使うなら **ios-audio-unlock スキルを必ず併読**
- 横幅が広いレイアウトは `overflow-x: auto` のコンテナに入れる
- 文字コードは UTF-8、`lang="ja"`

## ファイル構成の原則

1アプリ=1ファイル。CSS/JSも同一ファイル内に書く
(配布とGitHub Pages公開が楽になるため)。
ファイル名は `index.html` を推奨
(GitHub PagesでURL直下から開けるため)。
バリエーションを作る時は **single-source-versioning スキル**に従い、
複製ではなくgitで管理する。

## 動作確認の順序

PCブラウザ → PCブラウザのデバイスモード → 実機
(**mobile-preview スキル**参照)。
「PCで動いた」だけで完成と言わない。
特に音・バイブ・センサー系はデバイスモードでは確認できない。

## よくある落とし穴(過去の実例)

- **iOSの音声アンロック** (→ **ios-audio-unlock スキル**)
- `resume()` 後の処理を `.then()` の外に書いてしまう
- `position: absolute` の行レイアウトはスマホの文字サイズ変更で崩れやすい
