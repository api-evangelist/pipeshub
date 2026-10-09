---
generated: '2026-10-09'
method: generated
name: browse-the-knowledge-graph
description: Enumerate what is indexed in PipesHub (knowledge bases, connector folders and records) by paging through the knowledge graph instead of searching.
api: openapi/pipeshub-openapi.yml
operations: [listKnowledgeBases, listActiveConnectors, navigateKnowledgeGraph, getRecordContent]
source: >-
  Grounded in openapi/pipeshub-openapi.yml (operationIds verified verbatim) and the Tools Reference
  guidance that "how many / list all / every X" questions use navigate, not chat (https://docs.pipeshub.com/mcp/tools.md).
---
# Browse the knowledge graph

1. **Discover sources** — `listActiveConnectors` (`GET /connectors/active`) and `listKnowledgeBases` (`GET /knowledgeBase`, paged with `page`/`limit`/`search`).
2. **Navigate** — `navigateKnowledgeGraph` (`GET /connectors/navigate`). Omit `nodeId` for the top level; pass a `nodeId` to descend. Use `page`/`limit` to page children and `depth` (1–3) to flatten descendants in one call. Filter with `nodeTypes`, `createdAfter`/`createdBefore`, `modifiedAfter`/`modifiedBefore`.
3. **Read a leaf** — `getRecordContent` (`GET /connectors/record/{recordId}/content`).

Rules: page until a page returns fewer than `limit` items before reporting a count. Results are scoped to the caller's permissions.
MCP equivalent: `pipeshub_sources`, then `pipeshub_get_record_content` with `mode: navigate`.
