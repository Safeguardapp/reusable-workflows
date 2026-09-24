# reusable-workflows

This repository is used to store GitHub action workflows that are to be reused in our repositories.

Any workflow which is stored in this repository should be verified by our security officer to ensure that the workflow
can be made publicly accessible.

## Table of contents
* [How to use](#how-to-use)
* [Workflow documentation](#workflow-documentation)
  + [codestyle](#codestyle)
  + [jira_handle_code_review](#jira_handle_code_review)
  + [jira_request_code_review](#jira_request_code_review)
  + [merge_develop_into_cycle](#merge_develop_into_cycle)
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
    secrets:
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

* `gh_package_registry_token` should contain a personal access token that allows read access to the GitHub package
  registry

## jira_handle_code_review

This workflow can be used to automatically transition JIRA tickets from `In progress` to `Code Review`, also adds a comment to the ticket with the PR url.

The workflow expects the following input.

**Expected input:**

* `ref` GitHub ref to take the JIRA issue id from

**Expected secrets:**

* `JIRA_BASE_URL` JIRA base url
* `JIRA_USER_EMAIL` JIRA user email
* `JIRA_API_TOKEN` JIRA user API token

## jira_request_code_review

This workflow can be used to automatically approve or reject JIRA tickets that are in `Code Review`, also adds a comment to the ticket that 
states who approved/rejected the PR.

The workflow expects the following input.

**Expected input:**

* `ref` GitHub ref to take the JIRA issue id from

**Expected secrets:**

* `JIRA_BASE_URL` JIRA base url
* `JIRA_USER_EMAIL` JIRA user email
* `JIRA_API_TOKEN` JIRA user API token

## merge_develop_into_cycle

This workflow merges `develop` into every `cycle/*` branch that has not been merged back into `develop` yet, so fixes
and tooling that land on `develop` reach the cycle branch without cherry-picks. It merges, never rebases, so open PRs
on the cycle branch keep their base. When `develop` did not move since the last run nothing is committed. When
`develop` does not merge cleanly the job fails and posts to Slack; merge it by hand as a repository admin
(`git merge origin/develop` on the cycle branch, then push).

The merge queue on the cycle branches squashes, so the workflow pushes the merge itself with a GitHub App token. That
app must be a bypass actor on the "Cycle branches" ruleset. It needs no access to `develop`.

The job runs in the environment `cycle-merge`, and that is where the app's credentials live: the variable
`CYCLE_MERGE_APP_CLIENT_ID` and the secret `CYCLE_MERGE_APP_PRIVATE_KEY`. Restrict the environment's deployment
branches to `develop`. Never store the key as a repository secret: any workflow on any branch can read those, so every
writer could push past the ruleset.

Call it on a schedule from the default branch; scheduled workflows only run there. Pin it to a commit SHA rather than
`@main`: whatever this workflow runs can read the app key, so a change to it should only reach a caller through a
reviewed PR in that caller.

```yaml
name: Merge develop into cycle branches

on:
  schedule:
    - cron: "0 3 * * 1-5"
  workflow_dispatch:

jobs:
  merge:
    uses: safeguardapp/reusable-workflows/.github/workflows/merge_develop_into_cycle.yml@<commit sha>
    secrets:
      slack_webhook: ${{ secrets.SLACK_WEBHOOK }}
```

**Expected secrets:**

* `slack_webhook` optional Slack incoming webhook for failures

### Merging a cycle branch back into develop

The merge queue on `develop` squashes too, which would turn the whole cycle into one commit and cut the link to its
PRs. Land a cycle branch on `develop` the way `acceptance` is merged back: a repository admin merges it with
`git merge --no-ff origin/cycle/<name>` on `develop` and pushes directly, bypassing the queue. The `back-merge` skill
does this with `--source cycle/<name>`. After that the cycle branch is part of `develop` and this workflow skips it.

## node_build

This workflow runs the `npm run build` command to verify that the build succeeds, the repository using this workflow 
is required to contain the following packages and commands.

**Commands**

* `npm run build`

The workflow expects the following input.

**Expected secrets:**

* `gh_package_registry_token` should contain a personal access token that allows read access to the GitHub package
  registry

## node_test

This workflow runs the `npm run test` command to verify that the tests succeed, the repository using this workflow
is required to contain the following packages and commands.

**Commands**

* `npm run test`

The workflow expects the following input.

**Expected secrets:**

* `gh_package_registry_token` should contain a personal access token that allows read access to the GitHub package
  registry


## publish_package

This workflow runs the `npm run publish:package` command to publish the node package to the GitHub packages repository 
, it also ups the version in the repository's `package.json` and it tags the release.

The repository using this workflow is required to contain the following packages and commands.

**Commands**

* `npm run publish:package`

The workflow expects the following input.

**Expected input:**

* `branch_name` optional git reference, defaults to `main`
* `version_increment_type` optional version bump type, if specified should one of the following 'patch | minor | major'

**Expected secrets:**

* `gh_package_registry_token` should contain a personal access token that allows read access to the GitHub package
  registry
