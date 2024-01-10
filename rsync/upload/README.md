# rsync/upload

Transfers a file or directory from the GitHub runner to a remote host using rsync for uploading.

## Inputs

| Name            | Required | Type     | Default | Description                                                        |
|-----------------| -------- | -------- |---------| -------------------------------------------------------------------|
| `host`          | `yes`    | `string` | -       | Hostname of the remote host                                        |
| `username`      | `yes`    | `string` | -       | Username of the remote host                                        |
| `private_key`   | `yes`    | `string` | -       | SSH private key for the remote host                                |
| `source`        | `yes`    | `string` | -       | The file or directory that should be uploaded                      |
| `destination`   | `yes`    | `string` | -       | The path to which the upload should be directed at the remote host |
| `rsync_options` | `no`     | `string` | `-avz`  | Arguments to pass to the rsync command                             |

## Outputs

None.

## Usage

```yaml
steps:
  - name: "⏫ Upload files"
    uses: maandr/github-actions/rsync/upload@<version>
    with:
      host: ${{ secrets.SSH_DEPLOY_HOST }}
      username: ${{ secrets.SSH_DEPLOY_USERNAME }}
      private_key: ${{ secrets.SSH_DEPLOY_KEY }}
      source: ./dist
      destination: /var/www/html
```
