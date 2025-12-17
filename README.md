# Action: Release prerequisites

This action checks whether there's a Changelog file, and retrieves the changes for the tag that triggered the pipeline.

## Example usage

```yaml
name: Release Golang executable on tag

on:
  push:
    tags:
      - 'v[0-9]+.[0-9]+.[0-9]+'

jobs:
  release-prerequisites:
    runs-on: ubuntu-latest
    permissions:
      contents: write # only for the Delete version-tag step
    outputs:
      RELEASE_BODY: ${{ steps.release-prerequisites.outputs.RELEASE_BODY }}
    steps:
      - id: release-prerequisites
        uses: thetillhoff/action-release-prerequisites@v0.1.0
      ## Optional: Delete version-tag on failure
      - name: Delete version-tag when changelog doesn't contain information about it
        if: failure()
        shell: bash
        run: |
          git config --global user.name 'GithubActions'
          git config --global user.email 'githubactions@users.noreply.github.com'
          git push --delete origin ${{ github.ref_name }}
```

Note: This step should be added after the `release-prerequisites` step in your workflow, and requires `contents: write` permissions.
