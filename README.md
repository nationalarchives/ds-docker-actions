# Docker build GitHub actions

Build, lint and maintain Docker images with consistent labelling.

Used by:

- [ds-etna-frontend](https://github.com/nationalarchives/ds-etna-frontend)
- [ds-etna-search](https://github.com/nationalarchives/ds-etna-search)

## Build a Docker image

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
        uses: nationalarchives/ds-docker-actions/.github/actions/docker-build@main
        with:
          version: 0.1.0
          latest: false
          github-token: ${{ secrets.GITHUB_TOKEN }}
          docker-image-name: my-application
```

## Build a Docker image with a generated version number

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
      - name: Create version tag
        id: version-tag
        uses: nationalarchives/ds-docker-actions/.github/actions/get-version-tag@main
      - name: Build Docker image
        uses: nationalarchives/ds-docker-actions/.github/actions/docker-build@main
        with:
          version: ${{ steps.version-tag.outputs.version-tag }}
          latest: ${{ github.ref == 'refs/heads/main' }}
          github-token: ${{ secrets.GITHUB_TOKEN }}
          docker-image-name: my-application
```

## Remove untagged Docker images

```yml
name: Remove untagged Docker images

on:
  workflow_dispatch:
  schedule:
    - cron: "0 3 * * 1" # 03:00 every Monday

jobs:
  build:
    runs-on: ubuntu-latest
    permissions:
      packages: write
      contents: read
    steps:
      - name: Remove untagged Docker images
        uses: nationalarchives/ds-docker-actions/.github/actions/remove-untagged@main
        with:
          docker-image-name: my-application
          github-token: ${{ secrets.GITHUB_TOKEN }}
```
