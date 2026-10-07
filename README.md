# Oryn — showcase

**Oryn is a local assistant prototype built around one idea: the thing that does the work must not be the thing that declares it done.** In the demonstrated workflow you ask a plain-language research question, approve a plan, a worker runs it in an isolated container, and Oryn's own Core checks the result and says plainly what it did and did not check.

This repository is a recruiter-facing showcase (write-up and screenshots only). The product source is private and not published here.

![Oryn home screen: the character, example requests, and an honest refusal for a capability it does not have](screenshots/01-home-honest-refusal.png)

## Architecture
```
Plain-language request (text or voice)
        │
        ▼
  Core  (Bun + TypeScript, WebSocket, AG-UI events)
  ├─ planner      → turns a request into a plan card
  ├─ policy       → classifies each step; free-only model routes by default
  ├─ approval     → plan-approved steps; a plain "yes" does not approve
  ├─ adapters     → replaceable workers: Hermes-in-Docker, web-research, Codex
  └─ verification → probes + receipt, written by Core, never by the worker
        │
        ▼
  App server (React 19)  →  Electron 44 shell (Core and app run as packaged sidecars)
```

Engineering decisions worth noting:
- **Core is the authority, workers are replaceable.** Approval and verification live in Core; workers are adapters, so the product does not depend on one model vendor.
- **Honest verification wording.** Core's reply states the checks it ran (file exists, sources cited, cited pages reachable) and the one it did not (the claims themselves).
- **Safety defaults.** Research steps run in a separate empty workspace; fetches go through a DNS-pinned, SSRF-guarded client; the Watch feature is paused by default; routes are free-only unless the owner allows spend.
- **Failures are shown as failures.** The honest-refusal and "not checked" messages are deliberate design, not copy polish.

Stack: Bun, TypeScript, React 19, Electron, Zod, fast-check, Docker, AG-UI (`@ag-ui/core`), the Vercel AI SDK, and free models through OpenRouter and Gemini. The worker image comes from Nous Research's Hermes.

## Demonstrated outcomes
One workflow was run end to end in a real browser: **a plan-approved research request.** Captures are in `screenshots/`.

| Step | What you see | Screenshot |
|---|---|---|
| **Plan** | A plain research question becomes a one-step plan with cost ("free options only"), what the step can see, and the exact check Core will run afterwards. A plain "yes" does not approve; you tap *Approve plan* or say "approve plan 1". | [02](screenshots/02-plan-awaiting-approval.png) |
| **Isolated run** | After approval, the step runs in an empty Docker workspace with internet access for research, and can be cancelled. | [03](screenshots/03-worker-running.png) |
| **Verified outcome** | Core reports what it checked and states: "Core did not check the claims themselves." | [04](screenshots/04-verified-outcome.png) |
| **Receipt** | Developer Mode shows the plan, task, `findings.md` artifact, cost line and an *Evidence & audit* view. | [05](screenshots/05-developer-mode-receipt.png) |

Separately demonstrated: asked to post to LinkedIn, Oryn says it has no such capability and offers a draft instead of inventing a connector ([01](screenshots/01-home-honest-refusal.png)).

Results from the build's own test runs (2026-10-07; agent-run, self-reported, not an external audit): the research workflow passed its live check 19/19 twice against the real Core, a real planner and a Docker worker on free model routes; character and panel motion checks passed in full, reduced and off motion (15/15, 16/16, 17/17) in real Chrome; a packaged-app smoke test passed 12/12.

![Plan awaiting approval](screenshots/02-plan-awaiting-approval.png)

![Verified outcome with the limits of the check stated](screenshots/04-verified-outcome.png)

## My role, and what agents did
- **Me:** product requirements and direction, the decisions that shape the design (Core as sole authority for approval and verification, free-only routing, owner-gated actions), reviewing outcomes, and accepting or rejecting delegated work.
- **AI agents:** implementation, tests and real-browser automation. Claude Code did most of the implementation and test tooling; Cursor built several UI slices and audits; Copilot wrote tests; Codex/Forge ran independent audits.

I am presenting this as directing and reviewing agent-built software, not as hand-written code.

## Current limitations
- **Core verifies that research ran and returned sources, not that the claims are true.**
- Only the plan-approved research workflow is shown here. Other workflows exist in the prototype but are not demonstrated in this showcase.
- Not yet through my own full acceptance: no clean-machine install and no real-microphone test.
- The Windows installer is unsigned and is not distributed here.
- Agent-orchestration UIs are a crowded space; this is a prototype exploring approval-and-verification.
- Voice is off by default; when turned on it sends audio to a third-party speech service.

## Status
Working prototype, running locally; source private and unpublished. No hosted demo: Oryn's backend runs on my machine and is deliberately not exposed to the internet.

## Art and screenshots
The character and background shown are part of the product's own visuals. Screenshots are provided only for portfolio viewing; no licence to reuse them is granted (see [NOTICE.md](NOTICE.md)). Only the approved character poses appear; candidate poses are not shown.

## Contact
Dilip Chikatla · [LinkedIn](https://www.linkedin.com/in/dilipchikatla/) · [GitHub](https://github.com/dilip-ch-dev)
