# 指示書09【優先度★】device-compat-check スキルの作成

## 目的

「iPad対応済み」「スマホOK」と宣言する前に、確認できる範囲を自動テストで検証し、
確認できない範囲(実機のみの挙動)を正直に区別して報告する。

## 背景

過去の作業ではファイル名に `_working_ios` `_ipad_ok` と付けたものの、
何をもって「OK」としたかの記録がない。しかも `_ok` 版と `_final` 版が
複数並び、どれが検証済みか分からなくなった。

## 作成するファイル

`.claude/skills/device-compat-check/SKILL.md`

## フロントマター(このまま使う)

```markdown
---
name: device-compat-check
description: 「スマホ対応した」「iPad対応済み」「モバイルOK」と報告・宣言する前に読む。自動で確認できる項目と実機でしか確認できない項目の切り分け。
---
```

## SKILL.md 本文に含める内容(全部入れる)

1. **宣言の前に自動確認できる項目**(Playwrightのモバイルエミュレーションで確認):
   - ページがエラーなく読み込める(consoleエラーゼロ)
   - viewportメタタグが効き、横スクロールが発生しない(意図したコンテナ以外で)
   - タップ対象がタップイベントに反応する
   - 主要ボタンが画面内に収まっている

2. **Playwrightスモークテストの雛形**(このまま載せる):

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

3. **自動確認できない項目**(正直に「実機確認が必要」と報告する):
   - 実際に音が出るか(音声アンロックの実挙動)
   - サイレントスイッチ・音量の影響
   - 実機Safari固有のレンダリング差
   これらは mobile-preview スキルの手順でユーザーに実機確認を依頼する。

4. **報告テンプレート**: 「自動確認: ◯◯を確認済み / 実機確認が必要: ◯◯」の
   2段構えで報告する。全部まとめて「対応済み」と言わない。

## 完了条件

- [ ] `.claude/skills/device-compat-check/SKILL.md` が存在する
- [ ] 上記1〜4がすべて本文に入っている
- [ ] 「自動確認できない項目」の節が入っている
- [ ] コミットメッセージが「スキル追加: device-compat-check — …」形式である
