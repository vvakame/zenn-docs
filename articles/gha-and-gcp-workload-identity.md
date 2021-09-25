---
title: "GitHub Actions + google-github-actions/auth で GCP keyless CI/CD"
emoji: "😺"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["gcp", "githubactions"]
published: false
---

要約： GitHub ActionsでCI/CD的なことやろうとしたとき、SecretsとかにGCPのService AccountのKeyとか置かなくてもデプロイとかできるようになったらしいのでやったらできた。

## 経緯

[AWS federation comes to GitHub Actions](https://awsteele.com/blog/2021/09/15/aws-federation-comes-to-github-actions.html) という記事が出て。

[GitHub ActionsのOIDC id tokenでGCPにアクセスしてみた](https://ryotarai.hatenablog.com/entry/github-acitons-id-token-gcp) といってGCPでもやれるか確かめた人が出て。

[Use gcloud with credentials from identity federation with OIDC](https://issuetracker.google.com/issues/187734550) に

> For Github, you can check this Github Action: https://github.com/google-github-actions/auth

というタレコミがあったのでやってみることにした。

細かいことはあまり深追いせず、やったらできた、という記事なので細かいところが気になった人は自分で調べて記事書いてこの記事のコメント欄に書いといてください！よろしくおねがいします！読みます！

あと、そのうち公式からちゃんとしたドキュメントとかが出てくると思うのでそっちが出たらそっちを読むのがいいと思います。

## やりたいことの詳細

### 今まで

GitHub ActionsでAppEngineにデプロイしたいとき、Service Accountを発行してJSON形式のcredentialをGitHubのSecretsに持たせていた。
使うときはcredentialを `gcloud auth activate-service-account` に食わせたり、適当にファイルとしてどっかに置いて `GOOGLE_APPLICATION_CREDENTIALS` とか経由で使ったりしていた。
この鍵は通常非常に寿命が長い（デフォルトで無期限）。

これの問題点として、もしなんらかの理由によりcredentialが流出したらこれをrevokeしてSecretsを入れ替えないといけなかった。
つまり、鍵の管理コストがかかっていた。わりと属人化しやすいところ感はある。

### これから

GitHub Actionsで OIDC token を発行できるようになった。
この機能をGCPの[Workload identity federation](https://cloud.google.com/iam/docs/workload-identity-federation)と組み合わせると、君GitHubのとこのworkflowなの？じゃ寿命が短い権限貸してあげるね。
みたいな感じでkeylessで権限がもらえるようになった。

なんらかの理由によりcredentialが流出しても、この鍵は寿命が短いので攻撃者がこれを使える時間は限られている。
`google-github-actions/auth` のデフォルトだと1時間（3600秒）。
つまり、一発設定すれば鍵の管理コストを払う必要がなくなる。
さらに、セキュリティ的にも多少マシになる。はず。

## やること

基本的に https://github.com/google-github-actions/auth に書いてあることを上から適当にやっていくだけ。
ただまぁ、書いてある通りに追っても何が最適っぽいかを読み解くのがちょっとめんどかったのでここでは最適な構成の手順書を提供する、というモチベで書いていきます。
それぞれの項目がどういう意味合いなのかは僕もふんわりとしか理解してないので詳しい言及は避けます。

### 下準備

gcloud sdk の version を最新にする。
筆者が試した時点では `358.0.0` では動いている。 `347.0.0` ではだめだった。

### GCP側でやること

GCPのService Accountの準備と権限の設定自体はすでに動いているものがあるので割愛。
JSON形式の鍵でやるときと特に変わる箇所はありません。

```
$ export PROJECT_ID=<対象のGCPのID>
$ export POOL_NAME=github-actions   # お好きに
$ export PROVIDER_NAME=gha-provider # お好きに
$ export SA_EMAIL=<Service Accountのメアド>
$ export GITHUB_REPO=<vvakame/til 的なやつ>

# IAM Service Account Credentials API を有効にする
$ gcloud services enable iamcredentials.googleapis.com --project "${PROJECT_ID}"

# Workload IdentityにPoolを作成する Poolが何かは理解していない
$ gcloud iam workload-identity-pools create "${POOL_NAME}" \
    --project="${PROJECT_ID}" --location="global" \
    --display-name="use from GitHub Actions"
$ export WORKLOAD_IDENTITY_POOL_ID=$( \
    gcloud iam workload-identity-pools describe "${POOL_NAME}" \
      --project="${PROJECT_ID}" --location="global" \
      --format="value(name)" \
  )
$ echo $WORKLOAD_IDENTITY_POOL_ID

# PoolにProvierを作成する attribute-mappingで色々遊べるぽい
$ gcloud iam workload-identity-pools providers create-oidc "${PROVIDER_NAME}" \
    --project="${PROJECT_ID}" --location="global" \
    --workload-identity-pool="${POOL_NAME}" \
    --display-name="use from GitHub Actions provider" \
    --attribute-mapping="google.subject=assertion.sub,attribute.repository=assertion.repository,attribute.actor=assertion.actor,attribute.aud=assertion.aud" \
    --issuer-uri="https://vstoken.actions.githubusercontent.com" \
    --allowed-audiences="sigstore"

# Workload IdentityでSAにimpersonateできるようにする
# 全GitHubリポジトリのWorkflowからできちゃうとヤバいので可能なリポジトリを絞る
$ gcloud iam service-accounts add-iam-policy-binding "${SA_EMAIL}" \
    --project="${PROJECT_ID}" \
    --role="roles/iam.workloadIdentityUser" \
    --member="principalSet://iam.googleapis.com/${WORKLOAD_IDENTITY_POOL_ID}/attribute.repository/${GITHUB_REPO}"
```

たぶんこれが一番楽でぼちぼち安全だと思います。
[こっちの記事](https://ryotarai.hatenablog.com/entry/github-acitons-id-token-gcp)だと `sub` の値を使っているけど、この記事ではドキュメントに従って `repository` の値を使っている。
[GitHub Token Format](https://github.com/google-github-actions/auth#github-token-format)を見ると `actor` とかで制限かけることもできそうだけど、まぁ今回は `repository` でいいかな。

### GitHub Actions側でやること

GitHub Actionsの job の `permissions` に `id-token: write` を追加。
もし今まで `permissions` がなかった場合、[ドキュメント](https://docs.github.com/en/actions/security-guides/automatic-token-authentication#permissions-for-the-github_token)を読んで設定してください。
指定なしの場合暗黙の権限が結構色々とついていて、 `id-token: write` だけ書き足すとそれらが無くなってコケたりすると思います。
とはいえ、セキュリティを気にする人たちならちゃんと明示的に指定している場合がほとんどでしょう。

あとはworkflowのyamlに適当にちょいちょいstepを追加します。
僕がやっている技術書典では、stg環境とprod環境があって、一定のルールに従ってデプロイ先を変えています。
デプロイ先はGCPプロジェクト毎にわかれているので若干めんどくさいです。

```
- id: auth_stg
  if:  ${{ stg環境だと判定する条件 }}
  name: Authenticate to Google Cloud for stg
  uses: google-github-actions/auth@v0.3.0
  with:
    create_credentials_file: 'true'
    workload_identity_provider: projects/XXXX/locations/global/workloadIdentityPools/YYYY/providers/ZZZZ
    service_account: foobar@example.iam.gserviceaccount.com
    access_token_lifetime: 1200s
```

とかして、同様に `auth_prod` とかも定義しておきます。

あとは作成したcredentialをgcloudコマンドで使えるようにしてやります。

```
- name: gcloud auth login by workload identity
  run: |-
    gcloud auth login --brief --cred-file="${{ steps.auth_stg.outputs.credentials_file_path || steps.auth_prod.outputs.credentials_file_path }}"
```

みたいな感じにしました。
`auth_stg` と `auth_prod` はどちらかしか実行されないので、片方しか結果が存在しないはずです。
なので `||` でごまかしました。
めんどかったので…。

あとは Secrets からSAのcredentialsをあれこれ取り出してる箇所をバッサリ削除して終わり！

### おまけ 失敗集

最初 `attribute-mapping` に `attribute.repository=assertion.repository` が無くて member に一致しないかなんかで怒られたやつ。

```
ERROR: (gcloud.datastore.indexes.create) There was a problem refreshing your current auth tokens: ('Unable to acquire impersonated credentials: No access token or invalid expiration in response.', '***\n  "error": ***\n    "code": 403,\n    "message": "The caller does not have permission",\n    "status": "PERMISSION_DENIED"\n  ***\n***\n')
```

もーちょっと早いタイミングで怒られてほしさがある…。

あとコピペミスってUIから手動で `assertion.repository=assertion.repository` 相当の設定にして怒られて、あれー？ってなって `assertion.repository="vvakame/til"` 的にハードコーディングしたら通って、設定値間違ってんじゃん！ってなったりもした。
