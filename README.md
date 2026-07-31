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
        uses: thetillhoff/action-release-prerequisites@v0.4.1
        with:
          RELEASE_TAG: ${{ github.ref_name }}
      ## Optional: Delete version-tag on failure
      - name: Delete version-tag when changelog doesn't contain information about it
        if: failure()
        shell: bash
        env:
          RELEASE_TAG: ${{ github.ref_name }}
        run: |
          git config --global user.name 'GithubActions'
          git config --global user.email 'githubactions@users.noreply.github.com'
          git push --delete origin "$RELEASE_TAG"
```

Note: This step should be added after the `release-prerequisites` step in your workflow, and requires `contents: write` permissions.

## Changelog format

The action looks for a level-2 heading whose text starts with the release tag, and takes everything up to the next level-2 heading as the release body. A dated heading such as `## v1.2.3 on 2024-01-30` matches too, since the tag only has to start it. If no such section exists, or it is empty, the action fails.

## Consuming the release body

Pass it to your own steps through `env`, not by referencing `${{ }}` inside `run`:

```yaml
      - name: Use the release body
        shell: bash
        env:
          RELEASE_BODY: ${{ needs.release-prerequisites.outputs.RELEASE_BODY }}
        run: |
          printf '%s\n' "$RELEASE_BODY"
```

A `${{ }}` reference inside `run` is pasted into the script as literal text before bash parses it, so changelog content ends up interpreted as shell: a backtick or `$( )` executes, and a double quote ends the string and lets whatever follows run as commands.
