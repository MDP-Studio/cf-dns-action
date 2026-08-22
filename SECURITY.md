# Security Policy

## Supported Versions

Security fixes are applied to the latest `v1` release and the current `main`
branch. Older tags may remain available for reproducibility but are not
maintained separately.

## Reporting a Vulnerability

Report vulnerabilities privately to `meidie@mdpstudio.com.au` with the subject
`Security report: cf-dns-action`.

Include:

- the affected tag or commit
- the affected input, API call, or file when known
- minimal reproduction steps using accounts and domains you are authorised to test
- the expected and actual result
- the likely impact

Remove Cloudflare tokens, Netlify tokens, GitHub tokens, site IDs, zone IDs,
account identifiers, and client-private domain details from reports. Do not open
a public issue containing credentials or an unfixed exploit.

## Security Scope

In scope:

- input validation and command injection in the action runtime
- accidental disclosure of Cloudflare, Netlify, or GitHub credentials
- DNS changes outside the requested zone, record, or dry-run boundary
- unsafe handling of redirects, API errors, retries, or create-only mode
- dependency or bundled `dist/` vulnerabilities that affect action execution

Out of scope:

- denial-of-service, social engineering, or credential testing against GitHub,
  Cloudflare, Netlify, MDP Studio, or third-party accounts
- testing domains, zones, sites, or repositories you do not own or have explicit
  permission to administer
- issues that exist only in upstream services and are not caused or amplified by
  this action

## Security Design

- Cloudflare credentials should be scoped to `Zone -> DNS -> Edit` for the
  intended zone. Do not use the Global API Key.
- Netlify credentials currently have broader account access and should be kept in
  GitHub Actions secrets, limited to trusted workflows, and rotated if exposed.
- `dry-run` must not write to Cloudflare or Netlify.
- `create-only` must fail rather than overwrite a conflicting DNS record.
- Logs, outputs, errors, and test fixtures must not contain tokens or private
  account data.
- The committed `dist/` bundle is part of the released action and must match the
  reviewed source and lockfile.

## Maintainer Verification

Before releasing a security-sensitive change:

```bash
npm ci
npm run all
npm audit
```

Review the generated `dist/` diff, confirm the CI bundle-drift check passes, and
test dry-run and create-only behavior with non-production fixtures before using
real infrastructure credentials.
