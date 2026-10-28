# Deploy to Light Cloud

Deploy an app on [Light Cloud](https://light-cloud.com) from a GitHub Actions workflow. The step follows the build and fails the job if the deployment fails.

Apps connected to a GitHub repository already deploy on every push without this action. Use it when the deploy should wait for your tests, run on a schedule or by hand, or ship the checked-out folder (`upload: true`) instead of the repository.

## Usage

```yaml
# .github/workflows/deploy.yml
name: Deploy
on:
  push:
    branches: [main]

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: light-cloud-com/deploy-action@v1
        with:
          api-key: ${{ secrets.LIGHT_CLOUD_API_KEY }}
          app: my-app
```

## Inputs

| Input | Required | Default | What it does |
| --- | --- | --- | --- |
| `api-key` | yes | | Light Cloud API key. Store it as a repository secret. |
| `app` | no | | App name or id. Leave empty when the repository has a `.lightcloud` file (`lc init` writes one). |
| `environment` | no | production | Environment to deploy. |
| `upload` | no | `false` | `true` uploads the checked-out folder as the source instead of deploying from the connected Git repository. |
| `working-directory` | no | `.` | Folder to run in, and to upload with `upload: true`. |
| `cli-version` | no | `latest` | Version of [`@light-cloud/cli`](https://www.npmjs.com/package/@light-cloud/cli) to run. |

## The API key

In the [console](https://console.light-cloud.com): Organisation settings -> API keys -> create a key. API keys are available on paid plans. Then in GitHub: repository Settings -> Secrets and variables -> Actions -> New repository secret, named `LIGHT_CLOUD_API_KEY`.

A key belongs to one workspace, so the action needs no workspace setting.

## How it works

The action runs `lc deploy` from the Light Cloud CLI with the key. Exit codes from the CLI fail the step: `3` not signed in (bad key), `4` app not found, `5` the deployment failed. Docs: [CLI](https://docs.light-cloud.com/deploy-with-ai/cli).

## License

MIT
