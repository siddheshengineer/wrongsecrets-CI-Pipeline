# OWASP WrongSecrets with Qodana CI Pipeline

## Overview
This project is based on [OWASP WrongSecrets](https://owasp.org/www-project-wrongsecrets/), a security-focused vulnerable application designed for testing and training purposes related to secrets management. In addition to the base application, this repository includes a **Continuous Integration (CI) pipeline** using **GitHub Actions** to analyze code quality and automatically fix issues where possible using **Qodana** by JetBrains.

## Purpose of the Project
The primary objective of this project is to enhance **code security and quality** by integrating **automated static code analysis** into the development workflow. Secrets management is a critical security concern, and **OWASP WrongSecrets** provides a controlled environment for testing vulnerabilities related to secret leaks and mismanagement. By incorporating **Qodana** into the pipeline, the project ensures:
- **Continuous code quality enforcement** by scanning for vulnerabilities, bad practices, and coding style violations.
- **Automated fixes** where possible to reduce manual intervention and improve security posture.
- **Enhanced developer awareness** by integrating security testing and best practices into the CI/CD workflow.
- **Proactive risk management** to prevent insecure code from reaching production environments.

## How the Project Achieves Its Goals
1. **Security Training & Awareness**
   - OWASP WrongSecrets serves as an educational platform for developers and security teams to learn about secret management vulnerabilities.
   - The environment can be used for internal security training, penetration testing exercises, and awareness programs.

2. **Automated Code Quality Checks**
   - The **GitHub Actions** workflow triggers **Qodana** scans on every pull request and push to key branches.
   - The tool detects security issues, code smells, and other vulnerabilities, providing actionable insights.

3. **Optional Auto-Fixing Issues**
   - Qodana can automatically applies fixes for supported issues and creates pull requests with recommendations for further improvements.
   - This helps maintain high code quality without requiring extensive manual reviews.

4. **Continuous Improvement Through CI/CD**
   - By integrating with GitHub Actions, every code change is evaluated in real time.
   - Developers get instant feedback on security flaws and coding errors before merging changes into production.

## How This Helps an Organization
- **Reduces Security Risks**: Identifies and fixes vulnerabilities in the development phase before they become critical.
- **Improves Development Efficiency**: Automates code reviews, reducing the burden on developers and security teams.
- **Enhances Compliance & Best Practices**: Ensures that security and coding standards are consistently followed.
- **Facilitates Training & Skill Development**: Acts as a practical learning tool for security professionals and developers.
- **Supports DevSecOps Initiatives**: Integrates security testing seamlessly into the software development lifecycle (SDLC).

## GitHub Actions Workflow
The project includes a GitHub Actions workflow that triggers Qodana analysis on the following events:
- **Manually triggered** (`workflow_dispatch`)
- **Pull Requests** to the `master` branch
- **Pushes** to the `master` branch

### Workflow File (`.github/workflows/qodana.yml`)
```yaml
name: Qodana 

on:
  workflow_dispatch:
  pull_request:
    branches: [master]
  push:
    branches:
      - master

jobs:
  qodana:
    runs-on: ubuntu-latest
    permissions:
      contents: write
      pull-requests: write
      checks: write
    steps:
      - uses: actions/checkout@v4
        with:
          fetch-depth: 0
      - name: 'Qodana Scan'
        uses: JetBrains/qodana-action@v2023.3
        with:
          args: --apply-fixes
          pr-mode: false
          push-fixes: pull-request
        env:
          QODANA_TOKEN: ${{ secrets.QODANA_TOKEN }}
```

## Setup Instructions

### 1. Clone the Repository
```sh
git clone https://github.com/your-username/your-repository.git
cd your-repository
```

### 2. Configure Secrets in GitHub
- Go to **Settings** → **Secrets and variables** → **Actions**.
- Add a new secret named `QODANA_TOKEN` with your **Qodana API token**.
- setup `qodana.yaml` with required docker images

### 3. Running the Workflow
- Push code to the `master` branch, or create a pull request to `master` to trigger the Qodana scan.
- Manually trigger the workflow using GitHub Actions.

### 4. Reviewing the Qodana Report
- The CI pipeline integrates with **Qodana Cloud**, allowing for a visualized report of detected issues.
- Developers can review detailed insights on detected issues and suggested fixes.
- The results will be available in the **GitHub Actions logs**.
- If applicable, Qodana will create a pull request with suggested fixes.

Qodana can automatically fix minor issues and create pull requests for review.
This reduces technical debt and speeds up the development cycle.


## Contribution Guidelines
- Follow best practices for **secrets management** and do not commit sensitive information.
- Run Qodana locally before pushing changes to catch issues early.
- Use feature branches for new developments and open pull requests for review.

---

### References
- [OWASP WrongSecrets Documentation](https://owasp.org/www-project-wrongsecrets/)
- [Qodana by JetBrains](https://www.jetbrains.com/qodana/)
- [GitHub Actions Documentation](https://docs.github.com/en/actions)

