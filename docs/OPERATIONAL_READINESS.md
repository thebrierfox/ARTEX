# ARTEX operational readiness gate (2026-11-06)

> **STATUS: SOURCE STAGED — NOT OPERATIONALLY CERTIFIED.** This branch incorporates selected maintained upstream history for review; no deployed instance, security posture, or successful end-to-end task is established by this document.

## Provenance and baseline

- Maintained candidate upstream: [BaaRai/ARTEX](https://github.com/BaaRai/ARTEX), source pinned for this stage at `a908e1ec170f973734b3258dd37d667d2562440a` (2026-11-06).
- The fork's original main commit was `b3717f7a5fe8d07fcd4fe7167603f314e9b64840` (v0.4.0, 2026-10-23). The staged upstream fast-forward brings 52 commits and 42 changed files; recheck comparison against current main before merging.
- The original author was `Autumn-27`. `elder-plinius/T3MP3ST` is an independent project, **not** a drop-in ARTEX upgrade. Its public verification methodology can inform readiness tests; its code is not imported here.
- *Source-code status is not runtime readiness.* Do not expose or launch against non-lab targets before these gates pass.

## Priority findings to resolve and demonstrate

1. **Prebuilt image/provenance:** The prior default Compose referenced discontinued `autumn27/artex`, and unsafe third-party images have been reported in the ARTEX ecosystem. Use reviewed source-built images only; verify hashes and any prior image actually used before inferring compromise.
2. **Listen address:** Confirm management port `8787` is loopback-only by default and that reverse-proxy deployments authenticate, restrict IPs and set `ARTEX_TRUSTED_PROXIES` correctly. Verify `8788` capture/proxy access is restricted.
3. **Secret handling:** Confirm `.dockerignore` excludes `.env`, `config.json`, `jwt.key`, backups and key stores. Preserve JWT key across rebuild. Audit still-mutable external downloads, `curl | bash`, sqlmap branch, and `@latest` packages.
4. **Auth/runtime:** Test first-run setup-token requirement, login rate limiting, 12-character minimum for new passwords, secret-redaction paths and cross-origin/model endpoint safeguards.
5. **Reproducibility:** Fresh source checkout must build and start using documented Docker Compose path without pre-existing `dist/` artifacts.
6. **Known upstream gaps:** Draft tool supply-chain verification [#127](https://github.com/BaaRai/ARTEX/pull/127), strict-provider malformed tool schema [#130](https://github.com/BaaRai/ARTEX/pull/130), remaining secret/proxy exposure, CI `./server` race coverage, `govulncheck`/Docker build tests, and skipped tests. Determine whether the MCP protocol fallback fix has merged (PR #132) and test against an actual strict protocol peer.

## On-demand operation acceptance

The following must be **executed and logged**, not checked off based on documentation:

- [ ] Record exact tested git SHA and complete `go build ./...`, `go vet ./...`, `go test -p 1 ./...`, targeted race suite, frontend static build, shellcheck, dependency audit and clean-checkout Docker build. Record skips/failures as gaps, not successes.
- [ ] On a disposable host/isolated test network, verify Compose port mapping, initialized authentication, health, process supervision, shutdown and cold restart.
- [ ] Verify secret volumes/config persistence and PostgreSQL/data backup + restore; verify JWT persistence and migrations using disposable state.
- [ ] Verify supported LLM providers (or keyless local model where configured), MCP interoperability, browser and security tool smoke tests without external scans.
- [ ] Execute an owned/synthetic fixture task through planning → tool execution → captured evidence → finding → independent retest → export. Track scope receipt, permissions, run cost and runtime.
- [ ] Deliberately test prompt injection, stale/missing evidence, pause/stop, interrupted task, idempotent recovery and operator approval pathways. A UI ACK alone is not a durable state transition.
- [ ] Verify rollback to previous release, retaining a known-good build, data backup and rollback instructions; stop unsafe automatic rebuild/redeployment on sync failure.
- [ ] Verify idle/dormant state consumes minimal resources and an on-demand cold start consistently reaches the acceptance baseline. Deploy only after explicit review.

## Reliability methodology adapted from Plinius's T3MP3ST (not code merged)

- [Scope and authorization receipts](https://github.com/elder-plinius/T3MP3ST/blob/main/docs/SCOPE_AND_AUTHORIZATION.md)
- [Evidence provenance](https://github.com/elder-plinius/T3MP3ST/blob/main/docs/VERIFIED_PROVENANCE.md)
- [Source release certification](https://github.com/elder-plinius/T3MP3ST/blob/main/docs/RELEASE_CHECKLIST.md)
- [Integrity ledger: methodological corrections](https://github.com/elder-plinius/T3MP3ST/blob/main/docs/INTEGRITY_LEDGER.md)
- [Open recovery race](https://github.com/elder-plinius/T3MP3ST/issues/229) and [stalled required-task recovery](https://github.com/elder-plinius/T3MP3ST/issues/223) illustrate failure modes to test independently.

## Closure

Only mark `READY_FOR_ON_DEMAND_USE` after a reviewer links proof for **all** essential checks and demonstrates a real cold-start, isolated lab task, evidence-linked finding, retest, stop/restart and backup/restore at the exact release SHA. Otherwise retain `NOT_CERTIFIED` with specific blockers.

No external security tests or live production deployments are authorized by this document.
