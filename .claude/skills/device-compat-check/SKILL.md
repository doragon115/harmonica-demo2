---
name: device-compat-check
description: 「スマホ対応した」「iPad対応済み」「モバイルOK」と報告・宣言する前に読む。自動で確認できる項目と実機でしか確認できない項目の切り分け。
---

## 宣言の前に自動確認できる項目

Playwrightのモバイルエミュレーションで確認:

- ページがエラーなく読み込める(consoleエラーゼロ)
- viewportメタタグが効き、横スクロールが発生しない(意図したコンテナ以外で)
- タップ対象がタップイベントに反応する
- 主要ボタンが画面内に収まっている

## Playwrightスモークテストの雛形

```js
const { webkit, devices } = require('playwright');
(async () => {
  const browser = await webkit.launch();
  const ctx = await browser.newContext({ ...devices['iPad (gen 7)'] });
  const page = await ctx.newPage();
  const errors = [];
  page.on('console', m => { if (m.type() === 'error') errors.push(m.text()); });
  await page.goto('file://' + process.cwd() + '/index.html');
  await page.tap('body');
  console.log(errors.length ? '❌ consoleエラー: ' + errors.join('\n') : '✅ エラーなし');
  await browser.close();
})();
```

注: この環境ではChromiumが `/opt/pw-browsers` に導入済み。webkitがなければ
`chromium.launch({ executablePath: '/opt/pw-browsers/chromium' })` とiPhoneデバイス設定で代用する。

## 自動確認できない項目

正直に「実機確認が必要」と報告する:

- 実際に音が出るか(音声アンロックの実挙動)
- サイレントスイッチ・音量の影響
- 実機Safari固有のレンダリング差

これらは **mobile-preview スキル**の手順でユーザーに実機確認を依頼する。

## 報告テンプレート

「自動確認: ◯◯を確認済み / 実機確認が必要: ◯◯」の
2段構えで報告する。全部まとめて「対応済み」と言わない。
