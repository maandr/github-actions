# git/changes

Detects all files and directories that contain changes when compared against a base branch. The action uses `git diff` to compare the changes between the current HEAD and the base branch.

## Inputs

| Name              | Required | Type     | Default | Description                                                                                       |
|-------------------|----------|----------|---------|---------------------------------------------------------------------------------------------------|
| `base_branch`     | `no`     | `string` | `main`  | The branch to compare changes against                                                             |
| `include_pattern` | `no`     | `string` | `.*`    | A regular expression defining files to be included                                                |
| `ignore_pattern`  | `no`     | `string` | none    | A regular expression defining files to be excluded, with higher priority than the include_pattern |


## Outputs

| Name            | Type     | Description                                                |
|-----------------|----------|------------------------------------------------------------|
| `changed_files` | `string` | A comma-separated list of files that were changed          |
| `changed_dirs`  | `string` | A comma-separated list of directories that contain changes |


## Usage

```yaml
steps:
  - name: "🔎 Detect directories containing changes"
    id: detect-changes
    uses: ./git/changes
    with:
      base_branch: ${{ github.base_ref }}
      include_pattern: '.*\.(ya?ml|sh)'
      ignore_pattern: '(.github|test)/.*'

  - name: "🖨️ Print changes"
    run: |
      echo "${{ steps.detect-changes.outputs.changed_files }}"
      echo "${{ steps.detect-changes.outputs.changed_dirs }}"
```
