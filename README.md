# GitHub Actions

A compilation of predefined workflows for GitHub Actions. The workflows are designed to be [reusable workflows](https://docs.github.com/en/actions/using-workflows/reusing-workflows), which means that they can be used in other projects by referencing them in the `uses` field of a workflow step.

## Available Workflows

| Workflow                                                      | Description                                                                                                                                                                                      |
|---------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [`ci-node`](./.github/workflows/ci-node.yaml)                 | This is a Node.js continuous integration workflow that performs tasks such as building, linting, and testing the application.                                                                    |
| [`ci-docker-image`](./.github/workflows/ci-docker-image.yaml) | This is a continuous integration workflow that builds a docker image from a given dockerfile and docker context.                                                                                 |
| [`cd-ghcr`](./.github/workflows/cd-ghcr.yaml)                 | This is a continuous delivery workflow that builds a docker image, tags it and pushes it to the GitHub Container Registry.                                                                       |
| [`cd-image-tags`](./.github/workflows/cd-image-tags.yaml)     | This is a continuous delivery workflow that generates image tags from the GitHub repository meta information. It also produces an image tag that can be sorted using the format `<branch>-<sha>-<timestamp>`. |

## Usage

```yaml
jobs:
  ci:
    uses: maandr/github-actions/.github/workflows/<workflow>.yaml@main
    with:
      someInput: "foo"
    secrets:
      someSecret: ${{ secrets.SOME_SECRET }}
```
