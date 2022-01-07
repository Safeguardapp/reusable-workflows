# reusable-workflows

This repository is used to store GitHub action workflows that are to be reused in our repositories.

Any workflow which is stored in this repository should be verified by our security officer to ensure that the workflow
can be made publicly accessible.

## Table of contents
* [How to use](#how-to-use)
* [Workflow documentation](#workflow-documentation)
  + [codestyle](#codestyle)
  + [node_build](#node_build)
  + [node_test](#node_test)
  + [publish_package](#publish_package)

# How to use

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

# Workflow documentation

## codestyle

This workflow can be used to run ESLint and Prettier on a provided project, the repository using this workflow is required to contain 
the following packages and commands.

**Packages**

* ESlint
* Prettier

**Commands**

* `npm run lint`
* `npm run prettier:check`

The workflow expects the following input.

**Expected secrets:**

* `token` here you should provide the `secrets.GITHUB_TOKEN` from the parent workflow
* `gh_package_registry_token` should contain a personal access token that allows read access to the GitHub package
  registry

## node_build

This workflow runs the `npm run build` command to verify that the build succeeds, the repository using this workflow 
is required to contain the following packages and commands.

**Commands**

* `npm run build`

The workflow expects the following input.

**Expected secrets:**

* `token` here you should provide the `secrets.GITHUB_TOKEN` from the parent workflow
* `gh_package_registry_token` should contain a personal access token that allows read access to the GitHub package
  registry

## node_test

This workflow runs the `npm run test` command to verify that the tests succeed, the repository using this workflow
is required to contain the following packages and commands.

**Commands**

* `npm run test`

The workflow expects the following input.

**Expected secrets:**

* `token` here you should provide the `secrets.GITHUB_TOKEN` from the parent workflow
* `gh_package_registry_token` should contain a personal access token that allows read access to the GitHub package
  registry


## publish_package

This workflow requires that the repository using this contains a `publish:package` command that in turn should at least
call the `npm publish` command.

The workflow expects the following input.

**Expected input:**

* `branch_name` optional git reference, defaults to `main`
* `version_increment_type` optional version bump type, if specified should one of the following 'patch | minor | major'

**Expected secrets:**

* `token` here you should provide the `secrets.GITHUB_TOKEN` from the parent workflow
* `gh_package_registry_token` should contain a personal access token that allows read access to the GitHub package
  registry
