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
      branch_name: 'main'
    secrets:
      token: ${{ secrets.GITHUB_TOKEN }}
```

For more information about reusable workflows head over to
the [documentation](https://docs.github.com/en/actions/learn-github-actions/reusing-workflows).

## Workflow documentation

### publish_package

This workflow needs the following input:

* `repository_name` the repository name in the format of `org/repository-name`
* `branch_name` optional target branch to use, defaults to `main`
* `version_increment_type` optional version bump type, if specified should one of the following 'patch | minor | major'

It needs the following secrets:

* `token` here you should provide the `secrets.GITHUB_TOKEN` from the parent workflow
* `gh_package_registry_token` should contain a personal access token that allows read access to the GitHub package
  registry
