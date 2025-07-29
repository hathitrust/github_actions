# Deploy Docker Image
---
#### Purpose

This GitHub Action is designed to trigger deployments by updating a target file in the `ht_tanka` configuration repository. It uses a GitHub App to securely authenticate and dispatch an event with the relevant deployment data.

---

#### Parameters
| Name  | Type   | Description | Required | Default |
| ------------ | ------ | ----------------- | -------- | ------- |
| `environment` | string | GitHub Actions environment context (used for environment-specific logic)               | No       | –       |
| `image`                     | string | Fully qualified container image to deploy (e.g., `ghcr.io/org/service:tag`)            | Yes      | –       |
| `file`                      | string | Path to the file in the target repository that should be updated                       | Yes      | –       |
| `CONFIG_REPO_RW_APP_ID`     | string | GitHub App ID used to authenticate for dispatching the update to the target repository | Yes      | –       |
| `CONFIG_REPO_RW_INSTALL_ID` | string | Installation ID for the GitHub App (optional, inferred if not provided)                | No       | –       |
| `CONFIG_REPO_FULL_NAME`     | string | Full name (owner/repo) of the target configuration repository                          | Yes      | –       |
| `CONFIG_REPO_RW_KEY`        | string | Private key associated with the GitHub App for generating an access token              | Yes      | –       |

---

#### Steps and Logic
| Step                          | Purpose                                                                                                    |
| ----------------------------- | ---------------------------------------------------------------------------------------------------------- |
| **Checkout repository**       | Clones the source repository so the action can reference local files if needed.                            |
| **Set environment variables** | Sets environment variables based on provided inputs or existing environment context.                       |
| **Generate app token**        | Uses the GitHub App ID and private key to generate a token for authenticating with the configuration repo. |
| **Send the message**          | Dispatches an event (`update-image`) to the configuration repository with the image and file path details. |
