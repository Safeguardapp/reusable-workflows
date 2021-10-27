# reusable-workflows

This repository is used to store GitHub action workflows that are to be reused in our repositories.

Any workflow which is stored in this repository should be verified by our security officer to ensure that the workflow
can be made publicly accessible.

## How to use

In order to use a reusable workflow from this repository you need to create a workflow in your project that contains the
contents below.

```yaml
name: Example workflow

on:
  pull_request:

jobs:
  check-codestyle:
    uses: safeguardapp/reusable-workflows/.github/workflows/codestyle.yml@main
    with:
      repository_name: ${{ github.repository }}
      ref: ${{ github.ref }}
    secrets:
      token: ${{ secrets.GITHUB_TOKEN }}
      gh_package_registry_token: ${{ secrets.GH_PACKAGE_REGISTRY_PERSONAL_ACCESS_TOKEN }}
```

For more information about reusable workflows head over to
the [documentation](https://docs.github.com/en/actions/learn-github-actions/reusing-workflows).

## Workflow documentation

### codestyle

This workflow requires that the repository using this contains the following packages and commands.

**Packages**

* ESlint
* Prettier

**Commands**

* `npm run lint`
* `npm run prettier:check`

The workflow expects the following in terms of input.

**Expected input:**

* `repository_name` the repository name in the format of `org/repository-name`
* `ref` mandatory git reference to checkout

**Expected secrets:**

* `token` here you should provide the `secrets.GITHUB_TOKEN` from the parent workflow
* `gh_package_registry_token` should contain a personal access token that allows read access to the GitHub package
  registry

### publish_package

This workflow requires that the repository using this contains a `publish:package` command that in turn should at least
call the `npm publish` command.

The workflow expects the following in terms of input.

**Expected input:**

* `repository_name` the repository name in the format of `org/repository-name`
* `ref` optional git reference, defaults to `main`
* `version_increment_type` optional version bump type, if specified should one of the following 'patch | minor | major'

**Expected secrets:**

* `token` here you should provide the `secrets.GITHUB_TOKEN` from the parent workflow
* `gh_package_registry_token` should contain a personal access token that allows read access to the GitHub package
  registry
