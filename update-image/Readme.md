# Update Image File
---
#### Purpose
This GitHub Action updates a file in a repository (typically a configuration or deployment file in the ht_tanka repo) by replacing its contents with a new Docker image reference. It then commits and pushes the change back to the repository, automating the update process for image deployment configurations.

---
#### Parameters
| Name      | Type   | Description| Required | Default |
| --------- | ------ | ------------------------------------------------ | -------- | ------- |
| `subject` | string | The subject line of the Git commit message       | Yes      | –       |
| `body`    | string | The body (description) of the Git commit message | Yes      | –       |
| `image`   | string | The new Docker image reference to write to file  | Yes      | –       |
| `file`    | string | Path to the file to be updated with the image    | Yes      | –       |

---
#### Steps and Logic
| Step                        | Purpose|
| --------------------------- | ------------------------------------------------------------------------------------------- |
| **Clone latest repository** | Checks out the repository containing the target file.                                       |
| **Dump GitHub event**       | Outputs the full event payload for debugging purposes.                                      |
| **Update the config**       | Writes the new image reference into the specified file, replacing its contents.             |
| **Cat the files**           | Prints the updated file contents to the GitHub Actions log for verification.                |
| **Commit and push files**   | Commits the change using a descriptive message and pushes it back to the origin repository. |
