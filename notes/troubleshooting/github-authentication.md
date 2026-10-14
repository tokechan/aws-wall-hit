# GitHubで会社用・個人用の認証を分ける方法

最終更新日：2026-10-14
公式資料の確認日：2026-10-14

## ひとことでいうと

**リポジトリのURLごとに認証情報を区別し、個人用リポジトリでは個人用アカウントのトークンを使う。**

GitHub公式は、複数アカウントでHTTPS接続を使う方法として、リポジトリごとに異なる認証情報を保存する手順を案内している。[GitHub公式：複数アカウントの管理](https://docs.github.com/en/account-and-profile/how-tos/account-management/managing-multiple-accounts)

## 理解したこと

### `useHttpPath true`は、URLのパスも認証情報の区別に使う

今回の接続先：

```text
https://github.com/tokechan/aws-wall-hit.git
```

| URLの部分 | 意味 |
| --- | --- |
| `https` | 接続に使うプロトコル |
| `github.com` | 接続先のホスト |
| `tokechan/aws-wall-hit.git` | 所有者名とリポジトリ名を含むパス |

通常、GitはHTTPSの認証情報を扱う際、URLのパスを区別に使わない。
`useHttpPath`を`true`にすると、パスも認証情報を探す・保存する際の情報として認証情報の管理ツールへ渡す。
これはリポジトリの存在や書き込み権限を確認する設定ではなく、どの認証情報を使うかを区別するための設定。[Git公式：gitcredentials](https://git-scm.com/docs/gitcredentials)

### `username tokechan`は、ユーザー名の初期値を指定する

ユーザー名を`tokechan`にすると、接続時に使うユーザー名や保存済み認証情報の検索に利用される。
実際にGitHubがどのアカウントとして本人確認するかは、入力したトークンによる。
この設定だけで権限が付いたり、別アカウントのトークンが切り替わったりするわけではない。[Git公式：username](https://git-scm.com/docs/gitcredentials)・[GitHub公式：PATの利用](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/managing-your-personal-access-tokens#using-a-personal-access-token-on-the-command-line)

### 今回は公式の方法を、このリポジトリだけに適用した

GitHub公式の`useHttpPath`のコマンド例は`--global`を使う。
今回の手順では、設定の適用範囲をこのリポジトリに絞るため、Git公式がサポートする`--local`を使った。
また、認証情報の検索で個人用のユーザー名を使うように、`username`を設定した。[GitHub公式：複数アカウントの管理](https://docs.github.com/en/account-and-profile/how-tos/account-management/managing-multiple-accounts)・[Git公式：git config](https://git-scm.com/docs/git-config)

公式ページには、保存済みの認証情報を消去する手順も載っている。
今回は認証情報を一括消去するコマンドは実行せず、リポジトリ単位の設定とトークン入力で成功した。

`--local`の設定は、現在のリポジトリの`.git/config`に保存される。
GitHubへ送る設定ではなく、別のPCにcloneしても、そのPCの認証設定として自動で引き継がれるものではない。
ほかのリポジトリで同じ設定を使いたい場合は、そのリポジトリでも設定する。

## 具体例・試したこと

### 実施時の環境と対象

- 実施日：2026-10-14
- 環境：macOS、zsh、GitのHTTPS接続
- 個人用アカウント：`tokechan`
- 対象リポジトリ：`tokechan/aws-wall-hit`
- 当時の認証情報の管理ツール：`osxkeychain`（macOSのキーチェーン）

### 1. 個人用アカウントでトークンを作成する

ブラウザで`tokechan`としてログインし、Fine-grained Personal Access Tokenを作成した。
トークンは、GitHubへの接続で本人確認に使う認証情報。

| 項目 | 今回の設定 |
| --- | --- |
| Resource owner | `tokechan` |
| Repository access | `Only select repositories` |
| Selected repositories | `tokechan/aws-wall-hit` |
| Contents | `Read and write` |
| Metadata | `Read-only`（作成画面で必須と表示された） |

`Metadata`は、リポジトリの基本情報や共同作業者一覧などの参照に使われる権限。[GitHub公式：Metadataの権限](https://docs.github.com/en/rest/authentication/permissions-required-for-fine-grained-personal-access-tokens#repository-permissions-for-metadata)

Fine-grained PATでは、選択したリポジトリへの権限を絞れるが、公開リポジトリへの読み取りアクセスも含まれる。
有効期限も設定し、トークンの値は作成時にコピーして安全に保管する。[GitHub公式：PATの管理](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/managing-your-personal-access-tokens)
今回表示された案内では、作成後に同じトークンの値を再表示できないため、作成時のコピーが必要だった。
トークンの値は、このノートやGitの履歴には記録しない。

### 2. 対象リポジトリで認証設定を変更する

今回使った作業フォルダへ移動して実行した。
別の環境で使う場合は、`cd`の移動先をその環境のリポジトリに合わせる。

```bash
cd "/Users/svc-tokeshi/Documents/ChatGPT/AWS-wall-hit"

git config --local credential.https://github.com.useHttpPath true
git config --local credential.https://github.com.username tokechan
```

| コマンドの部分 | 意味 |
| --- | --- |
| `git config` | Gitの設定を変更する |
| `--local` | 現在のリポジトリの設定ファイルに保存する |
| `credential.https://github.com` | GitHubへのHTTPS接続で使う認証設定 |
| `useHttpPath true` | パスも含めて認証情報を区別する |
| `username tokechan` | ユーザー名の初期値を`tokechan`にする |

この2つの設定コマンド自体には、トークンの保存やpushは含まれない。[Git公式：認証設定](https://git-scm.com/docs/gitcredentials)

### 3. トークンを使ってpushする

READMEの初回コミットは既に作成されていたため、次を実行した。

```bash
git push -u origin main
```

`Password`を求められたら、GitHubにログインするパスワードではなく、作成したトークンの全文を貼り付けてEnterを押す。
ユーザー名も聞かれた場合は`tokechan`を指定する。[GitHub公式：コマンドラインでのPAT利用](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/managing-your-personal-access-tokens#using-a-personal-access-token-on-the-command-line)

### 発生したエラーと確認できたこと

| 表示・結果 | 今回確認できたこと |
| --- | --- |
| 別アカウント名を伴う`Permission denied`と403 | そのアカウントでの接続では、対象リポジトリへの書き込みが拒否された |
| `Invalid username or token`と`Authentication failed` | 入力した認証情報が受け付けられず、pushは失敗した |
| 再試行後の`[new branch] main -> main` | GitHub側に`main`が作成され、pushが成功した |
| `branch 'main' set up to track 'origin/main'.` | ローカルの`main`に、`origin/main`を追跡する設定が付いた |

認証エラーの際に入力した値は記録・検証していないため、入力ミスやトークンの状態など、失敗の細かい原因は断定しない。
端末が成功を報告した範囲は、この個人用リポジトリへの初回push。
会社用リポジトリでの操作や、別のPCでの動作は今回確認していない。

## まだわからないこと

- トークンが期限切れになった場合、キーチェーンに保存された認証情報を、対象を絞って更新する手順。
- 同じPCでSSH接続を使う場合、アカウントごとに鍵を分ける方法。

これらは次の学習候補。今回成功した手順はHTTPSとPATを使ったもの。
認証と権限の考え方は、[認証と認可の違い](../concepts/authentication-and-authorization.md)も参照する。

## 参考資料

以下の公式資料を2026-10-14に確認した。

- [GitHub：複数アカウントの管理](https://docs.github.com/en/account-and-profile/how-tos/account-management/managing-multiple-accounts) — HTTPSでリポジトリごとに認証情報を分ける方法。
- [GitHub：認証について](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/about-authentication-to-github) — ブラウザやコマンドラインなど、接続方法ごとの認証。
- [GitHub：Personal Access Tokenの管理](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/managing-your-personal-access-tokens) — トークンの作成、権限、HTTPS接続での利用。
- [GitHub：Fine-grained PATに必要な権限](https://docs.github.com/en/rest/authentication/permissions-required-for-fine-grained-personal-access-tokens#repository-permissions-for-metadata) — Metadataで参照できる情報。
- [Git：gitcredentials](https://git-scm.com/docs/gitcredentials) — `useHttpPath`、`username`、認証情報の管理ツールの役割。
- [Git：git config](https://git-scm.com/docs/git-config) — `--local`と`--global`の設定範囲。

## 更新の記録

- 2026-10-14：このリポジトリで実施した認証設定、トークンの権限、失敗と成功の表示を整理した。

[トラブルシューティングの目次](README.md) · [学習ログ](../../learning-log.md)
