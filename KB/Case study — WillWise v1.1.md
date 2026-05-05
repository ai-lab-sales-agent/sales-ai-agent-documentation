---
type: "case_study"
topic: "Halo Lab AI project — conversational AI, prompt engineering, production delivery"
---

# Case Study — WillWise

A production conversational AI product built by Halo Lab and published on the Slack Marketplace. Demonstrates the same capabilities used in Sales AI Agents: natural language understanding, context-aware responses, prompt-driven architecture, and LLM evaluation testing.

> Created: March 2, 2026 | Updated: May 5, 2026

## Why This Is Relevant to Sales AI Agent Projects

- Demonstrates conversational AI that understands context and generates natural responses — the same capability that powers a Sales AI Agent's lead conversations
- Experience with prompt-driven architecture and LLM evaluation tooling (Promptfoo) — the same techniques used to ensure a Sales AI Agent responds accurately and follows qualification flows
- Proven ability to integrate AI into tools teams already use — Sales AI Agents similarly plug into a company's existing website, CRM, and calendar
- Full product lifecycle from concept through platform review and publication, showing Halo Lab delivers production-ready AI products, not prototypes
- Hands-on experience with model limitations (hallucinations, incomplete context) and how to solve them — directly applicable to building reliable Sales AI Agents

## Metadata

- **Industry:** AI, SaaS
- **Services:** Product Development, Web Design
- **Duration:** 2024 — September 2025 (Marketplace publication)
- **Project Type:** Slack App
- **Website:** https://willwise.ai/
- **Team:** Backend engineer, QA specialist (hourly), PM support

## About the Project

WillWise is an AI-powered assistant for Slack built and released by Halo Lab. It helps teams work smarter without leaving Slack by summarizing threads, drafting announcements, answering questions, and generating ideas.

The project started in 2024 as an AI assistant connected to Gmail, designed to search emails, analyze correspondence, and help draft replies. During implementation, the team found that LLM models at the time were not stable enough for that scenario, with hallucinations and incomplete context being a serious limitation. This led to a pivot toward a Slack-native product focused on practical communication workflows.

In the Slack version, users can connect their own OpenAI or Gemini keys, choose a model, and use the bot for summarizing threads, creating announcements, generating ideas, and answering open-ended requests. A "Reply me to DM" function allows transferring thread context to private messages for continued interaction with the bot.

## Challenges

Teams lose time scrolling through long Slack threads to find key decisions, context, or action items. Important information gets buried, and writing announcements or answering repeated questions pulls people away from focused work.

## Our Approach

**Discovery and feature prioritization.** The team reviewed other Slack bots in the Marketplace to understand interaction patterns and interface expectations. They also drew on prior experience building Slack apps (including TeamGarden). Product direction and functionality were shaped together with a key stakeholder. The pivot from Gmail to Slack was driven by hands-on learning from the first product version.

**Tech stack.** NestJS (TypeScript), PostgreSQL, OpenAI API, and Slack. Users could configure models (OpenAI and Gemini) at workspace or individual level, including parameters like temperature and max tokens. API keys were stored encrypted, accessible only server-side. All workspace data could be permanently removed on app uninstall. The architecture was prompt-driven (not RAG-based), with core logic centered on classifying requests into workflows: Summarize, Announcement, and Anything.

**Testing.** Two layers: product stability and prompt quality. The team manually verified bot behavior, Slack integration, and response reliability. Promptfoo was used to evaluate prompt performance against expected outputs across multiple test scenarios. Before Marketplace publication, the app had been installed and tested in 10+ Slack workspaces.

**Slack Marketplace submission.** The submission went through four review rounds. Slack checked legal aspects, landing page, functionality description, data security, and product behavior. The team made changes based on reviewer feedback, including clarifying landing page wording and explaining shortcut functions. First submission: May 2025. Published: September 2025.

## Results

WillWise was published on the Slack Marketplace in September 2025 after four review rounds. Before publication, the app had been installed and tested in 10+ Slack workspaces.

The team did not collect detailed usage analytics for this version. This was partly a conscious decision due to Slack's strict requirements for collecting and processing user data. The product was used for summarization and drafting communication inside Slack, but without systematic performance measurement.

After the release, a follow-up version with Stripe connectivity and company-managed keys was considered but not shipped, in part because the market shifted: Slack introduced its own AI functions and new integration tools.
