---
name: clearance
description: Use Clearance (a Forge app for Jira) through its MCP tools to take Jira work without duplicating another agent, and to share scarce resources such as a staging environment without using them at the same time. Use when an agent should pick, claim, renew, report or release a Jira issue, or claim a shared resource.
---

# Clearance for agents

Assignment says who *should* work on an issue. Clearance says who *may*, right now.
A claim is a lease: one holder at a time, it expires unless renewed, and every grant carries a
**fence number** that only ever goes up. If your lease expired and someone else took over, your
old `claim_id` stops working, so a stale agent cannot write over newer work.

## Connect

Clearance exposes its tools through the Atlassian MCP endpoint for Forge apps
(`https://mcp.atlassian.com/forge/574919e5-6d3d-472d-ae9e-56e284622747`, OAuth 2.1 with the user's consent). A site admin
must allow the app's MCP server first. Inside Atlassian, the Rovo agent "Clearance Coordinator"
uses the same tools.

## Tools

| Tool | Use it to | Key inputs |
|---|---|---|
| `next-task` | get the single next issue you may take (status To Do, label `agent-ok`, not `needs-human`, not claimed; priority then rank) | `projectKey`, `agentLabel` |
| `claim-task` | claim one issue for a limited time | `issueKey`, `agentLabel`, `ttlMinutes` (1–240, default 30) |
| `renew-claim` | extend your claim before it expires (fence stays the same) | `claimId`, `ttlMinutes` |
| `report-work` | comment your result on the issue and release the claim | `claimId`, `summary` |
| `release-claim` | give a claim back early (issues or resources) | `claimId`, `note` |
| `claim-resource` | take one slot of a shared resource; if all slots are taken you are queued | `resource`, `agentLabel`, `ttlMinutes` |
| `clearance-status` | see holder, expiry, fence, queue and audit trail | `target` (issue key or resource name) |
| `define-resource` | create a resource with a capacity, e.g. `staging-eu` with 1 | `resource`, `capacity` |

All tools answer with JSON and include a `message` for humans. Times are UTC from the database.

## Workflow

1. `next-task` with your project key. Take only the issue it returns; do not pick another one yourself.
2. `claim-task` for that issue. If `granted` is false, someone else holds it: go back to step 1.
3. Keep the `claimId` private. Renew with `renew-claim` well before `until` if the work takes longer.
4. Need staging or another scarce resource? `claim-resource`; if queued, retry later and do not use the resource meanwhile.
5. When done, `report-work` with a one or two sentence summary. It comments on the issue, then releases the claim.
6. If any call says your claim is not valid any more, stop writing: another holder has a higher fence. Claim again or pick other work.

## Rules

- Never work on an issue without a granted claim. Never use a resource slot you did not get.
- Issues labelled `needs-human` are not for agents; `next-task` already excludes them.
- The Jira workflow can enforce this: with the "Clearance required" validator, moving an issue to
  In Progress fails unless the person moving it holds the lease.
- Several agents under one Atlassian account are told apart by `agentLabel` and their own `claimId`.
