# Docker build GitHub actions

Build, lint and maintain Docker images with consistent labelling.

## Simple example

```yml
name: Build Docker image

on:
  push:

jobs:
  build:
    runs-on: ubuntu-latest
    permissions:
      packages: write
      contents: read
    steps:
      - uses: actions/checkout@v4

      - name: Build Docker image
        uses: nationalarchives/ds-docker-actions/.github/actions/build@main
        with:
          version: 0.1.0
          latest: false
          github-token: ${{ secrets.GITHUB_TOKEN }}
          docker-image-name: my-application
          wiz-client-id: ${{ secrets.WIZ_CLIENT_ID }}
          wiz-client-secret: ${{ secrets.WIZ_CLIENT_SECRET }}
          wiz-project-id: ${{ secrets.WIZ_PROJECT_ID }}
```

## Build a Docker image with a generated version number

```yml
- name: Create version tag
  id: version-tag
  uses: nationalarchives/ds-docker-actions/.github/actions/get-version-tag@main

- name: Build Docker image
  uses: nationalarchives/ds-docker-actions/.github/actions/build@main
  with:
    version: ${{ steps.version-tag.outputs.version-tag }}
    latest: ${{ github.ref == 'refs/heads/main' }}
    github-token: ${{ secrets.GITHUB_TOKEN }}
    docker-image-name: my-application
    wiz-client-id: ${{ secrets.WIZ_CLIENT_ID }}
    wiz-client-secret: ${{ secrets.WIZ_CLIENT_SECRET }}
    wiz-project-id: ${{ secrets.WIZ_PROJECT_ID }}
```

## Lint a `Dockerfile`

```yml
- name: Lint Dockerfile
  uses: nationalarchives/ds-docker-actions/.github/actions/lint@main
  with:
    dockerfile-path: .
    ignore-linting-rules: DL3045,DL3007
```

## Working with Wiz

### Set up and configure Wiz

```yml
- name: Set up Wiz
  uses: nationalarchives/ds-docker-actions/.github/actions/setup-wiz@main
  with:
    wiz-client-id: ${{ secrets.WIZ_CLIENT_ID }}
    wiz-client-secret: ${{ secrets.WIZ_CLIENT_SECRET }}
```

### Scan a `Dockerfile`

```yml
- name: Scan Dockerfile
  uses: nationalarchives/ds-docker-actions/.github/actions/wiz-scan-dockerfile@main
  with:
    image-id: my-application
    image-tag: 0.1.0
    dockerfile-path: .
    wiz-project-id: ${{ inputs.wiz-project-id }}
```

### Scan a container

```yml
- name: Scan Dockerfile
  uses: nationalarchives/ds-docker-actions/.github/actions/wiz-scan-container@main
  with:
    image-id: my-application
    image-tag: 0.1.0
    wiz-project-id: ${{ inputs.wiz-project-id }}
```
