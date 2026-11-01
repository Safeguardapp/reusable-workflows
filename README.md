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

The Lint job also runs `npm run build --if-present` after the lint, so a repository whose `build` script runs `tsc`
gets typechecked in the Lint job. Without a `build` script, the step does nothing.

## node_build

This workflow runs the `npm run build` command to verify that the build succeeds, the repository using this workflow 
is required to contain the following packages and commands.

**Commands**

* `npm run build`

## node_test

This workflow runs the `npm run test` command to verify that the tests succeed, the repository using this workflow
is required to contain the following packages and commands.

**Commands**

* `npm run test`
