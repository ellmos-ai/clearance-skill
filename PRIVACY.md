# Clearance — Privacy and data handling

Last updated: 4 November 2026.

## Provider and contact

Clearance is provided by Lukas Geiger. Provider identification and the private contact address for privacy requests are available in the central [legal notice](https://github.com/lukisch/impressum).

For ordinary technical support, use [Clearance skill repository issues](https://github.com/ellmos-ai/clearance-skill/issues). Do not post personal information, Jira content or claim capabilities in public issues. Send access, correction or deletion requests through the private contact listed in the legal notice.

## Purpose and data

Clearance coordinates time-limited access to Jira issues and shared resources. It processes Atlassian account IDs, Jira issue and project identifiers, issue summaries returned by Jira for task selection, agent labels, resource names and descriptions, claim capabilities, fencing numbers, timestamps, queue entries, and user-entered notes or work summaries. Labels, descriptions and notes may contain personal information; do not enter sensitive personal information or credentials.

Forge SQL stores lease records, audit events, resource definitions and queue entries for each installation. Account IDs identify lease holders and resource creators. When report-work is invoked, the requested work summary is also written as a Jira comment. Releasing or expiring a lease does not delete its retained row or audit history.

## Hosting, integrations and access

The app runtime and SQL storage are hosted by Atlassian Forge. The production app declares no external network egress. This is not a statement that no data is stored. Atlassian operates the platform under its applicable privacy notices, terms and processing arrangements.

An external MCP client chosen and connected by an administrator or user receives the inputs and outputs needed for requested tool operations. Its provider and operator have their own responsibilities and policies. Where available and enabled, Atlassian Rovo processes prompts and action results through Atlassian's AI services. MCP and Rovo are optional for the issue panel and resource page.

The app displays lease state, holder labels, timing and audit information in its interface. Jira permissions apply to issue access. Agent labels are display labels, not verified identities. Keep claim IDs private.

The scheduled housekeeping handler emits generic completion or failure events containing an event name and status. Platform logs and diagnostic information may be accessible to app developers through Forge observability; this notice does not claim that developers are technically unable to access installation data.

## Retention and choices

The scheduled cleanup removes stale resource-queue entries. Leases expire using the database clock, while lease rows and audit events remain. Resource definitions remain until removed. Jira work-report comments follow the site's Jira retention and deletion controls.

After uninstall, Forge-hosted storage follows [Atlassian's hosted-storage data lifecycle](https://developer.atlassian.com/platform/forge/storage-reference/hosted-storage-data-lifecycle/). Immediate deletion on uninstall is not promised.

This preview is intended for evaluation with synthetic tasks and resources. It has no self-service interface for erasing an individual person's app records. Use the private contact in the legal notice for requests concerning stored information, correction, erasure or restrictions. The provider must evaluate each request and applicable obligations; this notice does not claim that an automated personal-data reporting or erasure flow is already implemented.

You can stop optional client or Rovo use and ask your site administrator to uninstall the app. No advertising or analytics service is included in the app's manifest.

## Scope

This notice describes Clearance. The separate [Um:bruch website privacy notice](https://um-bruch.org/datenschutz/) applies to that website and its contact form; it does not replace the app-specific information above. Changes to the app's processing will be reflected in a dated update to this notice.
