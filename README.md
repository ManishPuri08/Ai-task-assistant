n8n Google Tasks Assistant

> A conversational AI agent that answers questions about your tasks by pulling live data from Google Tasks and summarizing it in natural language.

## Problem & Goal
Checking a task list usually means opening an app, scanning a list, and mentally prioritizing. This assistant removes that friction — a user can simply ask "what's on my task list?" in a chat interface and get a summarized, readable answer instead of a raw list.

## Architecture
**Trigger → Agent → Tool → Response**

1. **Chat Trigger** — the flow starts when a user sends a message (e.g. "what are my tasks today?").
2. **AI Agent (LangChain)** — interprets the request. It's instructed via a system prompt to call the Google Tasks tool whenever the question relates to tasks, and to return a clear summary rather than a raw dump.
3. **OpenAI Chat Model (gpt-5-mini)** — powers the agent's reasoning and language generation.
4. **Simple Memory (buffer window)** — keeps recent conversation turns in context, so follow-up questions ("what about the urgent ones?") work naturally.
5. **Google Tasks Tool** — fetches all tasks from a specified Google Tasks list (`getAll` operation) when the agent decides it's needed.

See `workflow.json` for the exact node configuration — import it into n8n to view the visual canvas.

## Tools & Integrations Used
- **n8n** (LangChain nodes: Agent, Chat Trigger, Memory Buffer Window)
- **OpenAI API** — gpt-5-mini as the reasoning/language model
- **Google Tasks API** — OAuth2-connected task data source

## Setup Instructions
1. Import `workflow.json` into your n8n instance (Workflows → Import from File).
2. Set up credentials for:
   - **OpenAI API** (your own API key)
   - **Google Tasks OAuth2** (connect your Google account)
3. Update the Google Task node's task list ID to point to your own Google Tasks list.
4. Activate the workflow and open the chat interface to start asking about your tasks.

## Product Decisions
- **Agent-decides-when-to-call-tool pattern, not a rigid script:** Rather than always fetching tasks on every message, the agent is prompted to invoke the Google Tasks tool only when the question is actually about tasks — keeping the assistant conversational for other queries too.
- **Buffer window memory over no memory:** Chosen so the assistant can handle natural follow-up questions without the user repeating context, while keeping the memory footprint small (only recent turns, not the full history).
- **getAll over filtered queries (v1 scope):** Kept simple for the first version — the agent summarizes the full list itself rather than relying on API-side filtering. A tradeoff between simplicity and precision for large task lists.

## Limitations & Next Steps
- Currently read-only — the agent can view tasks but not create, complete, or edit them. Adding write operations (mark complete, add task) would be a natural v2.
- No filtering by due date or priority at the API level yet; relies on the LLM to summarize sensibly, which may not scale well with very long task lists.
- Single task list only — no support yet for switching between multiple Google Tasks lists.

Just paste this into a file named `README.md` in your repo folder. Send the next JSON whenever you're ready for the next one.
