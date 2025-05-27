# Overview

As AI agents become more sophisticated, transparent insight into their architecture, behavior, and security posture becomes critical. The Agent Bill of Materials (AG-BOM) addresses this need by providing a structured, dynamic inventory of all components comprising an agent system — including tools, models, capabilities, and dependencies. This concept aligns with growing calls for AI system transparency and supply chain integrity, particularly within regulated or enterprise environments .

## What Is AG-BOM?
AG-BOM, short for Agent Bill of Materials, is a comprehensive inventory that captures metadata about every component in an AI agent system. Its core purpose is to enable inspectability — allowing developers, auditors, and stakeholders to determine:
- What tools, models, and capabilities are embedded within an agent
- Who authored each component
- What version and configuration is currently deployed
- What external services and data sources are accessed

This visibility supports better security auditing, version tracking, and regulatory compliance. AG-BOM must dynamically adapt to reflect the rapid iteration and evolution of agent architectures, especially in real-time or distributed environments.

## Outcome and Standardization
The end result of generating an AG-BOM is a standardized, machine-readable artifact that outlines the full software composition of the agent. To support industry-wide adoption and interoperability, AgBOM supports output in the following standard formats:
- CycloneDX: A lightweight SBOM standard emphasizing security contexts
- SPDX: An open standard maintained by the Linux Foundation, commonly used in open source software compliance
- SWID (Software Identification Tags): ISO standard for tagging software products to support asset management and compliance

## High-level Requirements
To ensure usefulness across diverse workflows and tooling environments, the AG-BOM system must:
- Allow the agent to generate a bill-of-material upon request and dynamically upon changes
- Include all necessary metadata fields for Agent, Knowledge, Memory and Tools
- Provide output in one or more standardized SBOM formats (CycloneDX, SPDX, SWID)

### AG-BOM entities and parameters:

- Standard Packages: Name, Description, Version
- Models:	Name, Description, Endpoint, Auth Mechanism, Context Window
- Capabilities:	A2A Protocols, Agent Card Definitions, MCP Protocol and servers
- Knowledge: Name, Description, Type
- Memory: Name, Description, Type, Size
- Tools: Name, Description, Endpoint, Auth Mechanism

### Dynamic Update Procedure Principles

1. Emit a new AG-BOM Update Event Whenever the agent:
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
