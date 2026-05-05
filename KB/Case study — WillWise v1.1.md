---
type: "case_study"
topic: "AI agent — conversational AI, prompt engineering, request classification, production delivery"
---

# Case Study — WillWise

A conversational AI agent built by Halo Lab that processes natural language requests, classifies them into workflows, and generates contextual responses. Deployed in Slack and published on the Slack Marketplace.

> Created: March 2, 2026 | Updated: May 5, 2026

## What Was Built

A conversational AI agent that:
- Understands context from ongoing conversations and generates natural responses
- Classifies incoming requests into different workflows (summarization, announcements, open-ended Q&A)
- Uses prompt-driven architecture to route and respond accurately
- Supports multiple LLM providers (OpenAI, Gemini) with configurable parameters
- Published on the Slack Marketplace after passing four review rounds on security, functionality, and data handling

## Metadata

- **Industry:** AI, SaaS
- **Services:** AI Agent Development, Product Development, Web Design
- **Duration:** 2024 — September 2025 (Marketplace publication)
- **Project Type:** AI Agent
- **Website:** https://willwise.ai/
- **Team:** Backend engineer, QA specialist (hourly), PM support

## Challenges

Important information gets buried in team conversations. Writing announcements, finding key decisions, and answering repeated questions pulls people away from focused work. The goal: build an AI agent that processes conversations, extracts what matters, and generates useful responses — without requiring users to leave their workflow.

## Our Approach

**Discovery and feature prioritization.** The team researched existing conversational agents to understand interaction patterns and expectations. Product direction was shaped with a key stakeholder. The project started as an email-connected AI agent but pivoted after discovering LLM limitations (hallucinations, incomplete context) — leading to a conversational agent focused on practical workflows.

**Architecture.** NestJS (TypeScript), PostgreSQL, OpenAI API. Prompt-driven architecture with core logic centered on classifying requests into workflows. Users could configure models (OpenAI and Gemini) at workspace or individual level, including parameters like temperature and max tokens. API keys stored encrypted, accessible only server-side.

**Testing.** Two layers: product stability and prompt quality. Promptfoo was used to evaluate prompt performance against expected outputs across multiple test scenarios. Before publication, the agent was installed and tested in 10+ workspaces.

**Marketplace submission.** Four review rounds covering legal aspects, functionality, data security, and product behavior. Published September 2025.

## Results

The AI agent was published on the Slack Marketplace in September 2025 after four review rounds. Tested in 10+ workspaces before publication. The team gained hands-on experience with:
- Handling LLM limitations (hallucinations, incomplete context) and designing solutions
- Prompt-driven architecture for reliable request classification
- LLM evaluation tooling (Promptfoo) for quality assurance
- Navigating platform review processes for production deployment
