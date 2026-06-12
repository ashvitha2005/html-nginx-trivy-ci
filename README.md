# Docker Image Vulnerability Scanning in CI using Trivy and GitHub Actions

## Objective

The objective of this experiment is to automate Docker image vulnerability scanning using Trivy inside a GitHub Actions workflow.

This demonstrates a basic DevSecOps Continuous Integration (CI) pipeline where every push to the repository automatically builds a Docker image and scans it for vulnerabilities.

The workflow follows:

```text
Code Push
     ↓
GitHub Actions
     ↓
Build Docker Image
     ↓
Trivy Scan
     ↓
View Vulnerability Report
```

---

# Tools Used

- Docker
- GitHub Actions
- Trivy
- GitHub Repository
- YAML Workflow
- Nginx
- Alpine Linux

---

# Concepts Covered

- Continuous Integration (CI)
- DevSecOps
- Shift Left Security
- Docker image scanning
- GitHub Actions workflows
- Trivy vulnerability reports
- CVEs
- Automated security checks

---

# Project Structure

```text
.
├── Dockerfile
├── html/
│   └── index.html
├── nginx/
│   └── default.conf
└── .github
    └── workflows
        └── devsecops-ci.yml
```

---

# Dockerfile

```dockerfile
FROM nginx:alpine

COPY html/ /usr/share/nginx/html/
COPY nginx/default.conf /etc/nginx/conf.d/default.conf

EXPOSE 80
```

This image uses Alpine Linux and serves a static HTML page using Nginx.

---

# Nginx Configuration

```nginx
server {
    listen 80;
    server_name localhost;

    server_tokens off;

    root /usr/share/nginx/html;
    index index.html;

    location / {
        try_files $uri $uri/ =404;
    }
}
```

---

# GitHub Actions Workflow

```yaml
name: Docker Build and Trivy Scan

on:
  push:
    branches:
      - main

  pull_request:
    branches:
      - main

jobs:
  docker-trivy-scan:
    runs-on: ubuntu-latest

    steps:

      - name: Checkout repository
        uses: actions/checkout@v4

      - name: Build Docker image
        run: docker build -t html-nginx-trivy-ci-test .

      - name: Run Trivy vulnerability scan
        uses: aquasecurity/trivy-action@master
        with:
          image-ref: html-nginx-trivy-ci-test
          format: table
          exit-code: 0
          severity: HIGH,CRITICAL
```

---

# Workflow Explanation

## Checkout Repository

```yaml
uses: actions/checkout@v4
```

Downloads the repository into the GitHub Actions runner.

---

## Build Docker Image

```yaml
docker build -t html-nginx-trivy-ci-test .
```

Builds the Docker image using the Dockerfile.

---

## Run Trivy Vulnerability Scan

```yaml
uses: aquasecurity/trivy-action@master
```

Trivy scans the image for HIGH and CRITICAL vulnerabilities.

```yaml
severity: HIGH,CRITICAL
```

Only HIGH and CRITICAL vulnerabilities are reported.

```yaml
exit-code: 0
```

The workflow will complete successfully even if vulnerabilities are found.

This allows the vulnerability report to be viewed without failing the pipeline.

---

# CI Pipeline Flow

```text
Developer Push
       ↓
GitHub Repository
       ↓
GitHub Actions
       ↓
Build Docker Image
       ↓
Trivy Scan
       ↓
Generate Vulnerability Report
```

---

# Scan Results

### Target

```text
html-nginx-trivy-ci-test
```

### Base Operating System

```text
Alpine Linux 3.23.4
```

### Vulnerability Summary

```text
Total Vulnerabilities : 1
HIGH                 : 1
CRITICAL             : 0
```

---

# Vulnerable Package

| Library | CVE | Severity | Installed Version | Fixed Version |
|-----------|-----|----------|------------------|---------------|
| libxml2 | CVE-2026-6732 | HIGH | 2.13.9-r0 | 2.13.9-r1 |

---

# Understanding the Report

Trivy detected that the package:

```text
libxml2
```

contained a HIGH severity vulnerability.

The installed version:

```text
2.13.9-r0
```

was vulnerable.

A patched version:

```text
2.13.9-r1
```

was available.

The vulnerability corresponded to:

```text
CVE-2026-6732
```

which could potentially lead to a denial of service when processing specially crafted XML documents.

---

# Why Scan During CI?

Without security scanning:

```text
Code
 ↓
Build
 ↓
Deploy
 ↓
Discover Vulnerabilities Later
```

With DevSecOps:

```text
Code
 ↓
Build
 ↓
Scan
 ↓
Deploy
```

Security issues are identified earlier in the software development lifecycle.

---

# Concepts Learned

- GitHub Actions
- Continuous Integration (CI)
- DevSecOps
- Shift Left Security
- Docker image scanning
- Trivy
- YAML workflows
- CVEs
- Vulnerability severity levels
- Alpine Linux packages
- Automated security checks

---

# Conclusion

This experiment demonstrated how Docker image vulnerability scanning can be integrated into a Continuous Integration pipeline using GitHub Actions and Trivy.

Every push to the repository automatically triggers a workflow that builds the Docker image and scans it for HIGH and CRITICAL vulnerabilities.

The scan detected one HIGH severity vulnerability in the `libxml2` package running on Alpine Linux. Because the workflow was configured with:

```yaml
exit-code: 0
```

the pipeline completed successfully while still generating the vulnerability report.

This experiment illustrates a simple DevSecOps workflow:

```text
Build → Scan → Analyze → Remediate
```

and highlights the importance of integrating security checks early in the CI process.
