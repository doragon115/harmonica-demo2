---
name: ios-audio-unlock
description: iOS/iPad/iPhoneのSafariでWeb Audioの音が鳴らない・AudioContextが動かない・タップしても無音、という問題が出た時に必ず最初に読む。スマホ対応の音アプリを新規に作る時も読む。
---

## 大原則

iOS SafariではユーザーのタップやクリックというUI操作のイベントハンドラ内で
`AudioContext` を作成または `resume()` しない限り、音は絶対に鳴らない。
コードのバグではなくブラウザの仕様なので、コードをいくら見直しても直らない。

## チェックリスト(この順に確認する)

- AudioContext の生成は `new (window.AudioContext || window.webkitAudioContext)()` になっているか
- `audioContext.state === 'suspended'` の時に、`click` / `touchstart` の両方に
  `{once: true}` でresumeハンドラを付けているか
- `resume()` は Promise を返すので、**`.then()` の中で** その後の処理(再描画・再生開始)を
  しているか(← 過去にここで90分ハマった)
- iPad本体のサイレントスイッチ・音量がゼロになっていないか(コードの問題ではない)
- `touchstart` に `preventDefault()` を付けてダブルタップズームを防いでいるか

## 正解コード(コピペで使える)

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

## デバッグの鉄則

「最小テストファイルを新規に作って原因を探す」のは最終手段。
先に上のチェックリストを上から順に潰す方が速い。最小テストを作る場合は
minimal-repro スキル(あれば)に従い、本番リポジトリの外で作る。

## このスキルができた経緯

2025-06-09深夜、iOSで音が鳴らない問題に90分かかった。最小テストファイルを2本作り、
完全版を4回アップし直した末、必要だったのは `audioContext.resume()` の完了後に
`renderHarmonica()` を呼ぶという1行だけだった。iOS Safariの定番・音声アンロック問題で、
知識があれば5分で即解決できた。
