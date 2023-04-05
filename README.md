# GitHub Actions

A compilation of predefined workflows for GitHub Actions. The workflows are designed to be [reusable workflows](https://docs.github.com/en/actions/using-workflows/reusing-workflows), which means that they can be used in other projects by referencing them in the `uses` field of a workflow step.

## Available Workflows

| Workflow                                                      | Description                                                                                                                   |
|---------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------|
| [`ci-node`](./.github/workflows/ci-node.yaml)                 | This is a Node.js continuous integration workflow that performs tasks such as building, linting, and testing the application. |
| [`ci-docker-build`](./.github/workflows/ci-docker-build.yaml) | This is a continuous integration workflow that builds a docker image from a given dockerfile and docker context.              |


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
