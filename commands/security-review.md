---
description: Review code for security vulnerabilities (OWASP Top 10)
---

Review the specified code for security vulnerabilities.

# Instructions

1. **Identify the target**: The user will specify a file, directory, or feature to review. If not specified, ask what to review.

2. **Read the code** and analyze against these categories:

### Injection (SQLi, XSS, Command Injection)
- User input used in SQL queries without parameterization
- User input rendered in HTML without escaping
- User input passed to shell commands or `eval()`
- Template injection via user-controlled strings

### Authentication & Session Management
- Hardcoded credentials or API keys
- Weak password policies or missing rate limiting
- Session tokens in URLs or logs
- Missing session expiry or rotation

### Authorization
- Missing access control checks on endpoints
- Insecure direct object references (IDOR)
- Privilege escalation paths
- Missing ownership validation

### Data Exposure
- Sensitive data in logs, error messages, or stack traces
- Secrets in source code or config files committed to git
- PII returned in API responses without need
- Missing encryption for sensitive data at rest

### Security Misconfiguration
- Debug mode enabled in production configs
- Overly permissive CORS settings
- Missing security headers (CSP, HSTS, X-Frame-Options)
- Default credentials or unnecessary features enabled

### Dependency Vulnerabilities
- Known vulnerable packages (check lock files)
- Outdated dependencies with security patches available
- Unnecessary dependencies expanding attack surface

### Input Validation
- Missing validation on file uploads (type, size)
- Path traversal via user-supplied file paths
- Regex denial of service (ReDoS)
- Integer overflow or type confusion

3. **Output format**:

| Severity | Location | Vulnerability | Risk | Remediation |
|----------|----------|--------------|------|-------------|
| Critical | file:line | Description | Impact description | Concrete fix |
| High | file:line | Description | Impact description | Concrete fix |
| Medium | file:line | Description | Impact description | Concrete fix |
| Low | file:line | Description | Impact description | Concrete fix |

**Severity Definitions:**
- **Critical**: Actively exploitable, data breach risk, requires immediate fix
- **High**: Exploitable with some effort, significant impact
- **Medium**: Requires specific conditions, moderate impact
- **Low**: Theoretical risk, defense-in-depth improvement

4. **Summary**: Provide overall security posture assessment and top 3 priorities.

5. **Offer to implement** critical and high-priority fixes directly.

## Notes
- Flag potential issues even if you're not 100% certain - better to over-report
- Consider the deployment context (public internet vs internal tool)
- Check for OWASP Top 10 categories systematically
- Review both the code AND its configuration
