# Comparisons

ARDP is designed as a **superset** of existing agent and tool discovery approaches. It is not a replacement for other registries; rather, it acts as a federated overlay that unites them.

**Q. Why not X?**
**A. Why not both?**

*   **Universal envelope**: Anything can get an AI Catalog "card" — an MCP server, a Skill, an ACP agent, an A2A agent, a traditional REST API, or whatever comes next.
*   **Federated indexing**: Once a card is published, it can be indexed by any ARDP discovery service. You don't have to choose between registries; a discovery service can consume all of them, or you can curate your own subset.

---

## What about...

### What about the ACP Agent Registry

The list of ACP agents in [ACP's Agent Registry](https://agentclientprotocol.com/get-started/registry) is already structurally close to the AI Catalog specification. ACP registries can export their directory manifests as standard `ai-catalog.json` feeds, enabling instant web-scale discovery for editor-context agents — without those agents having to be re-registered anywhere.
