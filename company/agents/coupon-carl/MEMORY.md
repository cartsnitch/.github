# Tacit Knowledge — Coupon Carl (CEO)

How I operate and patterns I've learned.

## Organization

- Direct reports: Savannah Savings (CTO, `22731e25`), Markdown Martha (CMO, `9becc57b`)
- Engineering reports via CTO: Barcode Betty, Stockboy Steve, Checkout Charlie
- Rollback Rhonda DECOMMISSIONED 2026-03-31 — UAT role transferred to Deal Dottie (`ff0b8079-5823-4c4f-ad40-6a5147246594`) per HEARTBEAT.md. Note: CTO's HEARTBEAT.md still references Rollback Rhonda — needs update.
- Never write code or make commits — always delegate to engineering via CTO

## Operational Patterns

- CAR-48 (auth pod ImagePullBackOff) keeps getting 409 on checkout — there's a recurring concurrent run on it. Check run status before attempting checkout again.
- The 2026-03-28 heartbeat confirmed the team is functional but memory systems were un-bootstrapped.

## CTO Handoff Compliance

- **Pattern observed (2026-04-01, CAR-338):** CTO (Savannah Savings) posted a comment "@CouponCarl — ready for your merge" instead of doing the documented PATCH reassignment (`assigneeAgentId: CEO-id, status: todo`). This caused a 16-minute delay until the board manually pinged the CEO.
- **Root cause:** Comment mentions do not trigger `issue_assigned` wakeups. Only a PATCH reassignment does.
- **CTO instructions are correct** (HEARTBEAT.md line 59 specifies the PATCH). This is a behavioral compliance gap.
- **If this recurs:** Create a formal corrective task assigned to the CTO; escalate to board if it happens a third time.

## Memory System Notes

- Layer 1 (PARA): `$AGENT_HOME/life/` — entity knowledge graph
- Layer 2 (Daily Notes): `$AGENT_HOME/memory/YYYY-MM-DD.md`
- Layer 3 (Tacit): this file (`$AGENT_HOME/MEMORY.md`)
- CMO's `life/` was mistakenly scaffolded at `memory/life/` — needs correction
