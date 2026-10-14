# 学習ログ

日付ごとに、学んだこと、理解の修正、リポジトリの改善を短く記録します。
詳しい内容はテーマ別のノートにまとめ、ここからリンクします。
日付は日本時間（JST）を基準にします。

記録の形は、必要に応じて次の例を使います。

```markdown
## YYYY-MM-DD

- 学習：学んだことの要点。関連ノートへのリンク。
- 修正：見直した理解と、その理由。関連ノートへのリンク。
- 改善：使いにくい点、改善案、実施した変更とその理由。
```

## 2026-10-14

- 改善：個人学習の目的、記録方法、AIとの壁打ちでの使い方、運用・改善方針を[README](README.md)に整理した。
- 改善：[用語・概念](notes/concepts/README.md)、[AWSサービス](notes/services/README.md)、[トラブルシューティング](notes/troubleshooting/README.md)の学習用フォルダと、[共通テンプレート](notes/TEMPLATE.md)を用意した。
- 学習：[認証と認可の違い](notes/concepts/authentication-and-authorization.md)を、AWSの公式説明とGitHubでの経験に沿って整理した。本人確認、操作の許可・拒否、トークンの権限を区別して理解した。
- 学習：[GitHubで会社用・個人用の認証を分ける方法](notes/troubleshooting/github-authentication.md)を記録した。`useHttpPath`、`username`、`--local`の意味と、トークンを入力して初回pushが成功した結果をまとめた。
