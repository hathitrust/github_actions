# Build Docker Image
---
#### Purpose
This composite GitHub Action builds a Docker image from a specified revision, tag, or branch, and optionally pushes it to a container registry like GitHub Container Registry (GHCR). It supports features like multi-platform builds, tagging with latest, build caching, and submodule support.

#### Parameters
| Name                | Type    | Description                                                     | Required | Default                                         |
| ------------------- | ------- | --------------------------------------------------------------- | -------- | ----------------------------------------------- |
| `tag`               | string  | Git revision, tag, or branch to build                           | Yes      | `${{ github.event.repository.default_branch }}` |
| `img_tag`           | string  | Custom tag to apply to the built image                          | No       | –                                               |
| `image`             | string  | Full name of the image to build (e.g. `ghcr.io/org/image`)      | Yes      | –                                               |
| `registry`          | string  | Container registry hostname                                     | No       | `ghcr.io`                                       |
| `registry_username` | string  | Username for registry authentication (typically `github.actor`) | No       | `${{ github.actor }}`                           |
| `registry_token`    | string  | Token for registry authentication (typically `github.token`)    | No       | `${{ github.token }}`                           |
| `build-args`        | string  | Build arguments for the Dockerfile                              | No       | –                                               |
| `dockerfile`        | string  | Path to the Dockerfile                                          | No       | `Dockerfile`                                    |
| `push_latest`       | boolean | Whether to push the `latest` tag                                | No       | `false`                                         |
| `submodules`        | boolean | Whether to checkout and include submodules                      | No       | `false`                                         |
| `platforms`         | string  | Platforms to build for (comma-separated)                        | No       | `linux/amd64,linux/arm64`                       |
| `rebuild`           | boolean | Force rebuild even if the image exists                          | No       | `false`                                         |
| `gh_event`          | string  | GitHub event that triggered the workflow                        | No       | –                                               |
| `target`            | string  | Target stage to build from within a multi-stage Dockerfile      | No       | –                                               |
---

#### Outputs
| Name      | Description                                     |
| --------- | ----------------------------------------------- |
| `ghc_img` | Fully qualified Docker image tag pushed to GHCR |

---
#### Logic and Steps
| Step                             | Purpose                                                                                         |
| -------------------------------- | ----------------------------------------------------------------------------------------------- |
| **Set Inputs & Outputs**         | Determines reference (`ref`), push policy, and image tag from either the event or input values. |
| **Clone latest repository**      | Checks out the repository code at the specified `ref`, including submodules if requested.       |
| **Find commit for tag**          | Resolves the tag or revision into a proper commit SHA using a helper action.                    |
| **Log into container registry**  | Authenticates to the container registry using provided credentials.                             |
| **Check if revision exists**     | Checks whether the image already exists in the registry by inspecting its manifest.             |
| **Check whether to push latest** | Determines whether to tag the image as `latest` or `unstable` based on configuration.           |
| **Set up QEMU**                  | Sets up QEMU to support building ARM images on x86 runners.                                     |
| **Set up Docker Buildx**         | Prepares Docker Buildx for building multi-platform images.                                      |
| **Build image and push to GHCR** | Builds the Docker image for specified platforms and tags, then pushes it to the registry.       |
| **Export GHCR.io resource**      | Outputs the full image name and tag that was pushed to the registry.                            |
