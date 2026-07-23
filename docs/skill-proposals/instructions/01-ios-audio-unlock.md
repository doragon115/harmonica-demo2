# 指示書01【優先度★★★】ios-audio-unlock スキルの作成

## 目的

iOS/iPadのSafariで「音が鳴らない」問題を、試行錯誤なしで5分以内に解決できるようにする。

## 背景(どの回り道から生まれた提案か)

2025-06-09深夜、iOSで音が鳴らない問題に90分かかった。最小テストファイルを2本作り、
完全版を4回アップし直した末、必要だったのは「`audioContext.resume()` の完了後に
画面を再描画する」という1行だけだった。これはiOS Safariの定番問題で、知識があれば即解決できた。

## 作成するファイル

`.claude/skills/ios-audio-unlock/SKILL.md`

## フロントマター(このまま使う)

```markdown
---
name: ios-audio-unlock
description: iOS/iPad/iPhoneのSafariでWeb Audioの音が鳴らない・AudioContextが動かない・タップしても無音、という問題が出た時に必ず最初に読む。スマホ対応の音アプリを新規に作る時も読む。
---
```

## SKILL.md 本文に含める内容(全部入れる)

1. **大原則**: iOS SafariではユーザーのタップやクリックというUI操作のイベントハンドラ内で
   `AudioContext` を作成または `resume()` しない限り、音は絶対に鳴らない。
   コードのバグではなくブラウザの仕様なので、コードをいくら見直しても直らない。

2. **チェックリスト**(この順に確認する、と明記):
   - AudioContext の生成は `new (window.AudioContext || window.webkitAudioContext)()` になっているか
   - `audioContext.state === 'suspended'` の時に、`click` / `touchstart` の両方に
     `{once: true}` でresumeハンドラを付けているか
   - `resume()` は Promise を返すので、**`.then()` の中で** その後の処理(再描画・再生開始)を
     しているか(← 過去にここで90分ハマった)
   - iPad本体のサイレントスイッチ・音量がゼロになっていないか(コードの問題ではない)
   - `touchstart` に `preventDefault()` を付けてダブルタップズームを防いでいるか

3. **コピペで使える正解コード**(このまま載せる):

   ```js
   let audioContext;
   function ensureAudio(afterUnlock) {
     if (!audioContext) {
       audioContext = new (window.AudioContext || window.webkitAudioContext)();
     }
     if (audioContext.state === 'suspended') {
       const resume = () => audioContext.resume().then(() => {
         afterUnlock(); // ← resume完了後に再描画や再生をここで行う。これを忘れると無音のまま
       });
       document.body.addEventListener('click', resume, { once: true });
       document.body.addEventListener('touchstart', resume, { once: true });
     } else {
       afterUnlock();
     }
   }
   ```

4. **デバッグの鉄則**: 「最小テストファイルを新規に作って原因を探す」のは最終手段。
   先に上のチェックリストを上から順に潰す方が速い。最小テストを作る場合は
   minimal-repro スキル(あれば)に従い、本番リポジトリの外で作る。

5. **このスキルができた経緯**: 2025-06-09に90分かけて1行の修正にたどり着いた実例を
   2〜3行で記録する(README.mdの「回り道の軌跡」の要約でよい)。

## 完了条件

- [ ] `.claude/skills/ios-audio-unlock/SKILL.md` が存在する
- [ ] フロントマターの name がディレクトリ名と一致している
- [ ] 上記1〜5の内容がすべて本文に入っている
- [ ] コード例が指示書のものと一致している(改変していない)
- [ ] コミットメッセージが「スキル追加: ios-audio-unlock — …」形式である

## やってはいけないこと

- コード例を自分の知識で「改良」しない
- 既存のHTMLファイルを修正しない(このスキルは知識の記録であり、アプリ修正は別作業)
