---
title: "GitHub Actions + google-github-actions/auth ã§ GCP keyless CI/CD"
emoji: "ðº"
type: "tech" # tech: æè¡è¨äº / idea: ã¢ã¤ãã¢
topics: ["gcp", "githubactions"]
published: true
---

è¿½è¨ 2021/10/08 v0.3.1 ã«ããã¨ aud å¨ãã®è¨­å®å¤æ´ãå¿è¦ [ã½ã¼ã¹](https://github.com/google-github-actions/auth/releases/tag/v0.3.1)
è¿½è¨ 2021/10/07 OIDCãã¼ã¯ã³çºè¡åã®URLãå¤æ´ã«ãªã£ãããã§ã [ã½ã¼ã¹](https://github.com/google-github-actions/auth/pull/30)

è¦ç´ï¼ GitHub Actionsã§CI/CDçãªãã¨ãããã¨ããã¨ããSecretsã¨ãã«GCPã®Service Accountã®Keyã¨ãç½®ããªãã¦ãããã­ã¤ã¨ãã§ããããã«ãªã£ããããã®ã§ãã£ããã§ããã

## çµç·¯

[AWS federation comes to GitHub Actions](https://awsteele.com/blog/2021/09/15/aws-federation-comes-to-github-actions.html) ã¨ããè¨äºãåºã¦ã

[GitHub Actionsã®OIDC id tokenã§GCPã«ã¢ã¯ã»ã¹ãã¦ã¿ã](https://ryotarai.hatenablog.com/entry/github-acitons-id-token-gcp) ã¨ãã£ã¦GCPã§ãããããç¢ºãããäººãåºã¦ã

[Use gcloud with credentials from identity federation with OIDC](https://issuetracker.google.com/issues/187734550) ã«

> For Github, you can check this Github Action: https://github.com/google-github-actions/auth

ã¨ããã¿ã¬ã³ãããã£ãã®ã§ãã£ã¦ã¿ããã¨ã«ããã

ç´°ãããã¨ã¯ãã¾ãæ·±è¿½ãããããã£ããã§ãããã¨ããè¨äºãªã®ã§ç´°ããã¨ãããæ°ã«ãªã£ãäººã¯èªåã§èª¿ã¹ã¦è¨äºæ¸ãã¦ãã®è¨äºã®ã³ã¡ã³ãæ¬ã«æ¸ãã¨ãã¦ãã ããï¼ãããããã­ãããã¾ãï¼èª­ã¿ã¾ãï¼

ãã¨ããã®ãã¡å¬å¼ããã¡ããã¨ãããã­ã¥ã¡ã³ãã¨ããåºã¦ããã¨æãã®ã§ãã£ã¡ãåºãããã£ã¡ãèª­ãã®ãããã¨æãã¾ãã

## ãããããã¨ã®è©³ç´°

### ä»ã¾ã§

GitHub Actionsã§AppEngineã«ããã­ã¤ãããã¨ããService Accountãçºè¡ãã¦JSONå½¢å¼ã®credentialãGitHubã®Secretsã«æããã¦ããã
ä½¿ãã¨ãã¯credentialã `gcloud auth activate-service-account` ã«é£ãããããé©å½ã«ãã¡ã¤ã«ã¨ãã¦ã©ã£ãã«ç½®ãã¦ `GOOGLE_APPLICATION_CREDENTIALS` ã¨ãçµç±ã§ä½¿ã£ãããã¦ããã
ãã®éµã¯éå¸¸éå¸¸ã«å¯¿å½ãé·ãï¼ããã©ã«ãã§ç¡æéï¼ã

ããã®åé¡ç¹ã¨ãã¦ããããªãããã®çç±ã«ããcredentialãæµåºããããããrevokeãã¦Secretsãå¥ãæ¿ããªãã¨ãããªãã£ãã
ã¤ã¾ããéµã®ç®¡çã³ã¹ããããã£ã¦ãããããã¨å±äººåããããã¨ããæã¯ããã

### ãããã

GitHub Actionsã§ OIDC token ãçºè¡ã§ããããã«ãªã£ãã
ãã®æ©è½ãGCPã®[Workload identity federation](https://cloud.google.com/iam/docs/workload-identity-federation)ã¨çµã¿åãããã¨ãåGitHubã®ã¨ãã®workflowãªã®ï¼ããå¯¿å½ãç­ãæ¨©éè²¸ãã¦ãããã­ã
ã¿ãããªæãã§keylessã§æ¨©éãããããããã«ãªã£ãã

ãªãããã®çç±ã«ããcredentialãæµåºãã¦ãããã®éµã¯å¯¿å½ãç­ãã®ã§æ»æèããããä½¿ããæéã¯éããã¦ããã
`google-github-actions/auth` ã®ããã©ã«ãã ã¨1æéï¼3600ç§ï¼ã
ã¤ã¾ããä¸çºè¨­å®ããã°éµã®ç®¡çã³ã¹ããæãå¿è¦ããªããªãã
ããã«ãã»ã­ã¥ãªãã£çã«ãå¤å°ãã·ã«ãªããã¯ãã

## ãããã¨

åºæ¬çã« https://github.com/google-github-actions/auth ã«æ¸ãã¦ãããã¨ãä¸ããé©å½ã«ãã£ã¦ããã ãã
ãã ã¾ããæ¸ãã¦ããéãã«è¿½ã£ã¦ãä½ãæé©ã£ã½ãããèª­ã¿è§£ãã®ãã¡ãã£ã¨ããã©ãã£ãã®ã§ããã§ã¯æé©ãªæ§æã®æé æ¸ãæä¾ãããã¨ããã¢ããã§æ¸ãã¦ããã¾ãã
ããããã®é ç®ãã©ãããæå³åããªã®ãã¯åããµãããã¨ããçè§£ãã¦ãªãã®ã§è©³ããè¨åã¯é¿ãã¾ãã

### ä¸æºå

gcloud sdk ã® version ãææ°ã«ããã
ç­èãè©¦ããæç¹ã§ã¯ `358.0.0` ã§ã¯åãã¦ããã `347.0.0` ã§ã¯ã ãã ã£ãã

### GCPå´ã§ãããã¨

GCPã®Service Accountã®æºåã¨æ¨©éã®è¨­å®èªä½ã¯ãã§ã«åãã¦ãããã®ãããã®ã§å²æã
JSONå½¢å¼ã®éµã§ããã¨ãã¨ç¹ã«å¤ããç®æã¯ããã¾ããã

```
$ export PROJECT_ID=<å¯¾è±¡ã®GCPã®ID>
$ export POOL_NAME=github-actions   # ãå¥½ãã«
$ export PROVIDER_NAME=gha-provider # ãå¥½ãã«
$ export SA_EMAIL=<Service Accountã®ã¡ã¢ã>
$ export GITHUB_REPO=<vvakame/til çãªãã¤>

# IAM Service Account Credentials API ãæå¹ã«ãã
$ gcloud services enable iamcredentials.googleapis.com --project "${PROJECT_ID}"

# Workload Identityã«Poolãä½æãã Poolãä½ãã¯çè§£ãã¦ããªã
$ gcloud iam workload-identity-pools create "${POOL_NAME}" \
    --project="${PROJECT_ID}" --location="global" \
    --display-name="use from GitHub Actions"
$ export WORKLOAD_IDENTITY_POOL_ID=$( \
    gcloud iam workload-identity-pools describe "${POOL_NAME}" \
      --project="${PROJECT_ID}" --location="global" \
      --format="value(name)" \
  )
$ echo $WORKLOAD_IDENTITY_POOL_ID

# Poolã«Provierãä½æãã attribute-mappingã§è²ãéã¹ãã½ã
$ gcloud iam workload-identity-pools providers create-oidc "${PROVIDER_NAME}" \
    --project="${PROJECT_ID}" --location="global" \
    --workload-identity-pool="${POOL_NAME}" \
    --display-name="use from GitHub Actions provider" \
    --attribute-mapping="google.subject=assertion.sub,attribute.repository=assertion.repository,attribute.actor=assertion.actor,attribute.aud=assertion.aud" \
    --issuer-uri="https://token.actions.githubusercontent.com"

# Workload Identityã§SAã«impersonateã§ããããã«ãã
# å¨GitHubãªãã¸ããªã®Workflowããã§ãã¡ããã¨ã¤ããã®ã§å¯è½ãªãªãã¸ããªãçµã
$ gcloud iam service-accounts add-iam-policy-binding "${SA_EMAIL}" \
    --project="${PROJECT_ID}" \
    --role="roles/iam.workloadIdentityUser" \
    --member="principalSet://iam.googleapis.com/${WORKLOAD_IDENTITY_POOL_ID}/attribute.repository/${GITHUB_REPO}"
```

ãã¶ããããä¸çªæ¥½ã§ã¼ã¡ã¼ã¡å®å¨ã ã¨æãã¾ãã
[ãã£ã¡ã®è¨äº](https://ryotarai.hatenablog.com/entry/github-acitons-id-token-gcp)ã ã¨ `sub` ã®å¤ãä½¿ã£ã¦ãããã©ããã®è¨äºã§ã¯ãã­ã¥ã¡ã³ãã«å¾ã£ã¦ `repository` ã®å¤ãä½¿ã£ã¦ããã
[GitHub Token Format](https://github.com/google-github-actions/auth#github-token-format)ãè¦ãã¨ `actor` ã¨ãã§å¶éããããã¨ãã§ãããã ãã©ãã¾ãä»åã¯ `repository` ã§ããããªã

### GitHub Actionså´ã§ãããã¨

GitHub Actionsã® job ã® `permissions` ã« `id-token: write` ãè¿½å ã
ããä»ã¾ã§ `permissions` ããªãã£ãå ´åã[ãã­ã¥ã¡ã³ã](https://docs.github.com/en/actions/security-guides/automatic-token-authentication#permissions-for-the-github_token)ãèª­ãã§è¨­å®ãã¦ãã ããã
æå®ãªãã®å ´åæé»ã®æ¨©éãçµæ§è²ãã¨ã¤ãã¦ãã¦ã `id-token: write` ã ãæ¸ãè¶³ãã¨ããããç¡ããªã£ã¦ã³ã±ããããã¨æãã¾ãã
ã¨ã¯ãããã»ã­ã¥ãªãã£ãæ°ã«ããäººãã¡ãªãã¡ããã¨æç¤ºçã«æå®ãã¦ããå ´åãã»ã¨ãã©ã§ãããã

ãã¨ã¯workflowã®yamlã«é©å½ã«ã¡ããã¡ããstepãè¿½å ãã¾ãã
åããã£ã¦ããæè¡æ¸å¸ã§ã¯ãstgç°å¢ã¨prodç°å¢ããã£ã¦ãä¸å®ã®ã«ã¼ã«ã«å¾ã£ã¦ããã­ã¤åãå¤ãã¦ãã¾ãã
ããã­ã¤åã¯GCPãã­ã¸ã§ã¯ãæ¯ã«ãããã¦ããã®ã§è¥å¹²ããã©ãããã§ãã

```
- id: auth_stg
  if:  ${{ stgç°å¢ã ã¨å¤å®ããæ¡ä»¶ }}
  name: Authenticate to Google Cloud for stg
  uses: google-github-actions/auth@v0.3.1
  with:
    create_credentials_file: 'true'
    workload_identity_provider: projects/XXXX/locations/global/workloadIdentityPools/YYYY/providers/ZZZZ
    service_account: foobar@example.iam.gserviceaccount.com
    access_token_lifetime: 1200s
```

ã¨ããã¦ãåæ§ã« `auth_prod` ã¨ããå®ç¾©ãã¦ããã¾ãã

ãã¨ã¯ä½æããcredentialãgcloudã³ãã³ãã§ä½¿ããããã«ãã¦ããã¾ãã

```
- name: gcloud auth login by workload identity
  run: |-
    gcloud auth login --brief --cred-file="${{ steps.auth_stg.outputs.credentials_file_path || steps.auth_prod.outputs.credentials_file_path }}"
```

ã¿ãããªæãã«ãã¾ããã
`auth_stg` ã¨ `auth_prod` ã¯ã©ã¡ããããå®è¡ãããªãã®ã§ãçæ¹ããçµæãå­å¨ããªãã¯ãã§ãã
ãªã®ã§ `||` ã§ãã¾ããã¾ããã
ããã©ãã£ãã®ã§â¦ã

ãã¨ã¯ Secrets ããSAã®credentialsãããããåãåºãã¦ãç®æããããµãªåé¤ãã¦çµããï¼

### ãã¾ã å¤±æé

æå `attribute-mapping` ã« `attribute.repository=assertion.repository` ãç¡ãã¦ member ã«ä¸è´ããªãããªããã§æããããã¤ã

```
ERROR: (gcloud.datastore.indexes.create) There was a problem refreshing your current auth tokens: ('Unable to acquire impersonated credentials: No access token or invalid expiration in response.', '***\n  "error": ***\n    "code": 403,\n    "message": "The caller does not have permission",\n    "status": "PERMISSION_DENIED"\n  ***\n***\n')
```

ãã¼ã¡ãã£ã¨æ©ãã¿ã¤ãã³ã°ã§æããã¦ã»ãããããâ¦ã

ãã¨ã³ãããã¹ã£ã¦UIããæåã§ `assertion.repository=assertion.repository` ç¸å½ã®è¨­å®ã«ãã¦æããã¦ãããã¼ï¼ã£ã¦ãªã£ã¦ `assertion.repository="vvakame/til"` çã«ãã¼ãã³ã¼ãã£ã³ã°ãããéã£ã¦ãè¨­å®å¤ééã£ã¦ããããï¼ã£ã¦ãªã£ãããããã
