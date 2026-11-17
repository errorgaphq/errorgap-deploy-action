# errorgap-deploy-action

Records a release in an Errorgap project after a deploy, so it shows on
Deploys and the errors that follow are attributed to it. It runs
[`npx @errorgap/node deploy`](https://github.com/errorgaphq/errorgap-node#command-line),
and skips without a key, so one workflow serves environments that report and
environments that don't.

## Usage

```yaml
- uses: errorgaphq/errorgap-deploy-action@v1
  with:
    environment: production
    upload-key: ${{ secrets.ERRORGAP_UPLOAD_KEY }}
    endpoint: https://acme.errorgap.com
    project-slug: my-project
```

The commit and branch default to the ones the workflow runs for. After
[errorgap-setup-action](https://github.com/errorgaphq/errorgap-setup-action),
or in a job whose environment already has `ERRORGAP_ENDPOINT`,
`ERRORGAP_PROJECT_SLUG` and a key, only the environment is needed:

```yaml
- uses: errorgaphq/errorgap-deploy-action@v1
  continue-on-error: true   # a missed record shouldn't fail the deploy
  with:
    environment: ${{ env.DEPLOY_ENV }}
```

## Inputs

| Input | Required | Default | Description |
|---|---|---|---|
| `environment` | yes | | The environment that was deployed (`production`, `staging`, `beta`, ...). |
| `sha` | no | `github.sha` | The commit that was deployed. |
| `version` | no | | A version label for the release, when it isn't the commit. |
| `branch` | no | `github.ref_name` | The branch that was deployed. |
| `upload-key` | no | `ERRORGAP_UPLOAD_KEY` | The project's upload key (a secret). |
| `api-key` | no | `ERRORGAP_API_KEY` | The project's API key, used when there is no upload key. Skips when no key of either kind is set. |
| `endpoint` | no | `ERRORGAP_ENDPOINT` | The Errorgap instance. |
| `project-slug` | no | `ERRORGAP_PROJECT_SLUG` | The project's slug. |
| `cli-version` | no | `0.5.0` | The `@errorgap/node` version that records the release. |

## Outputs

| Output | Description |
|---|---|
| `status` | `recorded` when Errorgap accepted the release, `skipped` when there was no key. |

The step fails when Errorgap rejects the release or can't be reached. Add
`continue-on-error: true` where that shouldn't fail the workflow. Keys are
masked in the job log.

## License

MIT.
