# defectdojo-ci

Reusable GitHub Actions workflow that runs Semgrep, Gitleaks and Trivy on a repository,
uploads the results to a DefectDojo instance, and fails the run while that repository
has active Critical findings.

## Usage

```yaml
name: Security
on:
  push:
    branches: [main]
  pull_request:
jobs:
  security:
    uses: startmatter/defectdojo-ci/.github/workflows/defectdojo.yml@main
    secrets:
      DEFECTDOJO_URL: ${{ secrets.DEFECTDOJO_URL }}
      DEFECTDOJO_TOKEN: ${{ secrets.DEFECTDOJO_TOKEN }}
```

Set two secrets on the calling repository (or at organization level):

| secret | value |
| --- | --- |
| `DEFECTDOJO_URL` | base URL of your DefectDojo instance |
| `DEFECTDOJO_TOKEN` | API token of a DefectDojo service user |

Products are created automatically, named after the repository.

## The gate

The `gate` job fails while the product has active Critical findings that are not marked
False Positive or Risk Accepted. The scan jobs do not depend on it, so a blocked
repository still refreshes its findings — otherwise fixing the code could never clear
the gate.

Unblock by fixing the finding or triaging it in DefectDojo. To disable the gate for a
repository, call the workflow with `with: {gate: false}`.
