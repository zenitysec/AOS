# Core concepts

## Agent Environment Overview
An agent operates within an environment that includes interactions with several key entities and system components:
- User: The agent interfaces directly with the user
- Other Agents: Communication with peer agents is handled via the A2A protocol, enabling collaboration or delegation.
- Memory: The agent accesses memory resources that may be either local or remote through MCP.
- Knowledge Base: Knowledge sources can be either locally or accessed remotely via MCP
- Tools: Function or tool calling are either locally or invoked remotely using the MCP

A trustworthy agent is an Observed Agent that transparently exposes its interactions with the environment through standardized APIs to a Guardian Agent

![Agent Diagram](./agent_env.png "Agent Environment Diagram")

## Agent Instrumentation

### Observed Agent Responsibilities:
An Observed Agent should ensure inspectability, traceability, and observability by:
- Emitting Standard Events: Every interaction with the environment must be exposed through standardized event formats.
- Standardized Tracing: Maintain a standardized trace of all interactions to support improved observability, enable a holistic multi-agent view, and facilitate historical interaction analysis.
- Instrumentation: Events should trigger hooks that allow the Guardian Agent to enforce policies. Example policies may include restricting external communication, redacting sensitive data, or enforcing compliance constraints. Based on these policies, the Guardian Agent can permit, deny, or modify the content of the interaction.
- Reactive Capabilities: The agent must be capable of responding to Guardian Agent directives, including action denials or content mutations.

### Guardian Agent Responsibilities:
The Guardian Agent enforces policies and enables tracing through the following:
- Event Instrumentation Utilization: Leverage standard event hooks to evaluate and enforce policies, responding with permit, deny, or mutate instructions.
- Standardized Tracing: Maintain a consistent trace of all interactions to enhance observability, support a comprehensive view across agents, and enable detailed analysis of interaction history.

## Read Next

- [Implementation](./topics/implementation.md)
- [MCP & A2A](./topics/mcp_a2a.md)
