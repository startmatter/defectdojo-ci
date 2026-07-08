# defectdojo-ci

Shared security scanning for sportsid-inc: Semgrep, Gitleaks and Trivy run on every
push to the default branch, results land in DefectDojo (https://ob.startmatter.com),
and a gate fails the run while a repository has active Critical findings.

Add it to a repository:

```yaml
name: Security
on:
  push:
    branches: [main]
  pull_request:
jobs:
  security:
    uses: sportsid-inc/defectdojo-ci/.github/workflows/defectdojo.yml@main
    secrets: inherit
```

Unblocking a repository: fix the finding, or triage it in DefectDojo as False Positive
or Accept Risk. Scanners keep running even while the gate fails, so the next run picks
up the fix. To turn the gate off for one repository, pass `with: {gate: false}`.
