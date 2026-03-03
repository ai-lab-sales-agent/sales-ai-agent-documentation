# UC2: Knowledge Gap Logger — Sub-Workflow

> Botpress Studio → Create workflow: "Knowledge Gap Logger"
> Standard Node workflow (no Autonomous Node needed).
> Created: March 2, 2026 | Updated: March 2, 2026

---

## Purpose

Log when the agent cannot fully or partially answer a visitor's question from KB. The actual answering and gap acknowledgment happen inline in the calling node via global Knowledge Boundary rules. This workflow only handles the logging.

## How It Gets Triggered

This workflow is attached as an "Execute Workflow" card to every Autonomous Node that has a KB card (Early Discovery, Deep Discovery, Nurture).

**Card description (paste into the card's description field):**
> "Use this when the Knowledge Base does not fully answer the visitor's question — either a partial answer or no answer at all."

The LLM in the Autonomous Node reads this description and decides when to call the workflow based on context. No prompt reference needed.

## Workflow Steps (Standard Node)

### Node 1: Log Gap (Execute Code)

```javascript
workflow.knowledge_gap_triggered = true;
workflow.knowledge_gap_question = event.preview || 'Question not captured';
```

### Node 2: Exit

Return to the calling workflow.

## Variables Access

| Variable | Read | Write |
|---|---|---|
| `knowledge_gap_triggered` | ✅ | ✅ |
| `knowledge_gap_question` | ✅ | ✅ |

## Builder Notes

- Attach this workflow as an "Execute Workflow" card on: Early Discovery, Deep Discovery, Nurture nodes
- The card description is what tells the LLM when to use it — test this during QA
- This workflow does NOT send any message to the visitor — the calling node handles that per global Knowledge Boundary rules
- `knowledge_gap_question` feeds into the pre-call brief so the sales team knows what to prepare
- If multiple gaps occur in one conversation, the variable will be overwritten with the latest question. For MVP this is acceptable — consider logging to a Botpress Table in v2
