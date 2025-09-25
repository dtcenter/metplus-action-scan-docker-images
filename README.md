# METplus Docker Security Scan Action

A GitHub Action that installs Syft and Grype, then scans Docker images for security vulnerabilities.
This action provides flexible configuration options for failing builds based on vulnerability severity levels.

## Description

This composite action:
1. Installs Syft and Grype security scanning tools
2. Scans a list of Docker images for vulnerabilities
3. Generates detailed vulnerability reports
4. Optionally fails the build based on Critical and/or High severity CVEs
5. Uploads scan logs as artifacts for review

## Inputs

| Input | Description | Required | Default |
|-------|-------------|----------|---------|
| `images` | JSON array of Docker images to scan (e.g., `["image1:tag", "image2:tag"]`) | Yes | - |
| `continue-on-error` | Whether to continue on scan errors | No | `true` |
| `fail-on-critical` | Whether to fail the action if any Critical CVEs are found | No | `false` |
| `fail-on-high` | Whether to fail the action if any High CVEs are found | No | `false` |
| `upload-logs` | Whether to upload scan logs as artifacts | No | `true` |
| `log-artifact-name` | Name for the uploaded log artifact | No | `logs` |

## Examples

### Minimum Configuration

This example shows the minimum required configuration, scanning a single Docker image:

```yaml
- name: Scan Docker Images
  uses: dtcenter/metplus-action-scan-docker-images@v1
  with:
    images: |
      [
        "dtcenter/met-base-dev:feature_123",
        "dtcenter/met-base-unit-test-dev:feature_123",
        "dtcenter/met-base-metviewer-dev:feature_123",
      ]
```

### Fail on Critical CVEs

This example configures the action to fail if any Critical CVEs are found:

```yaml
- name: Scan Docker Images
  uses: dtcenter/metplus-action-scan-docker-images@v1
  with:
    images: |
      [
        "dtcenter/met-base:3.4.2",
        "dtcenter/met-base-unit-test:3.4.2",
        "dtcenter/met-base-metviewer:3.4.2"
      ]
    fail-on-critical: "true"
    fail-on-high: "false"
```

## Output

The action will:
- Display a summary of CVEs found for each image (Critical, High, Medium, Low, Negligible counts)
- Show detailed information about Critical and High CVEs when found
- Upload scan logs as GitHub Actions artifacts (if requested)
- Fail the workflow if configured severity thresholds are exceeded (if requested)

## Usage in Workflow

Typically, you would use this action after building your Docker images:

```yaml
jobs:
  build-and-scan:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      
      - name: Build Docker Images
        run: |
          docker build -t my-app:${{ github.sha }} .
          
      - name: Scan Docker Images
        uses: dtcenter/metplus-action-scan-docker-images@v1
        with:
          images: |
            [
              "my-app:${{ github.sha }}"
            ]
```
