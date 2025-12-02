# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is a GitHub Action for automatically generating semantic version numbers and Alpha release versions. The action reads the current version from `package.json`, auto-increments the patch version, and generates ordered Alpha release numbers (e.g., v1.5.13-alpha.1, v1.5.13-alpha.2). It's implemented as a composite action using bash and Node.js.

## Architecture

The action operates in a single composite step:

1. **Version Reading**: Reads current version from `package.json` using Node.js
2. **Patch Increment**: Auto-increments the patch version (PATCH in SemVer)
3. **Git Tag Search**: Fetches all git tags and counts existing Alpha tags for the next version
4. **Alpha Number Generation**: Generates sequential Alpha number (count + 1)
5. **Output Export**: Exports 4 values via `$GITHUB_OUTPUT` for downstream steps

The workflow validates that `git` and `node` are available in the runner environment.

## Key Files

- `action.yaml`: Main action definition with composite bash script step
- `README.md`: Full documentation including usage examples
- `.github/workflows/test_with_local_actions.yaml`: Tests action locally (push/PR trigger)
- `.github/workflows/test_with_marketplace_actions.yaml`: Tests marketplace version (manual trigger)

## Outputs

The action exports 4 outputs via step ID `version`:

| Output | Description | Example |
|--------|-------------|---------|
| `current_version` | Current version from package.json | `1.5.12` |
| `test_version` | Generated Alpha version | `1.5.13-alpha.1` |
| `alpha_number` | Sequential Alpha number | `1` |
| `build_number` | GitHub Actions run number | `42` |

These are accessed in workflows as `${{ steps.version.outputs.current_version }}`, etc.

## Development

### Testing Locally

Using [act](https://github.com/nektos/act) to simulate GitHub Actions:

```bash
# Install act
brew install act

# Test the local action
act --container-architecture linux/amd64 -W .github/workflows/test_with_local_actions.yaml
```

### Workflow Files

- `test_with_local_actions.yaml`: Runs on every push/PR, tests the action using `uses: ./`
- `test_with_marketplace_actions.yaml`: Manual workflow (workflow_dispatch), tests the published marketplace version

Both workflows verify that all 4 outputs are generated successfully.

## Version Increment Logic

The action follows [Semantic Versioning](https://semver.org/):

- Only the **patch version** (third number) is auto-incremented
- **Alpha sequence numbers** are based on existing git tags matching `v{NEXT_VERSION}-alpha.*`
- Example progression: `v1.5.12` → `v1.5.13-alpha.1` → `v1.5.13-alpha.2` → `v1.5.13`

The git tag search ensures each Alpha number is unique for a given patch version.