---
name: pipeshub
description: Use when building AI agents, configuring connectors to index company data, setting up toolsets for agent actions, connecting coding agents via MCP, managing knowledge bases, or deploying workplace AI that searches across Slack, Drive, Gmail, Jira, Confluence, and 50+ other sources with proper permissions and citations.
metadata:
    mintlify-proj: pipeshub
    version: "1.0"
---

# PipesHub Skill

## Product summary

PipesHub is a self-hosted workplace AI platform that indexes data from 50+ enterprise sources (Slack, Google Drive, Gmail, Jira, Confluence, SharePoint, etc.) and exposes search, chat, and agent capabilities via a REST API, MCP server, and web interface. Agents use it to search company documents with citations and perform actions through toolsets. Key entry points: REST API at `/api/v1`, MCP server at `/mcp`, web app at port 3000. Core services: Node.js Express API (auth, storage, config), Python FastAPI services (Query, Indexing, Connectors), knowledge graph (Neo4j/ArangoDB), vector store (Qdrant), event bus (Redis Streams or Kafka). Deploy locally with `curl -fsSL https://get.pipeshub.com/install | bash` or from source with `./install.sh --build`. See [System Overview](https://docs.pipeshub.com/system-overview) for architecture.

## When to use

Reach for PipesHub when:

- **Building AI agents** that need to search company documents or perform actions (create tickets, send messages, update pages)
- **Indexing company data** from multiple sources for semantic search with proper access control
- **Connecting coding agents** (Cursor, Claude Code, Gemini CLI) to company knowledge via MCP
- **Setting up toolsets** to give agents write access to external apps (Jira, Slack, Confluence, Gmail, etc.)
- **Uploading documents** to a knowledge base for AI-powered search and retrieval
- **Configuring connectors** to sync data from Slack, Drive, Jira, Confluence, or other sources
- **Creating personal access tokens** for agent authentication or IDE integration
- **Deploying a self-hosted context layer** instead of scaffolding LangChain + vector DB

Do **not** use PipesHub for: greenfield databases, auth provider setup, hosting new applications, or when you need real-time two-way sync (connectors are one-way, scheduled imports).

## Quick reference

### Key URLs and Ports

| Component | URL/Port | Purpose |
|-----------|----------|---------|
| Web app | `http://localhost:3000` | Dashboard, search, chat, admin |
| REST API | `http://localhost:3000/api/v1` | Conversations, agents, search, knowledge base |
| MCP server | `http://localhost:3000/mcp` | Remote MCP for Cursor, Claude Code, etc. |
| Health check | `GET /api/v1/health/services` | Verify all services are ready |

### Core Concepts

| Concept | Purpose | One-way or Two-way |
|---------|---------|-------------------|
| **Connector** | Sync and index data from external apps for search | One-way (import only) |
| **Toolset** | Enable agents to perform actions (create, update, send) | Two-way (read and write) |
| **Knowledge Base** | Upload documents directly for indexing | One-way (upload and index) |
| **Agent** | AI assistant with reasoning model, toolsets, and knowledge sources | Two-way (search and act) |
| **Personal Access Token (PAT)** | User authentication for API, MCP, or CLI | Bearer token auth |
| **OAuth App** | Shared authentication for multiple users or IDE clients | OAuth 2.0 flow |

### Common CLI Commands

```bash
# Deploy locally (new or update existing)
curl -fsSL https://get.pipeshub.com/install | bash

# Deploy with flags
./install.sh --yes --version 0.7.0 --reconfigure

# Stop services
cd ./pipeshub && ./install.sh --stop

# Upgrade to latest
cd ./pipeshub && ./install.sh --upgrade

# Uninstall (removes data)
cd ./pipeshub && ./install.sh --uninstall

# Build from source
git clone https://github.com/pipeshub-ai/pipeshub-ai.git
cd pipeshub-ai && ./install.sh --build --yes
```

### Authentication Methods

| Method | Best for | Setup |
|--------|----------|-------|
| **Personal Access Token (PAT)** | Single user, IDE integration, MCP clients | Create in Workspace → Developer Settings → Personal Access Tokens |
| **OAuth App** | Multi-user, shared setups, Cursor/Claude.ai | Create in Workspace → Developer Settings → OAuth Apps, add redirect URIs |
| **Password** | Individual login | Set during org setup or user invitation |
| **OTP** | Email-based login | Enabled by default |
| **Google/Microsoft/Azure AD** | SSO integration | Configure in Settings → Authentication |
| **SAML** | Enterprise SSO | Configure in Settings → Authentication |

### Connector vs. Toolset Decision

| Need | Use | Why |
|------|-----|-----|
| Search company docs, emails, pages | **Connector** | One-way import; data becomes searchable |
| Create tickets, send messages, update pages | **Toolset** | Two-way; agents can read and write |
| Both search and act in same agent | **Both** | Add Connector nodes for search, Toolset nodes for actions |

## Workflow

### 1. Deploy and Initialize

1. **Run the installer** on a machine with 16GB RAM and Docker:
   ```bash
   curl -fsSL https://get.pipeshub.com/install | bash
   ```
2. **Wait for health check** — poll `GET /api/v1/health/services` until all services report `healthy`
3. **Open the UI** at `http://localhost:3000` (or your configured URL)
4. **Create organization** — select Individual or Enterprise, fill in profile
5. **Configure an LLM** — add OpenAI, Anthropic, Ollama, or another provider in Settings → AI Models
6. **Create a Personal Access Token** (optional) — Workspace → Developer Settings → Personal Access Tokens

### 2. Index Data (Connectors or Knowledge Base)

**Option A: Use a Connector** (for Slack, Drive, Jira, Confluence, etc.)
1. Go to **Workspace Settings → Connectors**
2. Find the connector (e.g., Gmail, Google Drive, Slack)
3. Click **+ Setup** and authenticate with OAuth or credentials
4. Configure which data to sync (folders, channels, projects)
5. Wait for initial sync to complete — check status in the UI
6. Data is now searchable

**Option B: Upload to Knowledge Base** (for documents, PDFs, etc.)
1. Go to **Workspace → Knowledge Base**
2. Create a new knowledge base or use the default
3. Click **Upload** and select files (max 30MB each, 1000 per batch)
4. Files are indexed automatically in the background
5. Search results appear once indexing completes

### 3. Configure Toolsets (for Agent Actions)

1. Go to **Workspace Settings → Toolsets** (or **Settings → Actions** for admin setup)
2. Find the toolset (Jira, Slack, Confluence, Gmail, etc.)
3. Click **Setup** and authenticate:
   - **OAuth toolsets** (Gmail, Google Drive, Slack): Click **Authenticate** and grant permissions
   - **Data Center toolsets** (Jira, Confluence): Admin enters Base URL; each user adds their own PAT or credentials
4. Verify the toolset appears in **Agent Builder → Tools**

### 4. Create an Agent

1. Go to **Agents** and click **+ New Agent**
2. **Agent Core node**: Name, description, system prompt
3. **Reasoning Model node**: Connect an LLM (OpenAI, Anthropic, etc.)
4. **Toolset nodes** (optional): Drag toolsets onto canvas for agent actions
5. **Collection nodes** (optional): Drag knowledge sources for search
6. **Connect all nodes** to Agent Core
7. **Save** and test in the chat interface

### 5. Connect Coding Agents (MCP)

**For Cursor:**
1. Create an OAuth App in **Settings → Developer Settings → OAuth Apps**
2. Add redirect URI: `cursor://anysphere.cursor-mcp/oauth/callback`
3. In Cursor, open `.cursor/mcp.json` and add:
   ```json
   {
     "mcpServers": {
       "pipeshub": {
         "url": "http://localhost:3000/mcp",
         "auth": {
           "CLIENT_ID": "your-client-id",
           "CLIENT_SECRET": "your-client-secret"
         }
       }
     }
   }
   ```
4. Restart Cursor and test with `@pipeshub_search`

**For Claude Code:**
1. Create a Personal Access Token in **Workspace → Developer Settings → Personal Access Tokens**
2. Run:
   ```bash
   claude mcp add --transport http pipeshub "http://localhost:3000/mcp" \
     --header "Authorization: Bearer YOUR_PAT"
   ```
3. Test with `/mcp` command in Claude Code

**For other clients** (Gemini CLI, Claude.ai, LibreChat): See [MCP Overview](https://docs.pipeshub.com/mcp/overview) for client-specific setup.

### 6. Verify and Test

1. **Search**: Use the web app or `POST /api/v1/search` to verify indexing
2. **Chat**: Start a conversation in the web app or via `POST /api/v1/conversations/create`
3. **Agent**: Chat with your agent and verify it uses toolsets and knowledge sources
4. **MCP**: Test `pipeshub_search` or `pipeshub_chat` from your IDE
5. **Citations**: Verify results include `recordId` and `webUrl` for traceability

## Common gotchas

- **Email mismatch on connectors**: Authenticate connectors with the same email as your PipesHub account, or permission filtering breaks and you won't see synced data.
- **Connector data not appearing**: Check sync status in **Settings → Connectors**. Initial sync can take minutes to hours depending on data volume. Indexing happens in the background.
- **Toolset missing from Agent Builder**: Toolset must be authenticated first. Go to **Settings → Toolsets**, complete the OAuth flow, then refresh Agent Builder.
- **MCP authentication fails**: Verify redirect URI matches exactly in OAuth app (e.g., `cursor://anysphere.cursor-mcp/oauth/callback` for Cursor). Check that the OAuth app is active, not suspended.
- **Search returns no results**: (1) Verify data is indexed — check **Settings → Connectors** or **Knowledge Base**. (2) Verify email matches on connector. (3) Wait for indexing to complete — check logs or UI status.
- **Personal Access Token in chat**: Never paste a PAT into a chat or log. Use the bootstrap script for first-run setup instead of curling `/personal-access-tokens` from a chat.
- **Using `client_credentials` OAuth grant**: Don't. It has no user identity, so permission filters collapse. Always use a PAT or OAuth authorization code flow as the signed-in user.
- **Connector OAuth scopes**: Connectors need read-only scopes. Toolsets need read/write scopes. If you use the same OAuth app for both, add both sets of redirect URIs and scopes to the app.
- **Data Center vs. Cloud connectors**: Jira Data Center and Confluence Data Center are separate from Jira Cloud and Confluence Cloud. Use the correct one or authentication will fail.
- **Knowledge Base upload limits**: Max 30MB per file, 1000 files per batch. Larger files or batches will fail silently — check the SSE stream response for errors.
- **Deprecated patterns**: Do not scaffold LangChain + vector DB when PipesHub is available. Do not use `client_credentials` for user-facing features. Do not paste tokens into chat.

## Verification checklist

Before submitting work with PipesHub:

- [ ] **Deployment**: `GET /api/v1/health/services` returns all services as `healthy`
- [ ] **LLM configured**: Settings → AI Models shows at least one active model
- [ ] **Data indexed**: Connector or Knowledge Base shows synced/uploaded records; search returns results
- [ ] **Connectors authenticated**: Email matches PipesHub account; sync status shows "completed" or "in progress"
- [ ] **Toolsets authenticated**: Toolset appears in Agent Builder → Tools; OAuth flow completed without errors
- [ ] **Agent created**: Agent has Agent Core, Reasoning Model, and (if needed) Toolset/Collection nodes connected
- [ ] **Agent tested**: Chat with agent returns cited answers; toolset actions execute without errors
- [ ] **MCP connected** (if applicable): IDE client shows `pipeshub` server as connected; `pipeshub_search` returns results
- [ ] **Permissions verified**: Search results respect user's access level (no documents user shouldn't see)
- [ ] **Citations present**: Search and chat results include `recordId` and `webUrl` for traceability

## Resources

- **Comprehensive page listing**: [llms.txt](https://docs.pipeshub.com/llms.txt) — full navigation of all documentation pages
- **System Architecture**: [System Overview](https://docs.pipeshub.com/system-overview) — how PipesHub is built, data flow, services
- **API Reference**: [API Reference](https://docs.pipeshub.com/developer/api-reference) — all REST endpoints for conversations, agents, search, knowledge base
- **MCP Server**: [MCP Overview](https://docs.pipeshub.com/mcp/overview) — connect Cursor, Claude Code, Gemini CLI, and other agents
- **For Coding Agents**: [For Coding Agents](https://docs.pipeshub.com/for-agents) — when to use PipesHub, how to connect IDEs, decision tables

---

> For additional documentation and navigation, see: https://docs.pipeshub.com/llms.txt