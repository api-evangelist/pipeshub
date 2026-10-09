---
generated: '2026-10-09'
method: generated
name: chat-with-an-agent
description: Find a configured PipesHub agent whose toolsets fit the request and converse with it to take actions (e.g. create a Jira ticket).
api: openapi/pipeshub-openapi.yml
operations: [listAgents, getAgent, streamAgentConversation, streamAgentConversationMessage]
source: >-
  Grounded in openapi/pipeshub-openapi.yml (operationIds verified verbatim) and the pipeshub_agents
  guidance in https://docs.pipeshub.com/mcp/tools.md ("route on toolsets / knowledge, not the name").
---
# Chat with an agent

1. **List agents** — `listAgents` (`GET /agents`, `page`/`limit`/`search`). The list may be empty; then fall back to plain chat.
2. **Inspect** — `getAgent` (`GET /agents/{agentKey}`); choose by its toolsets and knowledge, not its name.
3. **Start** — `streamAgentConversation` (`POST /agents/{agentKey}/conversations/stream`).
4. **Continue** — `streamAgentConversationMessage` (`POST /agents/{agentKey}/conversations/{conversationId}/messages/stream`).

Rules: agent runs need `agent:execute` scope over OAuth. Do not use OAuth client_credentials for user-facing agents (no user identity) — prefer a personal access token or a user-login OAuth app.
MCP equivalent: `pipeshub_agents` → `pipeshub_chat` with `agentId`.
