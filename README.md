# gha-k8s-namespace

The purpose of this GitHub Action is to automate the creation of ExternalName services to allow for communication with other applications deployed into the main namespace without having to deploy a new copy into the feature branch namespace. 

### Usage

```yaml
steps:
  - name: Deploy K8S Namespace
    if: github.event.created == true
    uses: dmsi-io/gha-k8s-namespace@v1
    with:
      GCP_SA_KEY: ${{ secrets.GCP_SA_KEY }}
      GKE_CLUSTER_NAME: ${{ secrets.GCP_STAGING_CLUSTER_NAME }}
      GCP_ZONE: ${{ secrets.GCP_ZONE }}
      GCP_PROJECT_ID: ${{ secrets.GCP_PROJECT_ID }}
      TLD: ${{ secrets.TOP_LEVEL_DOMAIN }}
```

> It is recommended to leave the if statement on this step to only run this action on creation of new branches. However, this action is not destructive in nature and could be run on every commit.

### Optional inputs

#### Repositories

Sometimes it is important to actually deploy a copy of an application to the feature branch namespace. For our architecture, the primary contender is `graphql-api` for our middleware. This application acts as a gateway to the other middleware and can only pull the schema from applications deployed in the same namespaces. Repos can be supplied for this copy deployment as follows:

```yaml
  with:
    repos: graphql-api

  # multiple dependencies can be supplied as a comma-separated list
  with:
    repos: graphql-api, docs-api
```

#### To Namespace

This value shouldn't be overridden under most circumstances, but is provided on the off chance it is required. Providing this will determine the namespace to deploy this environment to:

Default: `${{ github.ref_name }}`

```yaml
  with:
    TO_NAMESPACE: testing-namespace
```

#### From Namespace

This value shouldn't be overridden under most circumstances, but is provided on the off chance it is required. Providing this will determine the namespace to grab resources from:

Default: `${{ github.event.repository.default_branch }}`

```yaml
  with:
    FROM_NAMESPACE: main
```