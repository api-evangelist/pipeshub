---
generated: '2026-10-09'
method: generated
name: find-and-read-a-document
description: Locate a document in PipesHub's permissioned index by semantic search, resolve it to a recordId, then read or download its content.
api: openapi/pipeshub-openapi.yml
operations: [search, lookupRecordByIdentifier, getRecordContent, streamRecordBuffer]
source: >-
  Grounded in openapi/pipeshub-openapi.yml (all operationIds verified verbatim) and the provider's
  MCP Tools Reference decision guide (https://docs.pipeshub.com/mcp/tools.md): search -> top hit -> read content.
---
# Find and read a document

Auth: `Authorization: Bearer <token>` (personal access token or OAuth access token). Base URL `{instance_url}/api/v1`, default `https://app.pipeshub.com/api/v1`. Never print or log the token.

1. **Search** — `search` (`POST /search`) with `query`, optional `filters`, and `limit` (5–10 is enough to resolve a filename). Take the `recordId` of the top hit.
2. **Or resolve a known identifier** — when the user gives a URL, issue key or external ID, call `lookupRecordByIdentifier` (`GET /connectors/record/lookup?identifiers=...`) instead of searching.
3. **Read** — `getRecordContent` (`GET /connectors/record/{recordId}/content`) returns the record's text for answering.
4. **Download bytes** — only when the user wants the file itself, `streamRecordBuffer` (`GET /knowledgeBase/stream/record/{recordId}`), optionally with `convertTo` (connectors honour `application/pdf`).

Rules: results are permission-filtered for the caller; a 403 means the user cannot see that record, not that it is missing. Search returns passages, never a complete list — for "list all / how many" use the navigate skill.
MCP equivalent: `pipeshub_search` → `pipeshub_get_record_content` / `pipeshub_download_record`.
