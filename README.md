cat > README.md << 'EOF'
# devsecops-api-pipeline

An automated security testing pipeline for a REST API. Every push is scanned for security problems before it can be merged, so vulnerabilities are caught early instead of in production.

## The problem

Most APIs are tested for whether they work, not whether they can be attacked. Common mistakes like missing authentication, one user reading another user's data, injectable inputs, leaked secrets, and vulnerable libraries often go unnoticed until someone exploits them.

## What this project does

It takes a Spring Boot REST API and adds four automated security checks that run in GitHub Actions on every push and pull request:

1. **Secret scanning** finds passwords, API keys, and tokens committed by mistake.
2. **Dependency scanning** finds libraries with known vulnerabilities (CVEs).
3. **Security tests** are custom REST Assured tests that try to attack the API: no token, someone else's data, injection payloads.
4. **Dynamic scan** runs OWASP ZAP against the live API to find runtime vulnerabilities.

If a check fails, the pipeline fails, and the problem is fixed before the code ships.

## How it works

```
push / pull request
        |
        v
  GitHub Actions
        |
        +--> Gitleaks              (secrets in code)
        +--> OWASP Dependency-Check (vulnerable libraries)
        +--> Start API --> REST Assured security tests
        +--> Start API --> OWASP ZAP scan
        |
        v
  Reports uploaded as build artifacts
```

## Tools

| Tool | Purpose |
|---|---|
| Spring Boot | The API being tested |
| REST Assured | Automated security test suite |
| OWASP ZAP | Dynamic API vulnerability scanning |
| OWASP Dependency-Check | Known-CVE detection in dependencies |
| Gitleaks | Secret detection |
| GitHub Actions | Runs everything automatically |

## Project structure

```
devsecops-api-pipeline/
├── .github/workflows/security.yml   # the pipeline
├── api/                             # the target Spring Boot API
├── security-tests/                  # REST Assured security tests
├── reports/                         # sample scan outputs
└── README.md
```

## Security tests covered

Based on the OWASP API Security Top 10:

- Access protected endpoints without a token
- Access another user's resource (broken object level authorization)
- SQL injection in parameters and request bodies
- Mass assignment (sending extra fields in a request)
- No rate limiting on login
- Sensitive data exposed in responses

## Run locally

Start the API:

```bash
cd api
mvn spring-boot:run
```

Run the security tests (in a second terminal):

```bash
cd security-tests
mvn test
```

## Results

| # | Vulnerability | Severity | Found by | Fix |
|---|---|---|---|---|
| 1 | | | | |

Screenshots of the pipeline failing (vulnerable version) and passing (fixed version) go here.

## What I learned

Add a short summary once the project is done.
EOF