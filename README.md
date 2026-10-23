# LLM Chatbot Security Assessment
OWASP LLM Top 10 analysis of a
self-hosted AI chatbot. Five-person
capstone, Seneca Polytechnic,
Summer 2026. This repo covers my
contributions.

## Stack
- Open WebUI + Ollama + Phi-3,
  in Docker
- My instance:
  jlib3027/chatbot-ollama:latest
- Lab: Ubuntu 24.04 target VM +
  Kali VM

## Approach
- Recon: Nmap service discovery
- LLM testing: Garak, Promptfoo and
  manual prompts, mapped to OWASP
  LLM Top 10 (LLM01/02/05/06/07/
  09/10)
- Web testing: OWASP ZAP + manual
  input testing (OWASP Top 10 2025)

## My contributions
- Built and published my own
  containerized instance of the
  chatbot for independent testing
- Ran manual reflected and stored
  XSS testing against chat input
  and rendered output. Payloads
  were sanitized; not exploitable
  in the tested configuration.
- Presented XSS hardening
  recommendations: Content Security
  Policy, context-aware output
  encoding, and sanitized markdown
  rendering

## Key findings
Confirmed:
- Indirect prompt injection (LLM01):
  an uploaded .txt with embedded
  "system override" text made the
  model ignore the user's request
  and print our canary string.
- System prompt leakage (LLM07):
  asked for its prompt, the model
  refused, then leaked the secret
  build key inside the refusal.
- Unauthenticated model API (A01,
  LLM10): Ollama's API on port
  11434 answered requests from the
  attacker VM with no auth,
  bypassing Open WebUI entirely.
- HTTP security header
  misconfiguration flagged (A02).

Not present in tested config:
- SQL injection, reflected XSS,
  stored XSS. Open WebUI sanitized
  input and rendered output.

## What I'd change now
- Our API finding supplied the
  system prompt in the request
  itself, so it proved exposure,
  not leakage.
- Security headers can't stop curl.
  The fix is binding Ollama to an
  internal network behind an
  authenticating proxy.
- A system prompt isn't a security
  boundary. Keep secrets out of it
  and enforce controls in code:
  output filters, Open WebUI filter
  functions, and treating uploads
  as untrusted.

## What's next
- Automated testing with promptfoo:
  injection and leakage tests run
  before and after hardening
  (in progress, Nov 2026)
- Follow-on: Microsoft Sentinel
  detection lab
