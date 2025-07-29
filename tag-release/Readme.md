# Docker Tag and Release
---
#### Purpose
This GitHub Action applies a new tag (such as a release version) to an existing Docker image and also updates the latest tag. It is useful in release workflows where you want to reference a specific image by a version tag or latest after confirming the image exists in the registry.

---
#### Parameters
| Name| Type   | Description| Required | Default|
| ------------------- | ------ |------------------- | -------- | --------------------- |
| `registry_username` | string | Username for logging into the container registry (typically `github.actor`)      | No       | `${{ github.actor }}` |
| `registry_token`    | string | Token for logging into the container registry (typically `secrets.GITHUB_TOKEN`) | Yes      | –                     |
| `registry`          | string | The container registry host                                                      | No       | `ghcr.io`             |
| `existing_tag`      | string | Fully qualified reference to the existing image (e.g. `ghcr.io/org/image:sha`)   | No       | `${{ github.sha }}`   |
| `image`             | string | Target image name to apply the new tag to                                        | Yes      | –                     |
| `new_tag`           | string | The new tag to apply to the image (e.g. `v1.0.0`)                                | Yes      | –                     |
| `wait_time`         | number | Time (in seconds) to wait between retries when checking if the image exists      | No       | `60`                  |
| `max_tries`         | number | Maximum number of retries to check for the existing image before giving up       | No       | `10`                  |

---
#### Steps and Logic
| Step | Purpose |
| ------------------------------- | ---------------------------------------------------------------------------------------------------------- |
| **Log into container registry** | Authenticates to the specified container registry using provided credentials.                              |
| **Wait for revision to exist**  | Polls the registry until the specified image with `existing_tag` is available, up to `max_tries` attempts. |
| **Tag image release in GHCR**   | Tags the existing image with both the specified `new_tag` and `latest`, using Docker Buildx imagetools.    |
