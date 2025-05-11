# Agent Observability Standard

The Agent Observability Standard (AOS) provides specification to buliding [trustworthy agents](./../introduction.md).
Agents that implement AOS can be deployed with higher trust.
They are inspectable, auditable and instrumentable.
They are an open book 

They have a dynamic bill-of-material, a clear audit trail and hard inline-controls.

<!-- TODO: add diagram -->

Trustworthiness of agents builds upon the foundation of MCP and A2A, but provides value regardless. 
It is compatible with cybersecurity and obseravbility standards including OpenTelemetry, OCSF, CycloneDX, SPDX and SWID.

AOS provides three benefits for agents:

<!-- TODO: Change this table to three-sections with a menu. 
Each section has the security property and an example of what the outcome looks like -->

| Trustworthy agents are | Value | Description | Standards |
|--|--|--|--
| Inspectable | Dynamic agent-aware bill-of-material | Specifies properties that capture tools, models and capabilities of an AI agent. Extends SBOM standard specs with these properties – AgBOM. Goes further to add dynamic updates to AgBOM to account for dynamic agent capability discovery. | Extends [CycloneDX](./../spec/inspect/extend_cyclonedx.md), [SPDX](./../spec/inspect/extend_spdx.md), [SWID](./../spec/inspect/extend_swid.md) |
| Auditable | Comprehensive audit trace | Specifies events that capture AI agent lifecycle and run-time execution. Extends OpenTelemety and OSCF specs with these properties. | Extends [OpenTelemetry](./../spec/audit/extend_opentelemetry.md), [OCSF](./../spec/audit/extend_ocsf.md) |
| Instrumentable| Hooks to agent run-time and lifecycle| Specifies run-time hooks that allow intervention at agent's lifecycle and run-time execution. | [AOS](./topics/aos.md) |

## Read Next

[Agent Observability Standard (AOS)](./topics/aos.md)