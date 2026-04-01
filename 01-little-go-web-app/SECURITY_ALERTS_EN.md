# GitHub Security Alerts via Docker Scout SARIF

## How it works

The CI/CD workflow already integrates Docker Scout vulnerability scanning and uploads the results as a SARIF report to GitHub. This enables GitHub's native **Code Scanning Alerts** feature to track and notify you of vulnerabilities — no additional workflow changes required.

### Workflow steps involved

```
Run Docker Scout vulnerability scan
        │
        ▼
  scout-results.sarif
        │
        ▼
Upload Scout SARIF report  (github/codeql-action/upload-sarif@v4)
        │
        ▼
  GitHub Security Tab
```

## No workflow changes needed

Your workflow already contains everything required:

```yaml
- name: Run Docker Scout vulnerability scan
  uses: docker/scout-action@v1.18.0
  with:
    command: cves
    image: ...
    sarif-file: scout-results.sarif

- name: Upload Scout SARIF report
  uses: github/codeql-action/upload-sarif@v4
  if: always()
  with:
    sarif_file: scout-results.sarif
    category: docker-scout
```

## Enabling GitHub Security Alerts

### 1. Enable Code Scanning on your repository

Go to your repository on GitHub:

```
Settings > Advanced Security > Code security > Code scanning
```

Make sure **Code scanning** is enabled.

### 2. Configure email notifications

GitHub will automatically send email notifications when:
- A **new vulnerability** is detected that was not present in the previous scan
- An existing alert changes severity
- An alert is dismissed or resolved

To configure who receives notifications:

```
Settings > Notifications
```

Under **"Code scanning alerts"**, choose:
- **Your activity only** — only when you introduced the vulnerability
- **All activity** — for any new alert on the repository

### 3. Managing alerts in the Security tab

After each workflow run, results appear at:

```
Repository > Security > Code scanning
```

Each alert includes:
- CVE identifier and severity (Critical / High / Medium / Low)
- Affected package and version
- Description and remediation advice
- Link to the NVD / advisory database

## Alert lifecycle

| Event | Notification sent |
|---|---|
| New CVE detected (not in previous scan) | Yes |
| CVE already present in previous scan | No |
| Alert manually dismissed | No |
| Previously dismissed CVE reappears | Yes |

## Team notifications

To notify the whole team, add all collaborators under:

```
Settings > Collaborators and teams
```

Each member can then configure their own notification preferences individually.

## Useful links

- [GitHub Code Scanning documentation](https://docs.github.com/en/code-security/code-scanning)
- [Managing code scanning alerts](https://docs.github.com/en/code-security/code-scanning/managing-code-scanning-alerts)
- [Docker Scout SARIF output](https://docs.docker.com/scout/integrations/ci/github-actions/)
