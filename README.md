# GitHub Actions

A compilation of predefined workflows and actions for GitHub Actions.

- The workflows are designed to be [reusable workflows](https://docs.github.com/en/actions/using-workflows/reusing-workflows), which means that they can be used in other projects by referencing them in the `uses` field of a workflow step.
- The actions are designed to be [composite actions](https://docs.github.com/en/actions/creating-actions/creating-a-composite-action), which means that they can be used in other jobs by referencing them in the `uses` field of a step.

## Available Actions

- [curl/check](./curl/check/README.md)
- [git/commit-and-push](./git/commit-and-push/README.md)
- [git/changes](./git/changes/README.md)
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

### gh-dependabot-config

A workflow that generates a GitHub Dependabot configuration file and issues a pull request to update the configuration in the repository.

The file is generated by detecting files within the repository, that are specific to a certain package ecosystem. It currently supports the following package ecosystems:
- Docker (Dockerfile)
- GitHub Actions (action.yaml, action.yml in subdirectories)
- Golang (go.mod)
- npm (package.json)

#### Usage

```yaml
permissions:
  contents: write
  pull-requests: write

jobs:
  gh-dependabot-config:
    uses: maandr/github-actions/.github/workflows/gh-dependabot-config.yaml@<version>
    with:
      schedule: weekly
```

#### Inputs

| Name          | Description                                        | Default                   |
|---------------|----------------------------------------------------|---------------------------|
| `output_file` | The file to generate                               | `.github/dependabot.yaml` |
| `schedule`    | The schedule dependabot should use                 | `weekly`                  |
| `base_branch` | The base branch to create the pull request against | `main`                    |

### ci-gitops

A continuous delivery workflow designed for GitOps projects. The workflow is designed for projects that use [Kustomize](https://kustomize.io/) to manage Kubernetes resources. It detects kustomization files in the working directory (`working_dir`) and performs the following checks on each subdirectory that contains a kustomization file:

- **build**: It runs `kustomize build` to validate the kustomization file.
- **validate**: It runs [kubeconform](https://github.com/yannh/kubeconform) to validate all Kubernetes manifests in the directory.
- **lint**: It runs [trivy](https://github.com/aquasecurity/trivy) to scan all Kubernetes manifests in the directory for vulnerabilities.

The workflow utilizes a custom action runner [`ghcr.io/maandr/action-runners/gitops`](https://github.com/maandr/action-runners/pkgs/container/action-runners%2Fgitops) that contains the necessary tools to perform the checks.

#### Usage

```yaml
jobs:
  ci-gitops:
    uses: maandr/github-actions/.github/workflows/ci-gitops@<version>
    with:
      working_dirs: ./example-projects/kubernetes
      ignore_dirs: .github,tests
      kubeconform_ignore_filename_patterns: ".*gotk-.*,.*.sops.yaml"
      kubeconform_skip_resources: "Secret"
      kubernetes_version: 1.27.4
      trivy_severity: "CRITICAL,HIGH"
```

#### Inputs

| Name                                  | Description                                                                                                              | Default                |
|---------------------------------------|--------------------------------------------------------------------------------------------------------------------------|------------------------|
| `working_dir`                         | A comma-separated list of directories to use (e.g. ./prod,./staging)                                                     | -                      |
| `ignore_dirs`                         | A comma-separated list of directory names to ignore (e.g. flux-system)                                                   | none                   |
| `kubeconform_ignore_filename_patterns` | A comma-separated list of regular expression specifying paths that kubeconform will ignore (e.g. .*gotk-.*,.*.sops.yaml) | none                   |
| `kubeconform_skip_resources`          | A comma-separated list of kinds or GVKs kubeconform will ignore (e.g. Secret,ConfigMap)                                  | none                   |
| `kubernetes_version`                  | The Kubernetes version to use (e.g. 1.27.4)                                                                              | -                      |
| `trivy_severity`                      | A comma-separated list of trivy severity levels to use (e.g. CRITICAL,HIGH)                                              | `CRITICAL,HIGH,MEDIUM` |
| `timeout_minutes`                     | Duration the workflow is allowed to run                                                                                  | `10`                   |
