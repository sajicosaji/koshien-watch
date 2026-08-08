# koshien-watch

第108回全国高校野球選手権（甲子園）の「見守りプール」を毎日19:00(JST)に自動更新し、Discordに結果を配信するbotです。

## 仕組み

- `.github/workflows/koshien-watch.yml` が毎日19:00(JST)にGitHub Actions上でClaude Codeを実行
- Claude Codeが `prompt.md` の指示に従い、WebSearchで今日の試合結果を調べ、`bracket.json` を更新し、Discordに投稿する
- 更新後の `bracket.json` は自動でコミット・pushされる

## bracket.json の書式

大会の全カードを保持する唯一のデータファイル。ブロックごとに試合が並ぶ。

```json
{"round": "2回戦", "date": "2026-08-10", "time": "16:00",
 "team1": {"name": "高岡商", "pref": "富山", "owner": "熊田"},
 "team2": {"name": "高川学園", "pref": "山口", "owner": "栗原"},
 "winner": null}
```

- `winner` が `null` のままなら未確定、決着すると勝者チーム名が入る
- 次の試合の `team1`/`team2` が `null` の場合は「前の試合の勝者待ち」（`note` に説明あり）
- `name` が「確認中」のチームは出場校自体が未確定

## 手動で直したいとき

自動更新が結果を取り違えた・試合が中止/順延になった等で手直ししたい場合は、`bracket.json` を直接編集して
コミット・pushすればよい（Discordへの再通知はしたい場合は手動でwebhookにPOSTするか、次回の定期実行を待つ）。

## 秘密情報

- `CLAUDE_CODE_OAUTH_TOKEN`: GitHub Actions上でClaude Codeを動かすためのトークン
- `DISCORD_WEBHOOK_KOSHIEN`: 通知先DiscordチャンネルのWebhook URL

このリポジトリは公開だが、上記2つはGitHub Secretsに登録しているため公開されない。
担当者の実名（伊藤・鵜瀬・河村・熊田・栗原・西條・西村）は本人たち了承の上でbracket.jsonに含めている。

## 手動実行・テスト

GitHubの Actions タブから `koshien-watch` ワークフローを workflow_dispatch で手動実行できる。
手動実行時はクレジット節約のためモデルを自動でHaiku 4.5に切り替える（本番の定期実行はSonnet 5）。
