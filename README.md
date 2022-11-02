# github-actions
Shared utility actions for HathiTrust GitHub repositories.

See [.github/workflows/test.yaml](.github/workflows/test.yaml) for minimalistic
usage examples.

See individual actions for documentation on their parameters.

See [test/README.md](test/README.md) for examples of how to test and run
actions locally with [act](https://github.com/nektos/act).

## [`validate-tag`](validate-tag/action.yml)

Uses `git rev-parse` to verify and expand a potentially ambiguous Git tag into
something usable by other actions. Used by the `build` action.

## [`build`](build/action.yml)

Builds a Docker image for an arbitrary revision, branch, or GitHub repository
tag and pushes it to a repository. Uses the `validate-tag` action above to
resolve the tag to a particular commit to check out and build.

For example, an action that allows building an image for an arbitrary tag,
branch, or revision and pushing that image might be:

```yaml
on:
  workflow_dispatch:
    inputs:
      tag:
        required: true

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - name: Clone latest repository
        uses: actions/checkout@v3
      - uses: ./build
        with:
          tag: ${{ github.event.inputs.tag }}
          image: ghcr.io/organization/package-unstable
          registry_token: ${{ secrets.GITHUB_TOKEN }}
```

Given an input tag `some-branch` which resolves is currently at revision
`451ba479cce63ab5c0e87a5c723e373d920d3406`, this would build that revision,
then push that image to GHCR with tags for
`ghcr.io/organization/package-unstable:some-branch` as well as
`ghcr.io/organization/package-unstable:451ba479cce63ab5c0e87a5c723e373d920d3406`.

## [`tag-release`](tag-release/action.yml)

Typically used in release workflows for applying that version tag to a
"production" version of the image, for example:

```yaml
on:
  release:
    types: [ released ]

jobs:
  tag_release:
    runs-on: ubuntu-latest
    steps:
      - uses: hathitrust/github-actions/tag-release@v1
        with:
          registry_token: ${{ secrets.GITHUB_TOKEN }}
          existing_tag: ghcr.io/organization/package-unstable:${{ github.sha }}
          image: ghcr.io/organization/package
          new_tag: ${{ github.event.release.tag_name }}
```

This workflow will wait for `existing_tag` to exist in the registry; by default
it checks once per minute and waits up to 10 minutes total.
