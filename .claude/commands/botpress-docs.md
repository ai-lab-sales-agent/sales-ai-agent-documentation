Research Botpress documentation to answer the user's question: $ARGUMENTS

## How to research

1. **Start from the docs hub** — Fetch `https://botpress.com/docs` to get the documentation structure and find relevant section links.

2. **Navigate to relevant pages** — Based on the user's question, fetch the specific documentation pages that are most likely to contain the answer. Common sections:
   - Building agents: `https://botpress.com/docs/building-agents`
   - Knowledge bases: `https://botpress.com/docs/building-agents/knowledge-bases`
   - Workflows / nodes: `https://botpress.com/docs/building-agents/workflows`
   - Variables: `https://botpress.com/docs/building-agents/variables`
   - API reference: `https://botpress.com/docs/api`
   - Integrations: `https://botpress.com/docs/integration`

3. **Follow links deeper** — If the initial pages reference sub-pages with more specific information, fetch those too. Do up to 5 fetches total to get comprehensive coverage.

4. **Synthesize the answer** — Combine what you found into a clear, concise answer. Include:
   - Direct answer to the user's question
   - Relevant code snippets, config examples, or step-by-step instructions from the docs
   - Links to the specific doc pages you referenced (so the user can read more)

## Important
- Use the WebFetch tool with a focused prompt to extract only the relevant information from each page
- If the docs have moved or a URL returns nothing useful, try navigating from the main docs page
- If the question is about something not covered in the docs, say so clearly
- Relate findings back to this project's Botpress implementation when relevant (see MEMORY.md for build context)
