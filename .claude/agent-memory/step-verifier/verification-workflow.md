---
name: verification-workflow
description: Concrete commands + checks that actually verify an AIDOS step (Go mirror, front mirror, e2e, docs, Linear)
metadata:
  type: project
---

How to verify an AIDOS step end-to-end without trusting the executor report.

**Why:** the contract requires re-running everything; the report's "5 passed / green" claims must be reproduced.

**How to apply (commands that work in this repo):**
- Go mirror: `cd /data/dev/aidos/back && go test ./kernel/<step>/ -count=1` + `go vet ./kernel/<step>/` + `gofmt -l kernel/<step>/` (empty = clean).
- Prior green intact: `cd /data/dev/aidos/back && go build ./...`.
- Front reproducibility mirror: `cd /data/dev/aidos/front/web && npx vitest run lib/<x>.test.ts`.
- Playwright e2e: `cd /data/dev/aidos && npx playwright test tests/e2e/<x>.spec.ts --reporter=line` (config has webServer, auto-starts dev on :3000).
- Docs: pages live in `/data/dev/aidos/.aidos-docs/steps/{concept,internals}/sNN-*.mdx`; internals must have `^## (Implémentation|Méta|Méta-méta)`; registered in `.aidos-docs/docs.json`; pushed = check `git log` shows the docs commit (working tree clean + `main...origin/main` with no ahead/behind).
- Linear: `mcp__linear-server__get_issue` with id `AID-NN` (S15 = AID-47); confirm `status: Done` only if truly green.

**Recurring-pattern note:** S15 came in fully green on first verification — front projection (lib/scope.ts) mirrored the Go guard's verdict logic exactly (one source, no drift), which is the pattern to expect when a step ships a pure guard + a Workbench read-only projection. The known limits (no Policy/Operation wiring, no write-time hook, no ContextRouter overlap, illustrative candidate records) are by-design forward-deps = OpenQuestions, NOT residual issues.
