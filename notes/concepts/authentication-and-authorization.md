# 認証と認可の違い

最終更新日：2026-10-14
公式資料の確認日：2026-10-14

## ひとことでいうと

**認証は「誰として接続しているか」の確認、認可は「その操作を許可するか」の判断。**

AWS IAMも、この2つを区別してアクセスを管理する。[AWS公式：IAMの仕組み](https://docs.aws.amazon.com/ja_jp/IAM/latest/UserGuide/intro-structure.html)

## 理解したこと

### 認証・認可・権限付与を分けて考える

| 言葉 | 自分の言葉での説明 | 例 |
| --- | --- | --- |
| 認証（Authentication） | 接続している人やアプリケーションの本人確認 | トークンを使ってGitHubへ接続する |
| 認可（Authorization） | 本人確認された相手からの操作要求を、権限に照らして許可・拒否する | 対象リポジトリへのpushを許可するか判断する |
| 権限付与 | 所有者や管理者が、できる操作の範囲を設定する | 共同作業者へ書き込み権限を与える |

身近な例では、入館時に社員証で本人確認することが認証、資料室に入ってよいか判断することが認可に当たる。

### ログインできても、すべての操作ができるとは限らない

AWSでは、認証された人やアプリケーションからのリクエストに対し、適用されるポリシーを評価して許可・拒否を決める。
例えば、AWSへサインインできたことだけでは、S3のオブジェクト取得や削除も許可されるとは判断できない。
人やアプリケーションなど、操作を要求する主体を「プリンシパル」と呼ぶ。[AWS公式：IAMの仕組み](https://docs.aws.amazon.com/ja_jp/IAM/latest/UserGuide/intro-structure.html)

### GitHubのトークンが使える権限

Personal Access Token（PAT）は、GitHubへの接続で使う認証情報の一つ。
Fine-grained PATでは、アクセス対象のリポジトリや操作の範囲を絞れる。
ただし、そのトークンの所有者であるアカウントが持っていない権限は追加できない。[GitHub公式：Personal Access Token](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/managing-your-personal-access-tokens)

「トークンに書き込みを許可する」ことは、既にアカウントが持つ権限の範囲内で、そのトークンに使わせる操作を設定すること。
別のアカウントへ新たにリポジトリの権限を与える操作とは分けて考える。

### PC側の認証設定とGitHub側の権限は別

Gitの認証設定は、接続時に渡すユーザー名や、保存済みの認証情報の探し方を指定する。
それによってGitHub側の権限が増えるわけではない。[Git公式：認証情報の扱い](https://git-scm.com/docs/gitcredentials)

HTTPS接続でユーザー名を指定しても、GitHubでの実際の本人確認にはトークンが使われる。
ユーザー名を`tokechan`に設定しただけで、別アカウントのトークンが`tokechan`のトークンに変わるわけではない。[GitHub公式：コマンドラインでのPAT利用](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/managing-your-personal-access-tokens#using-a-personal-access-token-on-the-command-line)

## 具体例・試したこと

2026-10-14に、個人学習用の`tokechan/aws-wall-hit`へpushする際、別アカウントとして接続して書き込みが拒否された。
その後、対象リポジトリのGit認証設定を変更し、個人用のトークンを入力してpushに成功した。

今回確認したこと：

- GitHubがどのアカウントとして認証するかと、そのアカウントでpushできるかは別の確認事項。
- 個人用のトークンで接続する設定にしても、会社用アカウントに新しい権限を付与するわけではない。
- 実際に成功した範囲は、このリポジトリへの初回push。AWSでの認証・認可操作はこの学習では実行していない。

具体的な設定とエラーの記録は、[GitHubで会社用・個人用の認証を分ける方法](../troubleshooting/github-authentication.md)にまとめる。

## まだわからないこと

- AWS IAMでは、複数のポリシーがある場合に、許可・拒否をどの順序やルールで判断するのか。
- IAMロールを使う場合、アプリケーションの認証情報はどのように取得されるのか。

これらは次の学習候補であり、今回の記録では具体的なAWS環境で検証していない。

## 参考資料

以下の公式資料を2026-10-14に確認した。

- [AWS：IAMとは](https://docs.aws.amazon.com/ja_jp/IAM/latest/UserGuide/introduction.html) — IAMが扱う認証と認可の概要。
- [AWS：IAMの仕組み](https://docs.aws.amazon.com/ja_jp/IAM/latest/UserGuide/intro-structure.html) — プリンシパルの認証とポリシーによる認可。
- [GitHub：Personal Access Tokenの管理](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/managing-your-personal-access-tokens) — トークンの権限と、その所有者が持つ権限との関係。
- [Git：gitcredentials](https://git-scm.com/docs/gitcredentials) — ユーザー名や認証情報の取得・保存の仕組み。

## 更新の記録

- 2026-10-14：認証・認可・権限付与の違いを、AWSの説明と今回のGitHubでの経験に沿って整理した。

[用語・概念の目次](README.md) · [学習ログ](../../learning-log.md)
