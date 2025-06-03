# Overview

As AI agents become more sophisticated, transparent insight into their architecture, behavior, and security posture becomes critical. The Agent Bill of Materials (AgBOM) addresses this need by providing a structured, dynamic inventory of all components comprising an agent system including tools, models, capabilities, and dependencies. This concept aligns with growing calls for AI system transparency and supply chain integrity, particularly within regulated or enterprise environments .

## What Is AgBOM?
AgBOM, short for Agent Bill of Materials, is a comprehensive inventory that captures metadata about every component in an AI agent system. Its core purpose is to enable inspectability, allowing developers, auditors, and stakeholders to determine:
- What tools, models, and capabilities are embedded within an agent
- Who authored each component
- What version and configuration is currently deployed
- What external services and data sources are accessed

This visibility supports better security auditing, version tracking, and regulatory compliance. AgBOM must dynamically adapt to reflect the rapid iteration and evolution of agent architectures, especially in real-time or distributed environments.

## Outcome and Standardization
The end result of generating an AgBOM is a standardized, machine-readable artifact that outlines the full software composition of the agent. To support industry-wide adoption and interoperability, AgBOM supports output in the following standard formats:
- CycloneDX: A lightweight SBOM standard emphasizing security contexts
- SPDX (Software Package Data Exchange): An open standard maintained by the Linux Foundation, commonly used in open source software compliance
- SWID (Software Identification Tags): ISO standard for tagging software products to support asset management and compliance

## High-level Requirements
To ensure usefulness across diverse workflows and tooling environments, the AgBOM system should:
- Allow the agent to generate a bill-of-material upon request and dynamically upon changes
- Include all necessary fields for Agent, Knowledge, Memory and Tools
- Provide output in one or more standardized SBOM formats (CycloneDX, SPDX, SWID)

### AgBOM entities and parameters:

- Standard Packages: Name, Description, Version
- Models:	Name, Version, Description, Endpoint, Context Window, Args
- Capabilities:	Agent Card Definitions (per A2A), list of discovered Agents, list of MCP servers and parameters (protocolVersion, capabilities, serverInfo)
- Knowledge: Name, Description, Schema, Search type, Search args
- Memory: Name, Description, Type, Size, Search args, Window size, Path
- Tools: Name, Description, Scheme, Endpoint (local/directly-attached and MCP)

### Triggers for AgBOM Update

- Agent discovered, removed or changed capabilities
- MCP server discovered, removed or changed capabilities
- Knowledge discovered, removed or changed capabilities
- Tool discovered, removed or changed capabilities
- Memory discovered, removed or changed capabilities
- Model discovered, removed or changed capabilities
