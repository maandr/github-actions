# curl/check

This action initiates an HTTP GET request to the designated host and validates the status code of the resulting response.

## Inputs

| Name          | Required | Type     | Default | Description                                         |
|---------------| -------- | -------- |---------| ----------------------------------------------------|
| `host`        | `yes`    | `string` | -       | Name of the host to send the request to             |
| `path`        | `no`     | `string` | `""`    | Path segment to append to after the request host    |
| `protocol`    | `no`     | `string` | `https` | The protocol to use for the request (http or https) |
| `status_code` | `no`     | `int`    | `200`   | The expected status code of the response            |

## Outputs

None.

## Usage

```yaml
steps:
  - name: 🧪 GET google.com
    uses: maandr/github-actions/curl/check@<version>
    with:
      host: google.com
      protocol: https
      status_code: 200
```
