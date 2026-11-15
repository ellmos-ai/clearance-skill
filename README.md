# clearance-skill

**English** | [Deutsch](README_de.md)

Agent skill and data-handling notice for **Clearance**, a Forge app for Jira that answers one question for AI agents: who may work on an issue, or use a scarce resource, right now. Assignment says who *should* work on something; Clearance says who *may*.

This repository contains only the public skill text and the privacy notice. It does not contain the app itself.

> **Status: early, preview.** According to the privacy notice, the app is a preview intended for evaluation with synthetic tasks and resources. The skill describes how an agent uses the app through MCP tools.

## Contents

| File | Purpose |
|---|---|
| [`skill.md`](skill.md) | The skill: connection, tool list, step-by-step agent workflow and rules |
| [`PRIVACY.md`](PRIVACY.md) | Which data the app processes, where it is hosted, retention and how to contact the provider |
| [`LICENSE`](LICENSE) | MIT License |

## The idea in short

- A **claim is a lease**: one holder at a time, it expires unless renewed.
- Every grant carries a **fence number** that only ever increases. If a lease expired and someone else took over, the old `claim_id` stops working, so a stale agent cannot overwrite newer work.
- Shared resources (for example a staging environment) have a capacity; if all slots are taken, the agent is queued.

## Tools described in the skill

| Tool | Use |
|---|---|
| `next-task` | Get the single next issue the agent may take (status To Do, label `agent-ok`, not `needs-human`, not claimed; by priority, then rank) |
| `claim-task` | Claim one issue for a limited time (`ttlMinutes` 1 to 240, default 30) |
| `renew-claim` | Extend a claim before it expires (the fence stays the same) |
| `report-work` | Comment the result on the issue and release the claim |
| `release-claim` | Give a claim back early (issues or resources) |
| `claim-resource` | Take one slot of a shared resource, or be queued |
| `clearance-status` | Show holder, expiry, fence, queue and audit trail |
| `define-resource` | Create a resource with a capacity |

The skill's workflow: take only the issue `next-task` returns, claim it, renew before it expires, claim shared resources before using them, finish with `report-work`, and stop writing as soon as a call says the claim is no longer valid. Issues labelled `needs-human` are not for agents. A Jira workflow validator ("Clearance required") can additionally block moving an issue to In Progress unless the mover holds the lease.

## Usage

The app is reached through the Atlassian MCP endpoint for Forge apps (OAuth 2.1 with the user's consent); a site admin must allow the app's MCP server first. The exact connection details are in [`skill.md`](skill.md). Load `skill.md` as a skill in your agent according to its skill mechanism; it carries `name` and `description` front matter.

## Privacy

See [`PRIVACY.md`](PRIVACY.md). In short: Forge SQL stores lease records, audit events, resource definitions and queue entries per installation; the production app declares no external network egress; lease rows and audit events are retained after expiry. Do not put personal or sensitive information into agent labels, descriptions or notes.

## Relation to ellmos

`clearance-skill` is published under the ellmos-ai organization next to the agent-coordination tools of the ecosystem, such as [lock-master](https://github.com/ellmos-ai/lock-master) (file locks for mixed agent teams) and [task-master](https://github.com/ellmos-ai/task-master). Clearance uses a comparable lease-and-fence approach for Jira work.

## License

MIT, see [`LICENSE`](LICENSE).
