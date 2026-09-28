# contact-api config

Private config and deployment for your instance of [contact-api](https://github.com/Unit2795/contact-api). The code lives there; this repo only holds your forms and deploy settings.

## Setup

1. Create your repo from this template with **Use this template**, and make it **private**. Its sites, addresses and deploy logs should stay private. If you're reading this in your own repo, this step is done.
2. Check the AWS [prerequisites](https://github.com/Unit2795/contact-api/blob/main/docs/deploy.md#prerequisites): mainly an SES-verified sender domain with production access.
3. Run the [bootstrap](https://github.com/Unit2795/contact-api/blob/main/docs/deploy.md#2-run-the-bootstrap) in AWS CloudShell with `GitHubRepo=<owner>/<this-repo>`. It prints `RoleArn` and `StateConfig`.
4. Replace the contents of `state.config` with `StateConfig`.
5. Edit `forms.json` with your sites, forms and addresses. See the [config reference](https://github.com/Unit2795/contact-api/blob/main/docs/config.md).
6. Add `RoleArn` as the repository secret `AWS_ROLE_ARN`: **Settings** → **Secrets and variables** → **Actions**, or `gh secret set AWS_ROLE_ARN`.
7. Open a PR with steps 4 and 5, and merge it once it passes. The merge deploys.
8. [Connect a site](https://github.com/Unit2795/contact-api/blob/main/docs/connect.md) and send a real test message.

Until `AWS_ROLE_ARN` exists, pushes to `main` only validate.

| File | Purpose |
| --- | --- |
| `forms.json` | Sites and forms. See the [config reference](https://github.com/Unit2795/contact-api/blob/main/docs/config.md). |
| `state.config` | Terraform state location. The state holds generated secrets; the bucket is private. |
| `.github/workflows/deploy.yml` | Runs the contact-api action at a pinned tag |
| `.github/dependabot.yml` | Opens a PR when contact-api (or another action) ships a new tag |

## Changing config

Edit `forms.json`, then open a PR. CI validates it against the pinned contact-api version without touching AWS. Merging to `main` deploys.

For a **new site**, deploy first; that creates its key. Then set up its proxy as described in [Connect a site](https://github.com/Unit2795/contact-api/blob/main/docs/connect.md). The [values to fetch](https://github.com/Unit2795/contact-api/blob/main/docs/connect.md#values-to-fetch) section says where the origin domain and the site key are stored.

## Upgrading contact-api

Dependabot opens a PR that bumps `Unit2795/contact-api@vX.Y.Z` in `deploy.yml`. The PR shows whether this config is still valid for the new version. A new major version may need config changes, and a release that needs new deploy permissions asks you to run the bootstrap again. Its release notes say which.

## Secrets

`AWS_ROLE_ARN` is the GitHub OIDC deploy role from the bootstrap. Its trust policy allows only this repo's `main` branch.
