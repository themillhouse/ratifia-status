# Incident runbook

Monitoring produces data. This runbook is what turns that data into incident response — and into the evidence an auditor actually asks for. Without it there is uptime history and nothing else.

## Severity

Severity is set by **customer impact**, not by which box is broken.

| Sev | Meaning | Examples | First update | Cadence |
|---|---|---|---|---|
| **Sev1** | Core promise broken. Decisions cannot be created or resolved, or approvals are not resuming workflows. | Decision API down; Postgres unreachable; approvals silently not resuming. | 30 min | every 30 min |
| **Sev2** | Materially degraded but working. A path is broken while others hold. | Dashboard down but API healthy; notifications delayed; one surface failing. | 1 hour | every 2 hours |
| **Sev3** | Minor or cosmetic. No decision is blocked. | Docs down; elevated latency within tolerance. | Best effort | on resolution |

When torn between two levels, take the higher one. Downgrading later is cheap; a customer discovering you understated an outage is not.

## Declaring

1. **Open an incident issue in this repo.** Upptime opens one automatically on a failed check — use that issue rather than starting a parallel one, so detection time is preserved as recorded rather than reconstructed.
2. **Label the severity.**
3. **Post the first update within the window above.** Do this before the cause is known — "we are investigating elevated errors on the Decision API" is a valid and useful first update. Waiting for a diagnosis is the most common way status pages fail their purpose.

## Writing updates

- Say what customers experience, not what the infrastructure is doing. "Approval requests are not being delivered" beats "the outbox sweeper is wedged."
- Give the next update time in every update, and meet it. If nothing has changed, say that — silence reads as abandonment.
- Never speculate on cause publicly while it is unconfirmed. A wrong cause published is very hard to walk back.
- Do not disclose internal hostnames, vendor names, stack traces, or anything that would help someone attack the system while it is already degraded.

## Resolving

1. Confirm the check has recovered and stayed green — do not resolve on the first green tick.
2. Post a resolution update stating what was affected and for how long.
3. Close the incident issue. Detection, updates, and resolution timestamps now live in the issue history, which is the audit artifact.

## Postmortem

Required for every **Sev1**, and for any **Sev2** lasting over two hours.

Write it in the repo the fault lives in, not here, and link it from the incident issue. Cover: what customers experienced, timeline, cause, why detection took as long as it did, and what changes. If detection was slow because nothing was watching the failed path, the fix is a new monitor — that is the loop this page exists to close.

## A standing caution

The components on this page are only the ones with a real check behind them (see [`../OPERATIONS.md`](../OPERATIONS.md)). An unmonitored path can be badly broken while every light stays green.

So: **do not treat an all-green board as proof that nothing is wrong.** If a customer reports a problem the board does not reflect, believe the customer, declare the incident manually, and then add the monitor that would have caught it.
