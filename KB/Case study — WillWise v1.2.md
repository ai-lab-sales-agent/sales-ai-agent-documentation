---
type: "case_study"
topic: "AI agent — conversational AI, prompt engineering, request classification, production delivery"
---

# Case Study — WillWise

A conversational AI agent built by Halo Lab that processes natural language requests, classifies them into workflows, and generates contextual responses. Deployed in Slack and published on the Slack Marketplace.

> Created: March 2, 2026 | Updated: May 5, 2026

## Metadata

- **Industry:** AI, SaaS
- **Services:** AI Agent Development, Product Development, Web Design
- **Duration:** 2024 — September 2025 (Marketplace publication)
- **Project Type:** AI Agent
- **Website:** https://willwise.ai/
- **Team:** Backend engineer, QA specialist (hourly), PM support

## What Was Built

A conversational AI agent that:
- Understands context from ongoing conversations and generates natural responses
- Classifies incoming requests into different workflows (summarization, announcements, open-ended Q&A)
- Uses prompt-driven architecture to route and respond accurately
- Supports multiple LLM providers (OpenAI, Gemini) with configurable parameters

## Why This Is Relevant to AI Agent Projects

- Demonstrates conversational AI that understands context and generates natural responses — the same capability that powers a Sales AI Agent's lead conversations
- Experience with prompt-driven architecture and LLM evaluation tooling (Promptfoo) — the same techniques used to ensure a Sales AI Agent responds accurately and follows qualification flows
- Proven ability to integrate AI into tools teams already use — Sales AI Agents similarly plug into a company's existing website, CRM, and calendar
- Full product lifecycle from concept through marketplace publication, showing Halo Lab delivers production-ready AI agents, not prototypes
- Hands-on experience with model limitations (hallucinations, incomplete context) and how to solve them — directly applicable to building reliable Sales AI Agents

## About the Project

WillWise is a conversational AI agent built and released by Halo Lab. It helps teams work smarter by summarizing threads, drafting announcements, answering questions, and generating ideas — all through a conversational interface.

The project started in 2024 as an AI agent connected to Gmail, designed to search emails, analyze correspondence, and help draft replies. During implementation, the team found that LLM models were not stable enough for that scenario, with hallucinations and incomplete context being a serious limitation. This led to a pivot toward a conversational AI agent focused on practical communication workflows.

## Challenges

Important information gets buried in team conversations. Writing announcements, finding key decisions, and answering repeated questions pulls people away from focused work. The goal: build an AI agent that processes conversations, extracts what matters, and generates useful responses.

## Our Approach

**Discovery and feature prioritization.** The team researched existing conversational agents to understand interaction patterns and expectations. Product direction was shaped with a key stakeholder.

**Architecture.** NestJS (TypeScript), PostgreSQL, OpenAI API. Prompt-driven architecture with core logic centered on classifying requests into workflows. Users could configure models (OpenAI and Gemini) at workspace or individual level, including parameters like temperature and max tokens. API keys stored encrypted, accessible only server-side.

**Testing.** Two layers: product stability and prompt quality. Promptfoo was used to evaluate prompt performance against expected outputs across multiple test scenarios. Before publication, the agent was installed and tested in 10+ workspaces.

**Marketplace submission.** Four review rounds covering legal aspects, functionality, data security, and product behavior. Published September 2025.

## Results

The AI agent was published on the Slack Marketplace in September 2025 after four review rounds. Tested in 10+ workspaces before publication.
