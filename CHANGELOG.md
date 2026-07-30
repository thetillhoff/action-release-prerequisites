# CHANGELOG

## v0.4.0

- Pass values through `env` in the two remaining steps as well. v0.3.2 fixed the display step; the tag check and the body extraction still referenced `${{ }}` expressions inside `run`, which pastes a value into the script before bash parses it
- Match the release tag literally instead of as a regular expression, so the dots in a version no longer act as wildcards
- Read the release body from the configured CHANGELOG_FILE, which the input previously had no effect on - it always read CHANGELOG.md
- Fail with a clear message when the changelog has no section for the tag, instead of producing an empty release body
- Use a random delimiter for the step output, so a changelog line reading EOF can no longer terminate it early
- Stop relying on GNU-specific `head -n -1` behaviour
- Document the changelog heading format, and how to consume the release body without reintroducing the injection

## v0.3.2

- Fix shell injection of backtick characters in release body display step; use env var instead of inline interpolation

## v0.3.1

- Bump `actions/checkout` to v6.0.2 (Node.js 24 compatible), pin to SHA

## v0.3.0

- Add required input for the release tag
- Remove step to delete the version tag when the changelog doesn't contain information about it and add it to readme instead.

## v0.2.0

- Major updates of dependencies

## v0.1.0

Initial release
