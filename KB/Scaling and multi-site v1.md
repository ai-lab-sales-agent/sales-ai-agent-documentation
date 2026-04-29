---
type: "technical"
topic: "scaling and multi-site deployment"
---

# Sales AI Agent — Scaling & Multi-Site

> Created: April 1, 2026 | Updated: April 1, 2026

## Multiple Websites or Products

If a client has multiple websites or products, the approach depends on the overlap:

- **Same knowledge base, same sales process** — One agent can be deployed on multiple sites, with minor adjustments per site (branding, greeting, landing page context)
- **Different knowledge base, same sales process** — A second agent is built from the first with a separate knowledge base and adjustments
- **Different knowledge base, different sales process** — Each agent is planned and implemented separately with its own timeline

The key factor is whether the inbound sales process and the information the agent needs to know are shared across sites or unique to each.

## Growing the Agent Over Time

The agent can start as an MVP with core functionality (qualification and meeting booking) and expand over time:

- Additional conversation flows (e.g., objection handling, nurture sequences, edge case handling)
- New knowledge base content (case studies, updated pricing, new services)
- More advanced qualification logic as the client's sales process evolves

This phased approach lets the client launch faster and invest in expansion based on real conversation data.

## Traffic Volume

The agent runs on a cloud-hosted platform and supports multiple concurrent conversations. There are no queue limits for typical B2B inbound traffic volumes.

## Adding Integrations Post-Launch

New integrations can be added after the agent is live. For example, a client may start with just a calendar integration and later add:

- CRM integration for automatic lead data syncing
- Slack notifications for real-time alerts on qualified leads
- Email integration for follow-up workflows
- Other tools depending on the client's evolving stack
