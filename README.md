# GitHub Actions

A compilation of predefined workflows and actions for GitHub Actions. 

* The workflows are designed to be [reusable workflows](https://docs.github.com/en/actions/using-workflows/reusing-workflows), which means that they can be used in other projects by referencing them in the `uses` field of a workflow step.  
* The actions are designed to be [composite actions](https://docs.github.com/en/actions/creating-actions/creating-a-composite-action), which means that they can be used in other jobs by referencing them in the `uses` field of a step.

## Available Actions

### setup-ssh

This action sets up an SSH config on the workflow runner, so SSH commands can be executed in any subsequent steps.

#### Inputs

| Name         | Description                          | Default |
|--------------|--------------------------------------|---------|
| `host`       | Hostname of the remote host.         | -       |
| `username`   | Username of the remote host.         | -       |
| `privateKey` | SSH private key for the remote host. | -       |

#### Usage

```yaml
steps:
  - name: ⚙️ Setup SSH on runner
    id: setup-ssh-on-runner
    uses: maandr/github-actions/.github/actions/setup-ssh@<version>
    with:
      host: ${{ secrets.SSH_DEPLOY_HOST }}
      username: ${{ secrets.SSH_DEPLOY_USERNAME }}
      privateKey: ${{ secrets.SSH_DEPLOY_KEY }}
```

### http-get

This action initiates an HTTP GET request to the designated host and validates the status code of the resulting response.

#### Inputs

| Name         | Description                                       | Default |
|--------------|---------------------------------------------------|---------|
| `host`       | Name of the host to send the request to.          | -       |
| `path`       | Path segment to append to after the request host. | `""`    |
| `protocol`   | The protocol to use for the request.              | `https` |
| `statusCode` | The expected status code of the response.         | `200`   |

#### Usage

```yaml
steps:
  - name: 🧪 GET google.com
    uses: maandr/github-actions/.github/actions/http-get@main
    with:
      host: google.com
```

### rsync-upload

Transfers a file or directory from the GitHub runner to a remote host using rsync for uploading.

#### Inputs

| Name           | Description                                                         | Default |
|----------------|---------------------------------------------------------------------|---------|
| `host`         | Hostname of the remote host.                                        | -       |
| `username`     | Username of the remote host.                                        | -       |
| `privateKey`   | SSH private key for the remote host.                                | -       |
| `source`       | The file or directory that should be uploaded.                      | -       |
| `destination`  | The path to which the upload should be directed at the remote host. | -       |
| `rsyncOptions` | Arguments to pass to the rsync command.                             | `-avz`  |

#### Usage

```yaml
steps:
  - name: ⏫ Upload files
    uses: maandr/github-actions/.github/actions/rsync-upload@main
    with:
      host: ${{ secrets.SSH_DEPLOY_HOST }}
      username: ${{ secrets.SSH_DEPLOY_USERNAME }}
      privateKey: ${{ secrets.SSH_DEPLOY_KEY }}
      source: './dist'
      destination: '/var/www/html'
```

## Available Workflows

### ci-go

A continuous integration workflow for Node.js that performs tasks such as building, linting, and testing the source code.

#### Inputs

| Name                | Description                                 | Default                        |
|---------------------|---------------------------------------------|--------------------------------|
| `go-version`        | The go version to use.                      | `1.20.3`                       |
| `lint-command`      | The command to run to lint the source code. | `staticcheck ./...`            |
| `test-command`      | The command to run to test the source code. | `go test -race -vet=off ./...` |
| `build-command`     | The command to build the source code.       | `go build -v ./...`            |
| `working-directory` | The working directory to use (e.g. ./app).  | `./`                           |

#### Usage

```yaml
jobs:
  ci-node:
    uses: maandr/github-actions/.github/workflows/ci-node.yaml@<version>
    with:
      nodeVersion: '18.x'
```

### ci-node

A continuous integration workflow for Node.js that performs tasks such as building, linting, and testing the source code.

#### Inputs

| Name                | Description                                 | Default      |
|---------------------|---------------------------------------------|--------------|
| `nodeVersion`       | The Node.js version to use.                 | `18.x`       |
| `lintCommand`       | The command to run to lint the source code. | `pnpm lint`  |
| `testCommand`       | The command to run to test the source code. | `pnpm test`  |
| `buildCommand`      | The command to build the source code.       | `pnpm build` |
| `working-directory` | The working directory to use (e.g. ./app).  | `./`         |

#### Usage

```yaml
jobs:
  ci-go:
    uses: maandr/github-actions/.github/workflows/ci-go.yaml@<version>
    with:
      go-version: 1.20.3
      lint-command: staticcheck ./...
      test-command: go test -race -vet=off ./...
      build-command: go build -v ./...
      working-directory: ./tests/node/
```

### ci-docker-image

A continuous integration workflow that builds a docker image from a given dockerfile and docker context.

#### Inputs

| Name      | Description                       | Default        |
|-----------|-----------------------------------|----------------|
| `file`    | Path to the Dockerfile to build.  | `./Dockerfile` |
| `context` | Path of the Docker build context. | `./`           |

#### Usage

```yaml
jobs:
  ci-docker-image:
    uses: maandr/github-actions/.github/workflows/ci-docker-image.yaml@<version>
    with:
      file: './Dockerfile'
      context: './'
```

### cd-image-tags

A continuous delivery workflow that generates image tags from the GitHub repository meta information. It also produces an image tag that can be sorted using the format `<branch>-<sha>-<timestamp>`.

#### Inputs

| Name    | Description                                        | Default |
|---------|----------------------------------------------------|---------|
| `image` | The name of the Docker image to generate tags for. | -       |

#### Outputs

| Name     | Description                           |
|----------|---------------------------------------|
| `tags`   | A comma-separated list of the tags.   |
| `labels` | A comma-separated list of the labels. |

#### Usage

```yaml
jobs:
  generate-image-tags:
    uses: maandr/github-actions/.github/workflows/cd-image-tags.yaml@<version>
    with:
      image: ghcr.io/${{ github.repository }}
```

### cd-ghcr

This is a continuous delivery workflow that builds a docker image, tags it and pushes it to the GitHub Container Registry.

#### Inputs

| Name      | Description                                                | Default        |
|-----------|------------------------------------------------------------|----------------|
| `tags`    | The tags to apply to the Docker image. (e.g. latest,1.0.0) | -              |
| `labels`  | The labels to apply to the Docker image.                   | ""             |
| `file`    | Path to the Dockerfile to build.                           | `./Dockerfile` |
| `context` | Path of the Docker build context.                          | `./`           |

#### Secrets

| Name       | Description                                          |
|------------|------------------------------------------------------|
| `GHCR_PAT` | Personal Access Token for GitHub Container Registry. |

#### Usage

```yaml
jobs:
  generate-image-tags:
    uses: maandr/github-actions/.github/workflows/cd-image-tags.yaml@<version>
    with:
      image: ghcr.io/${{ github.repository }}

  build-and-push-to-ghcr:
    needs: generate-image-tags
    uses: maandr/github-actions/.github/workflows/cd-ghcr.yaml@<version>
    with:
      tags: ${{ needs.generate-image-tags.outputs.tags }}
      labels: ${{ needs.generate-image-tags.outputs.labels }}
    secrets:
      GHCR_PAT: ${{ secrets.CONTAINER_REGISTRY_PAT }}
```
