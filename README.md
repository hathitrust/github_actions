# github-actions
Shared utility actions for HathiTrust GitHub repositories.

See `.github/workflows/test.yaml` for minimalistic usage examples.

## validate_tag
Uses `git rev-parse` to verify and expand a potentially ambiguous Git tag into
something usable by other actions.
```
    - name: Validate tag
      uses: hathitrust/github_actions/validate_tag
      with:
        tag: ${{ github.event.inputs.tag }}
```
