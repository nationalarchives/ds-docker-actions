# Docker build GitHub actions

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
        uses: nationalarchives/ds-docker-actions/.github/actions/docker-build
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
    needs:
      - version
    permissions:
      packages: write
      contents: read
    steps:
      - uses: actions/checkout@v4
      - name: Create version tag
        id: version-tag
        uses: nationalarchives/ds-docker-actions/.github/actions/get-version-tag
      - name: Build Docker image
        uses: nationalarchives/ds-docker-actions/.github/actions/docker-build
        with:
          version: ${{ steps.version-tag.outputs.version-tag }}
          latest: ${{ github.ref == 'refs/heads/main' }}
          github-token: ${{ secrets.GITHUB_TOKEN }}
          docker-image-name: my-application
```
