# 指示書04【優先度★★】mobile-preview スキルの作成

## 目的

「修正→GitHubにアップ→iPadでページを開く→動かない→また修正」という
1周10分のループを、ローカルサーバ+実機で1周1分に短縮する。

## 背景

過去の作業では、実機(iPad)で確認するために毎回GitHubへアップロードしていた。
90分で8回アップロード=確認のためだけにリポジトリ履歴が汚れ、時間も失われた。
同じWi-Fi内ならローカルサーバを立てて実機から直接開ける。

## 作成するファイル

`.claude/skills/mobile-preview/SKILL.md`

## フロントマター(このまま使う)

```markdown
---
name: mobile-preview
description: iPhone/iPad/Androidの実機でHTMLやWebアプリの動作確認をしたい時、スマホで見たい・タブレットで試したいと言われた時に読む。GitHubにアップして確認するのは最終手段。
---
```

## SKILL.md 本文に含める内容(全部入れる)

1. **原則**: 実機確認のためにGitHubへアップロードしない。
   ローカルサーバを立て、同じWi-Fi上の実機からIPアドレスで直接開く。

2. **手順**(このまま載せる):

   ```bash
   # 1) このマシンのローカルIPを確認(例: 192.168.1.23)
   hostname -I 2>/dev/null || ipconfig getifaddr en0
   # 2) プロジェクトのディレクトリでサーバを起動
   python3 -m http.server 8000
   # 3) 実機のブラウザで開く
   #    http://<ローカルIP>:8000/ファイル名.html
   ```

   注意: クラウド実行環境(Claude Code on the web等)ではローカルサーバに実機から
   届かないため、この手順はユーザーのローカルマシンでの実行を案内する。

3. **実機でconsoleが見えない問題への対処**: iOS Safariではエラーが見えないため、
   デバッグ中だけ `<head>` に次の1行を入れると画面内にconsoleが出る(eruda)。
   確認が終わったら必ず削除する:

   ```html
   <script src="https://cdn.jsdelivr.net/npm/eruda"></script>
   <script>eruda.init();</script>
   ```

4. **代替手段の使い分け**(表で載せる):
   - 同じWi-Fiにいる → ローカルサーバ(上記)。1周1分
   - 離れた場所の人に見せる → GitHub Pagesに**1本だけ**デプロイ(複製を作らない)
   - 見た目だけ確認 → PCブラウザのデバイスモード(Chrome DevTools)。ただし
     **音・タッチ・実機Safari固有の挙動は再現されない**ので過信しない

5. **アップロードするのは確認が取れてから**: 実機で動作確認できた変更だけを
   コミットする。「動くか試すためのコミット」を作らない。

## 完了条件

- [ ] `.claude/skills/mobile-preview/SKILL.md` が存在する
- [ ] 上記1〜5がすべて本文に入っている
- [ ] erudaを「確認後に必ず削除する」注意書きが入っている
- [ ] コミットメッセージが「スキル追加: mobile-preview — …」形式である
