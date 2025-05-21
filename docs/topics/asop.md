# Agent Security & Observability Protocol

The Agent Security & Observability Protocl (ASOP) provides specification to buliding [trustworthy agents](./../introduction.md).
Agents that implement ASOP can be deployed with higher trust.
They are inspectable, auditable and instrumentable.
They are an open book 

They have a dynamic bill-of-material, a clear audit trail and hard inline-controls.

<!-- TODO: add diagram -->

Trustworthiness of agents builds upon the foundation of MCP and A2A, but provides value regardless. 
It is compatible with cybersecurity and obseravbility standards including OpenTelemetry, OCSF, CycloneDX, SPDX and SWID.

ASOP provides three benefits for agents:

<!-- TODO: Change this to three-sections with a menu. 
Each section has the security property and an example of what the outcome looks like.
Support switching the examples to show a different language or agent framework.
Ideally this shows major agent frameworks and also A2A and MCP -->

## Trustworthy agents are

<details>
    <summary>Reveal agent setup</summary>
<!-- TODO: Add agent setup.-->

```python
# example agent setup
#
```
</details>

### Instrumentable

| Value | Description | Standards |
|--|--|--|
| Hooks to agent run-time and lifecycle| Specifies run-time hooks that allow intervention at agent's lifecycle and run-time execution. | [ASOP](./topics/asop.md) |

<!-- TODO: instrumentation example -->

```python
# example agent instrumentation
# Instrument to provide inspection and audit
#
```

### Inspectable

| Value | Description | Standards |
|--|--|--|
| Dynamic agent-aware bill-of-material | Specifies properties that capture tools, models and capabilities of an AI agent. Extends SBOM standard specs with these properties – AgBOM. Goes further to add dynamic updates to AgBOM to account for dynamic agent capability discovery. | Extends [CycloneDX](./../spec/inspect/extend_cyclonedx.md), [SPDX](./../spec/inspect/extend_spdx.md), [SWID](./../spec/inspect/extend_swid.md) |

<!-- TODO: SBOM generation -->

```python
# example agent SBOM generation
# Switch between all three SBOM standards
#
```

<!-- TODO: Dynamic SBOM generation on capability discovery -->

```python
# example agent dynamic SBOM generation
# Switch between all three SBOM standards
#
```

### Auditable

| Value | Description | Standards |
|--|--|--|
| Comprehensive audit trace | Specifies events that capture AI agent lifecycle and run-time execution. Extends OpenTelemety and OSCF specs with these properties. | Extends [OpenTelemetry](./../spec/audit/extend_opentelemetry.md), [OCSF](./../spec/audit/extend_ocsf.md) |

<!-- TODO: audit log -->

```python
# example agent audit log generation
# Switch between two observability standards
#
```

## Read Next

- [Core concepts](./topics/core_concepts.md)
- [MCP & A2A](./topics/mcp_a2a.md)
