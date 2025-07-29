# Validate Tags
---
#### Purpose
This GitHub Action validates and resolves a Git revision tag. It checks whether the given input corresponds to a Git tag or a raw commit SHA and then returns the resolved value. This is useful in workflows where input flexibility (tag or commit) is needed, but a concrete tag or SHA must be used downstream.

---
#### Parameters
##### Inputs
| Name  | Type   | Description| Required | Default |
| ----- | ------ | ------------------------- | -------- | ------- |
| `tag` | string | A Git tag or revision SHA | Yes      | –       |

##### Outputs
| Name  | Description |
| ----- | ----------------------- |
| `tag` | The resolved tag or SHA |

---
#### Steps and Logic
| Step| Purpose|
| --------------------------------- | --------------------------------------------------------------------------------------------------- |
| **Check and expand revision tag** | Resolves whether the input is a Git tag or a commit SHA, then outputs the canonical tag/SHA format. |
