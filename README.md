# gha-k8s-namespace

[![release][release-badge]][release]

The purpose of this GitHub Action is to automate the creation of ExternalName services to allow for communication with other applications deployed into the main namespace without having to deploy a new copy into the feature branch namespace.

## Setup

This action is reliant on a Service Account with the following permissions:

- Kubernetes Engine Admin

Additionally, it is recommended to use Workload Identity Federation. If this is not setup follow the steps here: https://github.com/google-github-actions/auth#setup

## Inputs

| NAME                    | DESCRIPTION                                                                              | TYPE     | REQUIRED | DEFAULT                                         |
| ----------------------- | ---------------------------------------------------------------------------------------- | -------- | -------- | ----------------------------------------------- |
| `GCP_IDENTITY_PROVIDER` | GCP Workload Identity Provider.                                                          | `string` | `true`\* |                                                 |
| `GCP_SERVICE_ACCOUNT`   | GCP Service Account email.                                                               | `string` | `true`\* |                                                 |
| `GCP_SA_KEY`            | GCP Service Account Key (JSON).                                                          | `string` | `true`\* |                                                 |
| `GKE_CLUSTER_NAME`      | Google Kubernetes Engine Cluster name.                                                   | `string` | `true`   |                                                 |
| `GCP_ZONE`              | GCP Zone.                                                                                | `string` | `true`   |                                                 |
| `GCP_PROJECT_ID`        | GCP Project ID.                                                                          | `string` | `true`   |                                                 |
| `TO_NAMESPACE`          | Allows to override the desired TO_NAMESPACE variable.                                    | `string` | `false`  | `${{ github.ref_name }}`                        |
| `FROM_NAMESPACE`        | Allows to override the desired FROM_NAMESPACE variable.                                  | `string` | `false`  | `${{ github.event.repository.default_branch }}` |
| `SERVICE_NAME`          | Allows to override the desired SERVICE_NAME variable.                                    | `string` | `false`  | `${{ github.repository }}`                      |
| `repos`                 | Comma separated list of repositories to instead deploy a copy from the default namespace | `string` | `false`  |                                                 |

> It is recommended to use Workload Identity Federation with the `GCP_IDENTITY_PROVIDER` and `GCP_SERVICE_ACCOUNT` inputs. `GCP_SA_KEY` will still work with `v1` tags.

### Usage

```yaml
name: Kubernetes Namespace Deployment
on:
  push:
    branches:
      - develop
      - 'feature/*'

jobs:
  deploy:
    name: Deploy Kubernetes Namespace
    runs-on: ubuntu-latest

    permissions:
      contents: 'read'
      id-token: 'write'

    steps:
      - name: Deploy K8S Namespace
        if: github.event.created == true && github.ref_type == 'branch'
        uses: dmsi-io/gha-k8s-namespace@main
        with:
          GCP_IDENTITY_PROVIDER: ${{ secrets.GCP_IDENTITY_PROVIDER }}
          GCP_SERVICE_ACCOUNT: ${{ secrets.GCP_SERVICE_ACCOUNT }}
          GKE_CLUSTER_NAME: ${{ secrets.GCP_CLUSTER_NAME }}
          GCP_ZONE: ${{ secrets.GCP_ZONE }}
          GCP_PROJECT_ID: ${{ secrets.GCP_PROJECT_ID }}
```

> Workload Identity Federation requires access to the id-token permission and thus the outlined permissions in the example above are required.

#### With Service Account Credentials JSON

```yaml
name: Kubernetes Deployment
on:
  push:
    branches:
      - develop
      - 'feature/*'

jobs:
  build-deploy:
    name: Build and Deploy Kubernetes
    runs-on: ubuntu-latest

    steps:
      - name: Deploy K8S Namespace
        if: github.event.created == true && github.ref_type == 'branch'
        uses: dmsi-io/gha-k8s-namespace@main
        with:
          GCP_SA_KEY: ${{ secrets.GCP_SA_KEY }}
          GKE_CLUSTER_NAME: ${{ secrets.GCP_CLUSTER_NAME }}
          GCP_ZONE: ${{ secrets.GCP_ZONE }}
          GCP_PROJECT_ID: ${{ secrets.GCP_PROJECT_ID }}
```

<!-- badge links -->

[release]: https://github.com/dmsi-io/gha-k8s-namespace/releases
[release-badge]: https://img.shields.io/github/v/release/dmsi-io/gha-k8s-namespace?style=for-the-badge&logo=github
