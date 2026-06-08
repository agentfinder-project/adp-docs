# Overview & Architecture

> Organizing agents, tools, and skills isn't really all that difficult, until you need to do so globally and with cryptographic trust guarantees.

The Agentic Resource Discovery Protocol (ARDP) is a lightweight, domain-anchored discovery specification. It defines how agentic resources — MCP servers, A2A cards, Skills, and traditional API tools — are cataloged, searched, and dynamically discovered across composable, federated networks of discovery services.

---

## 1. The scaling problem (prompt bloat)

Feeding every available agentic resource schema directly into a system prompt works fine when you have five tools. When you have five thousand, your context window vanishes, latency spikes, and the model's selection accuracy drops.

```
❌ Walled garden / prompt-stuffing:
[System Prompt] + [User Query] + [Tool A] + [Tool B] + [Tool C]... = Prompt Bloat

✅ Discovery-first (ARDP):
[User Query] ──> [Discovery service (POST /search)] ──> [Top 3 agentic resources] ──> [LLM Context]
```

Instead of forcing the model to sort through the noise, ARDP moves selection outside the active context window. The orchestrator queries a dedicated discovery service first, injecting only the top matching schemas into the final prompt.

---

## 2. How it differs from the "app store" model

ARDP moves away from manually installed, hardcoded integrations toward dynamic runtime discovery.

| Vector | Centralized registries | ARDP discovery services |
| :--- | :--- | :--- |
| **Discovery** | Manual registration / gatekeeper approval | Dynamic crawling and indexing (SEO for agents) |
| **Hosting** | Single central repository database | Self-hosted on publisher domains |
| **Lifecycle** | Hardcoded configs and manual installs | Discovered and connected at runtime |
| **Scope** | Restricted to a single protocol (e.g., MCP only) | Protocol-agnostic envelope (MCP, A2A, Skills) |

---

## 3. Decentralized trust (no central kingmakers)

Centralized directories create administrative bottlenecks and unilateral gatekeepers. ARDP avoids this by anchoring logical names directly to DNS domains:

```text
urn:ai:acme.com:finance:trading
```

*   **Domain authority**: Because the namespace maps to a FQDN (`acme.com`), the publisher domain acts as the cryptographic trust root.
*   **Workload identity**: The domain binds directly to the host's cryptographic identity (like SPIFFE or `did:web`) in a local `trustManifest`.
*   **No walled gardens**: Anyone can index these URNs, verify their provenance, and run a discovery service without requiring a central naming committee.

---

## 4. The core mechanics

ARDP operates on a simple envelope design using standard and proposed **IANA media types** (like `application/mcp-server+json` or `application/a2a-agent-card+json`) to wrap different protocols, delegating execution details to the underlying schemas.

```mermaid
sequenceDiagram
    participant Client as Orchestrator / LLM
    participant Service as Discovery Service
    participant Publisher as Publisher Domain

    Publisher->>Publisher: Hosts /.well-known/ai-catalog.json
    Service->>Publisher: Ingests static catalog
    Client->>Service: POST /search { "query": "expense filing" }
    Service-->>Client: Returns ranked agentic resource schemas + FQDNs
    Client->>Publisher: Connects & executes (MCP / REST)
```

1.  **Publish**: You host an `ai-catalog.json` at your domain's `.well-known/` directory.
2.  **Index**: Discovery services index your manifest and generate vector embeddings from your `representativeQueries`.
3.  **Search**: Clients query a discovery service (`POST /search`) with natural language.
4.  **Execute**: The client gets back the exact agentic resource schema and endpoint URL, connecting dynamically over the agentic resource's own protocol.
