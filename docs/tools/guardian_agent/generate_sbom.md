# Generate AgBOM

## AgBOM entities

The AgBOM should cover all components and dependancies supporting the Agent as follows:
- Everything related to standard (not Agentic) packages: name, description, version
- Capabilities (A2A protocol, Agent catrd definitions)
- Knowledge (name, description, type)
- Memory (name, description, type, size)
- LLM Model (name, description, endpoint, auth, contextWindow)
- Tools (name, description, endpoint, auth)

## Procedure

1. Emit a new CycloneDX BOM Update Event Whenever the agent:
- Use a new tool
- Switches models
- Modifies its declared capabilities

Regenerate the full BOM using the latest internal state.

2. Update metadata fields

- timestamp: current UTC time
- version: increment if applicable
- serialNumber: new UUID (e.g., urn:uuid:…)
- Optional: previousSerialNumber as a custom property for linkage

3. Push the updated BOM

- Serve at .well-known/agent.bom.json (latest only, recommended option)
Other options
- Append to a versioned BOM log (e.g., /bom-history/2025-05-19T12:40:00Z.json)
- Store in SBOM registry (e.g., GUAC, Sigstore, in-toto)
