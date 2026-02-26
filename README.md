# Docker build GitHub actions

Build, lint and maintain Docker images with consistent labelling.

## Simple examples

`actions/build` is a one-step action that:

- Lints and scans your Dockerfile
- Builds, scans and tags your Docker image
- Uploads your image to GHCR (GitHub Container Repository)
- Integrates with Wiz for reporting

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
          dockerfile-path: ./Dockerfile
          wiz-client-id: ${{ secrets.WIZ_CLIENT_ID }}
          wiz-client-secret: ${{ secrets.WIZ_CLIENT_SECRET }}
          wiz-project-id: ${{ secrets.WIZ_PROJECT_ID }}
```

### Build a Docker image with a generated version number

`actions/get-version-tag` will generate a simple and safe version tag based on your branch name or relelase/tag.

When used on a `main` branch, it generates a [CalVer](https://calver.org/) version in the format `YY.0M.0D.MICRO` where `MICRO` is a continually-incrementing number based on the run number, for example `26.02.26.1586`.

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
    dockerfile-path: ./Dockerfile
    wiz-client-id: ${{ secrets.WIZ_CLIENT_ID }}
    wiz-client-secret: ${{ secrets.WIZ_CLIENT_SECRET }}
    wiz-project-id: ${{ secrets.WIZ_PROJECT_ID }}
```

## Complex examples (bespoke build processes)

If you need more granular steps than `actions/build`, you can build and scan Docker images yourself using the component actions.

1. Lint Dockerfile: `actions/lint`
1. Scan Dockerfile: `actions/wiz-scan-dockerfile`
1. Build Docker image: [bespoke to your application]
1. Scan Docker image: `actions/wiz-scan-container`
1. Push Docker image: [bespoke to your application]
1. Tag Docker image: `actions/wiz-tag-container`

### Lint a `Dockerfile`

```yml
- name: Lint Dockerfile
  uses: nationalarchives/ds-docker-actions/.github/actions/lint@main
  with:
    dockerfile-path: ./Dockerfile
    ignore-linting-rules: DL3045,DL3007
```

### Set up Wiz

```yml
- name: Install the Wiz CLI
  uses: nationalarchives/ds-docker-actions/.github/actions/wiz-install-cli@main
```

### Scan a `Dockerfile`

```yml
- name: Scan Dockerfile
  uses: nationalarchives/ds-docker-actions/.github/actions/wiz-scan-dockerfile@main
  with:
    image-id: my-application
    image-tag: 0.1.0
    dockerfile-path: ./Dockerfile
    wiz-client-id: ${{ secrets.WIZ_CLIENT_ID }}
    wiz-client-secret: ${{ secrets.WIZ_CLIENT_SECRET }}
    wiz-project-id: ${{ secrets.WIZ_PROJECT_DIGITALSERVICES }}
```

### Scan a container

```yml
- name: Scan container
  uses: nationalarchives/ds-docker-actions/.github/actions/wiz-scan-container@main
  with:
    image-id: my-application
    image-tag: 0.1.0
    dockerfile-path: ./Dockerfile
    wiz-client-id: ${{ secrets.WIZ_CLIENT_ID }}
    wiz-client-secret: ${{ secrets.WIZ_CLIENT_SECRET }}
    wiz-project-id: ${{ secrets.WIZ_PROJECT_DIGITALSERVICES }}
```

### Tag a container

```yml
- name: Scan container
  uses: nationalarchives/ds-docker-actions/.github/actions/wiz-tag-container@main
  with:
    image-id: my-application
    image-tag: 0.1.0
    wiz-client-id: ${{ secrets.WIZ_CLIENT_ID }}
    wiz-client-secret: ${{ secrets.WIZ_CLIENT_SECRET }}
    wiz-project-id: ${{ secrets.WIZ_PROJECT_DIGITALSERVICES }}
```
