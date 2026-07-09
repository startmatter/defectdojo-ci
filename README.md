# defectdojo-ci

Reusable GitHub Actions workflow that scans a repository and uploads results to a
DefectDojo instance, failing the run while the repository has active Critical findings.

## Scanners

Always on (filesystem, no setup):

| Scanner | Finds |
| --- | --- |
| Semgrep | insecure code (SAST) |
| Gitleaks | secrets across the whole git history |
| Trivy (fs) | vulnerable dependencies + misconfig |
| Checkov | IaC misconfig — CDK/CloudFormation/k8s/Terraform/Dockerfile |
| Hadolint | Dockerfile security & best practices |

Opt-in (need a target):

| Scanner | Enable with | Needs |
| --- | --- | --- |
| Trivy image | `with: {image: ghcr.io/org/app:tag}` | the runner can pull the image |
| OWASP ZAP (DAST) | `with: {dast_url: https://staging.example.com}` | a reachable **staging** URL |

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
    with:
      runner: self-hosted        # omit for GitHub-hosted runners
      product_type: SportsID     # DefectDojo product type (must match existing)
      # image: ghcr.io/org/app:${{ github.sha }}   # optional image scan
      # dast_url: https://staging.example.com       # optional DAST
    secrets:
      DEFECTDOJO_URL: ${{ secrets.DEFECTDOJO_URL }}
      DEFECTDOJO_TOKEN: ${{ secrets.DEFECTDOJO_TOKEN }}
```

## The gate

`gate` runs after the scanners and fails while the product has active Critical findings
that are not False Positive or Risk Accepted. On a pull request it judges that branch's
own engagement (`PR-<n>`), so a fix turns it green before merge. It only passes on an
explicit "zero criticals" — an unreachable API or a rejected upload fails the run.
Disable per repo with `with: {gate: false}`.
