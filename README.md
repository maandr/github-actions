# GitHub Actions

A compilation of predefined workflows and actions for GitHub Actions.

- The workflows are designed to be [reusable workflows](https://docs.github.com/en/actions/using-workflows/reusing-workflows), which means that they can be used in other projects by referencing them in the `uses` field of a workflow step.
- The actions are designed to be [composite actions](https://docs.github.com/en/actions/creating-actions/creating-a-composite-action), which means that they can be used in other jobs by referencing them in the `uses` field of a step.

## Available Actions

- [curl/check](./curl/check/README.md)
- [git/commit-and-push](./git/commit-and-push/README.md)
- [rsync/upload](./rsync/upload/README.md)
- [ssh/setup](./ssh/setup/README.md)

## Available Workflows

### ci-go

A continuous integration workflow for Go projects that performs tasks such as building, linting, and testing the source code.

#### Usage

```yaml
jobs:
  ci-go:
    uses: maandr/github-actions/.github/workflows/ci-go.yaml@<version>
    with:
      go_version: 1.20.3
```

#### Inputs

| Name              | Description                                | Default                        |
| ----------------- | ------------------------------------------ | ------------------------------ |
| `go_version`      | The go version to use                      | -                              |
| `lint_command`    | The command to run to lint the source code | `staticcheck ./...`            |
| `test_command`    | The command to run to test the source code | `go test -race -vet=off ./...` |
| `build_command`   | The command to build the source code       | `go build -v ./...`            |
| `working_dir`     | The working directory to use (e.g. ./app)  | `./`                           |
| `timeout_minutes` | Duration the workflow is allowed to run    | `10`                           |

### ci-node

A continuous integration workflow for Node.js projects that performs tasks such as building, linting, and testing the source code.

#### Usage

```yaml
jobs:
  ci-node:
    uses: maandr/github-actions/.github/workflows/ci-node.yaml@<version>
    with:
      node_version: 18.x
```

#### Inputs

| Name              | Description                                | Default      |
| ----------------- | ------------------------------------------ | ------------ |
| `node_version`    | The Node.js version to use                 | -            |
| `lint_command`    | The command to run to lint the source code | `pnpm lint`  |
| `test_command`    | The command to run to test the source code | `pnpm test`  |
| `build_command`   | The command to build the source code       | `pnpm build` |
| `working_dir`     | The working directory to use (e.g. ./app)  | `./`         |
| `timeout_minutes` | Duration the workflow is allowed to run    | `10`         |

### ci-docker-image

A continuous integration workflow that builds a docker image from a given dockerfile and docker context.

#### Usage

```yaml
jobs:
  ci-docker-image:
    uses: maandr/github-actions/.github/workflows/ci-docker-image.yaml@<version>
    with:
      file: "./Dockerfile"
      context: "./"
```

#### Inputs

| Name              | Description                             | Default        |
| ----------------- | --------------------------------------- | -------------- |
| `file`            | Path to the Dockerfile to build         | `./Dockerfile` |
| `context`         | Path of the Docker build context        | `./`           |
| `timeout_minutes` | Duration the workflow is allowed to run | `10`           |

### cd-generate-image-tags

A continuous delivery workflow that generates image tags from the GitHub repository meta information. It also produces an image tag that can be sorted using the format `<branch>-<sha>-<timestamp>`.

#### Usage

```yaml
jobs:
  generate-image-tags:
    uses: maandr/github-actions/.github/workflows/cd-generate-image-tags.yaml@<version>
    with:
      image: ghcr.io/${{ github.repository }}
```

#### Inputs

| Name              | Description                                       | Default |
| ----------------- | ------------------------------------------------- | ------- |
| `image`           | The name of the Docker image to generate tags for | -       |
| `timeout_minutes` | Duration the workflow is allowed to run           | `10`    |

#### Outputs

| Name     | Description                          |
| -------- | ------------------------------------ |
| `tags`   | A comma-separated list of the tags   |
| `labels` | A comma-separated list of the labels |

### cd-ghcr

This is a continuous delivery workflow that builds a docker image, tags it and pushes it to the GitHub Container Registry.

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

#### Inputs

| Name              | Description                                               | Default        |
| ----------------- | --------------------------------------------------------- | -------------- |
| `tags`            | The tags to apply to the Docker image (e.g. latest,1.0.0) | -              |
| `labels`          | The labels to apply to the Docker image                   | ""             |
| `file`            | Path to the Dockerfile to build                           | `./Dockerfile` |
| `context`         | Path of the Docker build context                          | `./`           |
| `timeout_minutes` | Duration the workflow is allowed to run                   | `10`           |

#### Secrets

| Name       | Description                                         |
| ---------- | --------------------------------------------------- |
| `GHCR_PAT` | Personal Access Token for GitHub Container Registry |
