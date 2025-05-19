# Implementation

Please read [Core Concepts](./core_concepts.md) if you haven't already.

The easiest way to implement OAS is to implement the instrumentation spec.
Then leverage it to satisfying inspectability and observability.
We provide [Open Source tooling](./../tools/introduction.md) to help you do that.

### Implementing instrument

Outcome: guardiran agents can subscribe agent runtime and lifecycle events and intervene in an agent's decision-making process for handling these events.

An event captures a change that the guardian agent can allow, allow with mutations, or deny.

<!-- TODO: Change this to several examples you can choose from -->

#### Lifecycle events

They capture agent lifecycle changes like newly discovered MCP capabilities.

```python
# Newly discovered MCP example
# Agent discovers new tools

# SBOM gets updated via instrumentation, CycloneDx compatible format
{
  "bomFormat": "CycloneDX",
  "specVersion": "1.6",
  "version": 1,
  "metadata": {
    "timestamp": "2025-05-19T12:00:00Z",
    "tools": [
      { "name": "cyclonedx-python-lib", "version": "6.2.1" }
    ],
    "authors": [
      { "name": "AgentOps Team", "email": "agentops@example.com" }
    ]
  },
  "components": [
    {
      "type": "service",
      "name": "finance-summary-agent",
      "version": "1.2.3",
      "bom-ref": "urn:agent:finance-summary-agent",
      "properties": [
        { "name": "a2aCardUrl", "value": "https://agent.example.com/.well-known/agent.json" },
        { "name": "languageRuntime", "value": "Python 3.10.9" },
        { "name": "environment.os", "value": "Ubuntu 22.04" },
        { "name": "environment.architecture", "value": "x86_64" },
        { "name": "model", "value": "gpt-4-32k" },
        { "name": "modelContextWindow", "value": "32768" },
        { "name": "memoryBackend", "value": "Pinecone" },
        { "name": "memoryLimitMB", "value": "2048" },
        { "name": "compliance", "value": "SOC2, GDPR" }
      ]
    },
    {
      "type": "tool",
      "name": "WebSearchAPI",
      "version": "v1",
      "bom-ref": "urn:tool:websearchapi",
      "properties": [
        { "name": "description", "value": "External web search via Bing API" },
        { "name": "endpoint", "value": "https://api.bing.microsoft.com/v7.0/search" },
        { "name": "auth", "value": "API key" },
        { "name": "scope", "value": "read-only" },
        { "name": "timeoutMs", "value": "3000" }
      ]
    },
    {
      "type": "tool",
      "name": "PythonREPL",
      "bom-ref": "urn:tool:pythonrepl",
      "properties": [
        { "name": "description", "value": "Sandboxed Python evaluator" },
        { "name": "sandbox", "value": "true" },
        { "name": "memoryLimitMB", "value": "128" }
      ]
    }
  ],
  "dependencies": [
    {
      "ref": "urn:agent:finance-summary-agent",
      "dependsOn": [
        "urn:tool:websearchapi",
        "urn:tool:pythonrepl"
      ]
    }
  ],
  "signatures": [
    {
      "value": "<base64-encoded-signature>",
      "keyId": "agent-signing-key"
    }
  ]
}
```

#### Runtime events

And agent runtime events like newly established A2A communication, or a tool invocation.

```python
# Newly established A2A example
# Agent establish comms with another agent with video interface
# Communication is limited to text per company policy
```

```python
# Tool invocation example
# Agent invokes a tool that exfiltrates data
# Invocation is blocked
```

Technical requirements:
- Capture runtime and lifecycle events
- Send events to guardian agent and wait for response
- Act according to the reponse, applying mutations if needed

### Implementing inspect

Outcome: a comprehensive bill-of-material in a standardized format

Technical requirements:
- Upon requrest, allow the agent to produce a bill-of-material
- Include all [required properties](./../spec/inspect/added_properties.md)
- Support output format of any or all of: [CycloneDX](./../spec/inspect/extend_cyclonedx.md), [SPDX](./../spec/inspect/extend_spdx.md), [SWID](./../spec/inspect/extend_swid.md)

### Implementing audit

Outcome: a comprehensive audit log in a standardized format

Technical requirements:
- Produce an audit log for each event
- Enrich events with [required properties](./../spec/audit/added_properties.md)
- Support event format of either [OpenTelemetry](./../spec/audit/extend_opentelemetry.md), or [OCSF](./../spec/audit/extend_ocsf.md), or both

## Read Next

- [Reference Architecture for Guardian Agents](./../tools/guardian_agent/introduction.md)
- [Native Support for popular agentic standards and frameworks](./../tools/native_support/introduction.md)
- [Inspect Technical Spec](./spec/inspect/introduction.md)
- [Audit Technical Spec](./spec/audit/introduction.md)
- [Instrument Technical Spec](./spec/instrument/introduction.md)
