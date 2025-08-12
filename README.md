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
            img_tag:
                description: Docker Image Tag
            ref:
                description: Revision or Branch to build
                default: main
            push_latest:
                description: Set True if the build is for the latest version
                type: boolean
                required: false
                default: false
            platforms:
                description: Platforms to build for
                type: choice
                default: linux/amd64,linux/arm64
                options:
                - linux/amd64,linux/arm64
                - linux/amd64
                - linux/arm64
            rebuild:
                description: Rebuild this image?
                type: boolean
                default: false

jobs:
    build-image:
        runs-on: ubuntu-latest
        permissions:
            contents: read
            packages: write

        steps:
            - name: Build Image
              uses: hathitrust/github_actions/build@v1
              with:
                image: ghcr.io/${{ github.repository }}
                dockerfile: Dockerfile
                img_tag: ${{ inputs.img_tag }}
                tag: ${{ inputs.ref }}
                push_latest: ${{ inputs.push_latest}}
                registry_token: ${{ github.token }}
                rebuild: ${{ inputs.rebuild }}
```

Given an input tag `some-branch` which resolves is currently at revision
`451ba479cce63ab5c0e87a5c723e373d920d3406`, this would build that revision,
then push that image to GHCR with tags for
`ghcr.io/organization/package-unstable:some-branch` as well as
`ghcr.io/organization/package-unstable:451ba479cce63ab5c0e87a5c723e373d920d3406`.

---
## [`update-image`](update-image/action.yml)
This GitHub Action updates a file in a repository (typically a configuration or deployment file in the ht_tanka repo) by replacing its contents with a new Docker image reference. It then commits and pushes the change back to the repository, automating the update process for image deployment configurations. This is an action that is used by the `deploy` workflow.

---
## [`tag-release`](tag-release/action.yml)

Typically used in release workflows for applying that version tag to a
"production" version of the image, for example:

```yaml
on:
  release:
    types: [ released ]

jobs:
  tag-release:
    runs-on: ubuntu-latest
    steps:
      - uses: hathitrust/github_actions/tag-release@v1
        with:
          registry_token: ${{ github.token }}
          existing_tag: ghcr.io/${{ github.repository }}:${{ github.sha }}
          image: ghcr.io/${{ github.repository }}
          new_tag: ${{ github.event.release.tag_name }}
```

This workflow will wait for `existing_tag` to exist in the registry; by default
it checks once per minute and waits up to 10 minutes total.

---
## [`deploy`](deploy/action.yml)

This GitHub Action is designed to trigger deployments by updating a target file in the `ht_tanka` configuration repository. It uses a GitHub App to securely authenticate and dispatch an event with the relevant deployment data.

```yaml
on:
    workflow_dispatch:
        inputs:
            branch_hash:
                description: Branch sha or revision to deploy (Defaults to branch sha)
            environments:
                description: The environment to deploy to
                type: choice
                default: testing
                options:
                - testing
                - staging
                - production
jobs:
    deploy:
            runs-on: ubuntu-latest
            permissions:
                contents: read
                packages: write
            steps:
                - name: Deploy to ${{inputs.environments}}
                  uses: hathitrust/github_actions/deploy@v1
                  with:
                    image: ghcr.io/${{ github.repository }}:${{ inputs.branch_hash || github.sha}}
                    file: environments/${{ github.event.repository.name }}/${{inputs.environments}}/web-image.txt
                    CONFIG_REPO_RW_APP_ID: ${{ vars.CONFIG_REPO_RW_APP_ID }}
                    CONFIG_REPO_FULL_NAME: ${{ vars.CONFIG_REPO_FULL_NAME }}
                    CONFIG_REPO_RW_KEY: ${{secrets.CONFIG_REPO_RW_KEY}}
```
---
## [`scan-image`](scan-image/action.yml)
This GitHub Action performs a security scan on a Docker image using Trivy. It checks for OS and library vulnerabilities, outputs a readable report, publishes a summary to the GitHub Actions UI, and optionally comments the results on a pull request.

```yaml
on:
    workflow_dispatch:
        inputs:
            image-ref:
                description: "The name of the image (Default will get this repositories latest image)"
            branch_hash:
                description: Branch sha or revision to deploy (Defaults to branch sha)

jobs:
    image-scan:
        runs-on: ubuntu-latest
        permissions:
            contents: read
            packages: write
            pull-requests: write

        steps:
            - name: Scanning Image ${{ github.repository }}:${{ inputs.branch_hash || github.sha}}
              uses: hathitrust/github_actions/scan-image@v1.7.0 # Will update to v1 after demo and appoval
              with:
                image-ref: ghcr.io/${{ github.repository || inputs.image-ref }}:${{ inputs.branch_hash || github.sha}}
```