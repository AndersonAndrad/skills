---
name: security-review
description: Reviews code changes for authentication, authorization, input validation, injection, secrets, sensitive data, logging, dependency risks, insecure defaults, and abuse scenarios. Use for every change involving user input, APIs, credentials, permissions, persistence, files, external services, or sensitive data.
---

# Security Review

Perform a security review of the complete affected flow, not only the modified
lines.

## Trust boundaries

Identify all data entering the system from:

- HTTP requests
- Message queues
- Webhooks
- Databases
- Files
- Environment variables
- External APIs
- Command-line arguments
- User-controlled configuration

Treat external data as untrusted until validated.

## Input validation

Verify:

- Required fields
- Expected types
- Maximum and minimum lengths
- Allowed formats
- Allowed values
- Numeric boundaries
- Collection size limits
- File size and file type limits
- Unexpected fields
- Encoding and normalization behavior

Do not normalize fields blindly.

Fields such as passwords, tokens, cryptographic values, signatures, hashes,
encoded payloads, identifiers, and user-provided free-form content may be
changed or invalidated by trimming or normalization.

Apply normalization only when the domain explicitly allows it.

## Authentication and authorization

Verify separately:

- Who is the caller?
- Is the caller authenticated?
- Is the caller authorized for this exact resource and operation?
- Can one user access another user's data?
- Can identifiers be manipulated?
- Are administrative operations protected?
- Are authorization checks performed server-side?

Authentication does not imply authorization.

## Sensitive data

Never expose or log:

- Passwords
- Access tokens
- Refresh tokens
- API keys
- Private keys
- Authentication cookies
- Full payment information
- Sensitive personal information
- Secrets from environment variables

Use redaction when logging objects that may contain sensitive fields.

## Injection and unsafe execution

Check for:

- SQL injection
- NoSQL injection
- Command injection
- Path traversal
- Template injection
- Unsafe deserialization
- Server-side request forgery
- Cross-site scripting
- Unvalidated redirects
- Dynamic code execution

Prefer parameterized APIs and allowlists.

## Abuse and resource exhaustion

Consider:

- Repeated requests
- Brute-force attempts
- Oversized payloads
- Expensive queries
- Unbounded pagination
- Infinite retries
- Missing timeouts
- Missing rate limits
- Duplicate processing
- Replay attacks
- Race conditions

## Dependencies

Before adding a dependency:

1. Confirm that existing platform features cannot solve the problem.
2. Prefer actively maintained and widely adopted packages.
3. Avoid packages for trivial functionality.
4. Inspect the dependency's required permissions and transitive impact.
5. Pin versions according to project policy.
6. Run the project's dependency audit when available.

## Security report

Report concrete findings using:

- Risk
- Affected flow
- Exploitation scenario
- Recommended correction

Do not report hypothetical vulnerabilities without explaining how the affected
code makes them possible.