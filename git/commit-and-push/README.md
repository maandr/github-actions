# git/commit-and-push

Commits and pushes the current changes in a git repository. Prior to running this action make sure you checked out a repository and applied the desired changes.

## Inputs

| Name                | Required | Type     | Default              | Description                         |
|---------------------| -------- | -------- |----------------------| ------------------------------------|
| `commit_message`    | `yes`    | `string` | -                    | The message to use for the commit   |
| `commit_user_email` | `no`     | `string` | `noreply@github.com` | The email to use for the commit     |
| `commit_user_name`  | `no`     | `string` | `GitHub Actions`     | The user name to use for the commit |
| `push_options`      | `no`     | `string` | `""`                 | Options to use for the push command |

## Outputs

None.

## Usage

```yaml
steps:
  - name: "⏫ Commit and push changes"
    uses: maandr/github-actions/git/commit-and-push@<version>
    with:
      commit_message: "Commit message"
      commit_user_email: "doe@example.com"
      commit_user_name: "John Doe"
      push_options: "-f"
```
