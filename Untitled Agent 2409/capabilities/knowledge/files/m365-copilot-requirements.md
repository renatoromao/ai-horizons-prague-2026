# Name: M365 Copilot Requirements

<!-- Microsoft 365 Copilot technical/admin rollout requirements: app and network prerequisites (not licensing/cost) -->

## What this covers

This snapshot describes the technical prerequisites an IT admin must satisfy before Microsoft 365
Copilot works for users, separate from licensing (see the M365 Copilot Licensing source for
that).

## Prerequisites

Users need: an assigned eligible Microsoft 365 base license (see the Licensing source), a
Microsoft Entra ID account, and a primary mailbox hosted on Exchange Online (Copilot also works
against archive, shared, and delegate mailboxes the user can access).

## Per-app configuration

Microsoft Copilot integrates with individual Microsoft 365 apps, each with its own setup notes:

- **Microsoft 365 Apps** — must be deployed to the user's device; privacy/connected-experience
  settings can affect Copilot availability; device-based licensing for Microsoft 365 Apps for
  enterprise is not compatible with Copilot; third-party cookies must be enabled for the web
  versions of Word, Excel, and PowerPoint.
- **OneDrive** — some Copilot features (like file restore) require the user to have a OneDrive
  account provisioned.
- **Outlook** — works with both classic and new Outlook, on supported platforms; not available on
  group mailboxes.
- **Teams and Teams Phone** — Copilot in Teams meetings needs transcription or recording enabled
  to reference meeting content afterward; Copilot for Teams Phone calls has separate licensing
  and configuration notes for VoIP versus PSTN calls.
- **Loop and Whiteboard** — both must be enabled for the tenant before Copilot can be used with
  them.

## Privacy and update mechanics

Admins should review Microsoft 365 Apps privacy/connected-experience settings, since some can
suppress Copilot features, and ensure the standard Office Feature Updates background task runs on
schedule, since several core Copilot experiences depend on it.

## Network requirements

Copilot reuses the same network connections and endpoints as Microsoft 365 apps generally.
Admins should ensure the standard Microsoft 365 URL/IP allow-list is not blocked, and specifically
ensure WebSockets (WSS) traffic to the Copilot enterprise domains is not blocked or TLS-inspected,
since several Copilot integrations depend on a persistent WebSocket connection — a common failure
mode is described where legacy proxy or firewall rules block streaming responses. Microsoft
recommends allowing the relevant domains at the wildcard/domain level rather than trying to
enumerate individual fully-qualified endpoints, because the underlying service topology changes
frequently.

## What this source does not do

This snapshot describes required configuration areas, not licensing or cost, and it does not
reproduce specific version numbers, URLs, or endpoint lists as a definitive checklist — for the
current, authoritative list of endpoints and versions, consult the live page.

For current prices/limits, see: https://learn.microsoft.com/en-us/microsoft-365/copilot/microsoft-365-copilot-requirements
