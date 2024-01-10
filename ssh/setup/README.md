# ssh/setup

This action sets up an SSH config on the workflow runner, so SSH commands can be executed in any subsequent steps.

## Inputs

| Name          | Required | Type     | Default | Description                         |
|---------------| -------- | -------- |---------| ------------------------------------|
| `host`        | `yes`    | `string` | -       | Hostname of the remote host         |
| `username`    | `yes`    | `string` | -       | Username of the remote host         |
| `private_key` | `yes`    | `string` | -       | SSH private key for the remote host |

## Outputs

None.

## Usage

```yaml
steps:
  - name: "⚙️ Setup SSH on runner"
    uses: maandr/github-actions/ssh/setup@<version>
    with:
      host: ${{ secrets.SSH_DEPLOY_HOST }}
      username: ${{ secrets.SSH_DEPLOY_USERNAME }}
      private_key: ${{ secrets.SSH_DEPLOY_KEY }}
```
