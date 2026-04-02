---
inclusion: always
---

# Security & Coding Standards

## XSS Prevention

- Never use `innerHTML` or template literals to inject external/API data into the DOM
- Always use `textContent` or `document.createElement()` for dynamic content
- Validate and sanitize ALL external data before rendering (API responses, GeoJSON, etc.)

## API Security

- Never expose API keys or secrets in client-side code
- Validate all API responses before rendering — use a typed validation function
- Use server-side proxies (CloudFront/API Gateway) to hide real API endpoints
- Implement timeouts on all fetch calls (`AbortController`)

## Input Validation Patterns

```ts
// Country codes
/^[A-Z]{2}$/.test(code)

// Safe integer counts
typeof n === 'number' && Number.isInteger(n) && n >= 0 && n < 10_000_000
```

## DOM Manipulation Rules

```ts
// ✅ Safe
el.textContent = userValue;
const span = document.createElement('span');
span.textContent = userValue;

// ❌ Never
el.innerHTML = userValue;
el.innerHTML = `<span>${userValue}</span>`;
```

## Error Handling

- Never expose stack traces or internal error details to the client
- Use `console.warn()` for non-critical failures, not `console.error()` with full errors
- Always provide a graceful fallback (e.g. demo data) when an API call fails

## Dependency Management

- Run `npm audit` before every deploy
- Keep dependencies updated monthly
- Review GitHub security advisories for all direct dependencies

## Checklist for New Features

Before shipping any new feature:

- [ ] No hardcoded secrets or API keys
- [ ] All external data is validated before use
- [ ] No `innerHTML` or unsafe DOM manipulation
- [ ] API endpoints use authentication where needed
- [ ] Errors are handled gracefully without leaking internals
- [ ] External URLs are validated before use
