# backport-github-action

This Github Action will automatically create backport pull request when a pull request is merged, based on the labels it contains.

### Example

By merging a pull request with the labels `backport-to-staging` and `backport-to-production` Backport Github Action (BGA) will automatically create backport pull requests to the branches "staging" and "production".

## How to use

Add the backport workflow to `.github/workflows/backport.yml`:

```yml
name: Backport
on:
  pull_request_target:
    types: [opened, closed, labeled, unlabeled, synchronize]

jobs:
  backport:
    name: Backport Action
    runs-on: ubuntu-latest
    steps:
      - name: Backport
        uses: sqren/backport-github-action@v1
        with:
          # Required
          # Token to authenticate requests
          access_token: ${{ secrets.GITHUB_TOKEN }}

          # Required
          # Backport PR by adding a label
          # Example: PRs labeled with "backport-to-staging" will be backported to "staging"
          backport_label_pattern: '^backport-to-(.*)$'

          # Optional
          # Title for the backport PR
          # Example: [branch-a] My commit msg
          # pr_title: '[{targetBranch}] {commitMessages}'

          # Optional
          # Comma separated list of labels that will be added to the backport PR.
          # target_pr_labels: 'backport'

          # Optional
          # If no labels match the `backport_label_pattern` the backport check will fail. To bypass this for a single PR you can add a label to indicate the PR should not be backported
          # skip_backport_check_label: 'backport:skip'

          # Optional
          # If no labels match the `backport_label_pattern` the backport check will fail. Enabling this will bypass the check for all PRs
          # skip_backport_check: false
```

## Backport CLI (optional)

[Backport CLI](https://github.com/sqren/backport) is a tool that makes it much easier to create ad-hoc backport pull requests. This integrates seemlessly with the Backport Github Action. All you need to do is to add a backport configuration file to the repository root:

```jsonc
// .backportrc.json
{
  "upstream": "<repo name>/<repo owner>",
  "targetBranchChoices": [
    "master",
    "staging",
    "production",
    "<any other branch>"
  ]
}
```
