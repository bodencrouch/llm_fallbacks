# Security Policy

## Reporting a Vulnerability

**Please do not report security vulnerabilities through public GitHub issues, discussions, or pull requests.**

This repository has GitHub's private vulnerability reporting enabled. Report vulnerabilities via:

**[Report a vulnerability](https://github.com/bodencrouch/llm_fallbacks/security/advisories/new)**

When reporting, please include:

- A description of the vulnerability and its potential impact
- The component affected (Python library, Docker gateway, Cloudflare Worker edge proxy, chat UI, CI workflows)
- Steps to reproduce, or a proof of concept if available
- Affected version(s) or commit SHA
- Any suggested mitigations, if you have them

You can expect:

- **Acknowledgment within 7 days** of your report
- **An initial assessment within 30 days**, including whether the report is accepted, declined, or needs more information
- Credit in the security advisory once the issue is resolved, unless you prefer to remain anonymous

If the vulnerability is confirmed, a fix will be developed privately and released alongside a published GitHub Security Advisory (CVE requested where appropriate).

## Supported Versions

This project is pre-1.0 (`0.x`) and maintained by a small team. **Only the latest release on PyPI and the `main` branch receive security fixes.** Older versions are not patched; please upgrade to the latest release.

| Version   | Supported |
| --------- | --------- |
| Latest    | ✅        |
| < Latest  | ❌        |

The Python package supports Python 3.10, 3.11, and 3.12.

## Scope

### In scope

- The `llm_fallbacks` Python library (`src/llm_fallbacks/`)
- The self-hosted LiteLLM gateway (`deploy/`), including its Docker image and helper scripts
- The Cloudflare Worker edge proxy (`edge/`), including rate limiting, allowlisting, and Turnstile gating
- The static chat UI (`webui/`, `docs/`) where it handles credentials or untrusted content
- GitHub Actions workflows (`.github/workflows/`) and the PyPI release pipeline
- Secrets handling in any repository script (e.g., `deploy/scripts/sync-github-secrets.sh`)

### Out of scope

- Vulnerabilities in upstream dependencies ([LiteLLM](https://github.com/BerriAI/litellm), [OpenRouter](https://openrouter.ai/), model providers) — report these to the upstream project; if the issue is in how this repo *integrates* them, it is in scope
- The behavior, availability, or output of third-party LLM models themselves
- Self-hosted deployments you operate — you are responsible for TLS, firewalling, and secret storage in your own environment
- Vulnerabilities requiring compromise of the maintainer's GitHub, PyPI, Render, or Cloudflare accounts

## Security Considerations for Users

This project routes traffic to external LLM APIs and handles provider credentials. When deploying or integrating it:

- **Never commit secrets.** Copy `deploy/.env.example` to `deploy/.env` (gitignored) and keep `LITELLM_MASTER_KEY`, `OPENROUTER_API_KEY`, and provider keys out of version control, issues, and logs.
- **Treat `LITELLM_MASTER_KEY` as an operator-only secret.** Never ship it to the browser or the GitHub Pages site. Browser-facing traffic should go through the edge Worker's guest token, not the master key.
- **Local gateway is not production-hardened by default.** The Docker gateway binds to `127.0.0.1:4000` for local development. Before exposing it, add TLS, firewall rules, and proper secret storage (see [`deploy/README.md`](deploy/README.md)).
- **Verify CI changes to configs.** The `configs/` model lists refresh nightly via automation. If you consume these files, pin to a known-good commit or validate them before use.
- **Keep dependencies current.** Both Python (`requirements.txt`, `litellm`) and Node (`edge/`, `webui/`, `runner/`) dependencies should be updated regularly; use `pip-audit`/`npm audit` or GitHub Dependabot alerts.

## Disclosure Policy

- Reports are handled privately until a fix is released.
- Please give us reasonable time to remediate (target: within 90 days) before any public disclosure.
- Coordinated disclosure is appreciated; we will work with you on timing for the advisory publication.
