# git/changed-top-level-dirs

Detects all top level directories that contain changes when compared against a base branch. The action uses `git diff` to compare the changes between the current HEAD and the base branch.

## Inputs

| Name           | Required | Type     | Default | Description                                     |
|----------------|----------|----------|---------|-------------------------------------------------|
| `base_branch`  | `no`     | `string` | `main`  | The branch to compare changes against           |
| `ignored_dirs` | `no`     | `string` | `""`    | A comma-separated list of directories to ignore |

## Outputs

| Name           | Type     | Description                                                            |
|----------------|----------|------------------------------------------------------------------------|
| `changed_dirs` | `string` | A json array containing the top level directories that contain changes |


## Usage

```yaml
steps:
  - name: "🔎 Detect top level directories containing changes"
    id: detect-changes
    uses: maandr/github-actions/git/changed-top-level-dirs@<version>
    with:
      base_branch: "main"
      ignored_dirs: ".github,docs"

  - name: "🖨️ Print changed directories"
    run: echo "${{ steps.detect-changes.outputs.changed_dirs }}"
```
