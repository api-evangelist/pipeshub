---
generated: '2026-10-09'
method: generated
name: ask-a-cited-question
description: Ask PipesHub a question and get a streamed answer grounded in the org's indexed sources with citations, then continue the conversation.
api: openapi/pipeshub-openapi.yml
operations: [getAIModelsProviders, createConversation, streamChat, addMessageStream, getConversationById]
source: >-
  Grounded in openapi/pipeshub-openapi.yml (operationIds verified verbatim) and the pipeshub_chat
  section of https://docs.pipeshub.com/mcp/tools.md.
---
# Ask a cited question

1. **(Optional) pick a model** — `getAIModelsProviders` (`GET /configurationManager/ai-models`); pass a `modelKey` or omit it to use the org default.
2. **Start** — `createConversation` (`POST /conversations/create`) with `query`, optional `filters` (scope to connector/knowledge-base ids, first turn only), `recordIds`, `modelKey`. Or stream the first answer with `streamChat` (`POST /conversations/stream`).
3. **Follow up** — `addMessageStream` (`POST /conversations/{conversationId}/messages/stream`) with the same `conversationId`.
4. **Re-read** — `getConversationById` (`GET /conversations/{conversationId}`) for the full thread and citations.

Rules: chat reads a few retrieved passages — it is not for complete lists or whole documents. `createConversation` can return 429; back off before retrying and do not resend a turn that already produced a conversation.
MCP equivalent: `pipeshub_chat`.
