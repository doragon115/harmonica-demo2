---
name: mobile-preview
description: iPhone/iPad/Androidの実機でHTMLやWebアプリの動作確認をしたい時、スマホで見たい・タブレットで試したいと言われた時に読む。GitHubにアップして確認するのは最終手段。
---

## 原則

実機確認のためにGitHubへアップロードしない。
ローカルサーバを立て、同じWi-Fi上の実機からIPアドレスで直接開く。

## 手順

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

## 実機でconsoleが見えない問題への対処

iOS Safariではエラーが見えないため、
デバッグ中だけ `<head>` に次の1行を入れると画面内にconsoleが出る(eruda)。
**確認が終わったら必ず削除する**:

```html
<script src="https://cdn.jsdelivr.net/npm/eruda"></script>
<script>eruda.init();</script>
```

## 代替手段の使い分け

| 状況 | 手段 | 速度 | 注意点 |
|---|---|---|---|
| 同じWi-Fiにいる | ローカルサーバ(上記) | 1周1分 | 実機がないと不可 |
| 離れた場所の人に見せる | GitHub Pagesに1本だけデプロイ | 遅い | 複製を作らない |
| 見た目だけ確認 | PCブラウザのデバイスモード | 即座 | 音・タッチ・Safari固有の挙動は再現されない |

## アップロードするのは確認が取れてから

実機で動作確認できた変更だけをコミットする。
「動くか試すためのコミット」を作らない。
