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
  LLM Top 10 (LLM01/02/05/06/07/09/10)
- Web testing: OWASP ZAP + manual
  input testing (OWASP Top 10 2025)

## My contributions
- Scoped the penetration test and
  threat model for the chatbot's
  front end (Open WebUI)
- Compiled the compliance and
  organizational requirements for
  the assessment (PIPEDA, ISO/IEC
  27001, NIST CSF)
- Built a STRIDE threat model of
  AI-specific threats, covering
  spoofing, tampering and
  repudiation
- Documented the environment's
  existing security issues as a
  baseline
- Built the test lab: validated the
  initial environment, installed
  testing tools on the VM, deployed
  my own chatbot instance
  (jlib3027/chatbot-ollama), and
  identified deployment issues with
  a plan to resolve them
- Tested stored and reflected XSS
  delivered through model output
  (OWASP LLM05: Improper Output
  Handling). Open WebUI sanitized
  the rendered payloads; not
  exploitable in the tested
  configuration.
- Presented XSS hardening
  recommendations: Content Security
  Policy, context-aware output
  encoding, and sanitized markdown
  rendering
- Contributed to the penetration
  testing and final vulnerability
  assessment report

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

## Automated testing (Oct 2026)
Turned the manual findings into
repeatable promptfoo tests against
Phi-3. Baseline: the model blocked
1 of 7 attacks, and leaked the
secret key in 3 of 4 leakage tests,
twice while refusing. Configs and
results: [promptfoo/](promptfoo/)

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
- Hardened promptfoo run: stricter
  system prompt vs secret removed
  (Nov 2026)
- Follow-on: Microsoft Sentinel
  detection lab
