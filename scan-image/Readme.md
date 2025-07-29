# Run Vulnerability Scan on Docker Image
---

#### Purpose
This GitHub Action performs a security scan on a Docker image using Trivy. It checks for OS and library vulnerabilities, outputs a readable report, publishes a summary to the GitHub Actions UI, and optionally comments the results on a pull request.

---

#### Parameters
| Name        | Type    | Description                                                          | Required | Default |
| ----------- | ------- |------------ | -------- | ------- |
| `image-ref` | string  | Full Docker image reference to scan (e.g. `ghcr.io/org/service:tag`) | Yes      | –       |
| `latest`    | boolean | Skip calling the `setup-trivy` action to install Trivy               | No       | `false` |

---
#### Logic and Steps
| Step| Purpose|
| ---------- | -------------- |
| **Run Trivy vulnerability scanner** | Executes the Trivy scanner on the given image. Outputs results to a text file (`trivy-report.txt`).            |
| **Publish Trivy Output to Summary** | If vulnerabilities are found, adds a collapsible Trivy report section to the GitHub Actions Summary.           |
| **Comment on PR**                   | If the workflow was triggered by a pull request, posts the Trivy report as a comment on the PR for visibility. |
