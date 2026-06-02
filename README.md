# excel.claude.ai OAuth Code Capture PoC

**Security Research PoC - HackerOne acd421**

This repository demonstrates the OAuth redirect_uri validation failure in Anthropic's claude.ai OAuth server.

## Vulnerability

`https://claude.ai/oauth/authorize` accepts ANY redirect_uri without validation:

```
https://claude.ai/oauth/authorize?client_id=966eba67-8b8c-4eae-bbb3-08361d1b9292&redirect_uri=https://excel.claude.ai/callback&response_type=code&scope=user:inference&state=poc
```

The server returns 200 (no `error=invalid_redirect_uri`) for:
- https://excel.claude.ai/callback (this repo)
- https://attacker.example.com/steal
- http://localhost:3000/callback
- ANY arbitrary redirect_uri

## NXDOMAIN Status

`excel.claude.ai` is currently NXDOMAIN - no DNS records.
This repo is ready to serve as the redirect target if/when a CNAME is added.

Nine similar subdomains are also NXDOMAIN:
sheets.claude.ai, docs.claude.ai, slides.claude.ai, word.claude.ai,
office.claude.ai, app.claude.ai, embed.claude.ai, widget.claude.ai

## Attack Chain

1. Attacker claims this GitHub Pages repo with excel.claude.ai custom domain
2. If DNS CNAME is added (or was pre-existing): excel.claude.ai → ACD421.github.io
3. Victim visits: `https://claude.ai/oauth/authorize?...&redirect_uri=https://excel.claude.ai/callback`
4. Victim authorizes (consent page looks legitimate - first-party Anthropic client)
5. Server redirects to excel.claude.ai/callback?code=AUTH_CODE
6. Attacker exchanges code for access_token
7. Full account access

Reported to HackerOne: acd421
