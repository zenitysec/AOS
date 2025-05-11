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
# SBOM gets updated via instrumentation
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