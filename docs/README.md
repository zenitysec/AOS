# Agent Observability Standard

We are moving with high urgency to adopt AI agents – capturing advancement in AI to transform our digital spaces. Agents can act, plan, write and execute just-in-time apps to achieve their goals. The industry is rapidly standardizing around agent tooling (MCP) and communication (A2A, ACP) protocols. We can build more complex agents, in higher quantities, and allow them to easily communicate.

But we cannot trust any of them – the biggest inhibitor to their adoption. LLMs are an opaque technology from the get go. Planning, reasoning and long-term memories make agents even more of a black box. Agents invent, but not always like we want them to -- ignoring instructions and following other goals.

Understanding what and why an agent performed an action is a big challenge. Multi-agent systems, implicit dependencies and remote tools make it worse. Inconsistent identity and reliance on impersonation adds more fuel to the fire. Lack of standardization makes every agent different.

Agents must become trustworthy to enable widescale adoption. Whether agents are built in-house or adopted as is, consumed as SaaS, on cloud, on-prem or on endpoints, enterprises should retain clear visibility and controls. For agents to become trustworthy they need to be inspectable, observable and instrumentable.

Inspectable: we don’t have to guess what’s inside. Which tools, models and capabilities are being used. What software version is running and who built it. What are the services behind them, and which data they can access.

Observable: we know what the agent did and why. We can trace back any action taken to the reasoning behind it and the originating task. Even if the thread goes through multiple agents and software systems. In case of compromise, we can identify and remediate the root cause.

Instrumentable: we can hook into agent execution and steer it in the right direction. We can put hard controls around agents and define their scope of action. Apply centralized enterprise logic, be it security, compliance or legal, uniformly across agent platforms. Prevent or modify behavior to comply.

Driven by high risk and high rewards, we are collectively thinking about these problems with agents early on. Early standardization provides a unique opportunity to build trustworthiness into agents.

## What is missing?

Standards around agent tools, resources (MCP), discovery, and communication (A2A, ACP) and a huge step forward. While these continue to evolve, and others emerge, they are all making agents useful. They simplify adding capabilities. Streamline communication interoperability.

Trustworthiness of agents builds upon the foundation of making agents useful. It is concerned with understanding and controlling agents, rather than advancing their capabilities.

| Trustworthy Agents are | Current state | Relevant standards and frameworks | What is missing |
|--|--|--|--|
| Inspectable| The industry has made a great process standardizing around SBOM for software. AIBOM represents important progress, but agentic reasoning and capabilities are missing. We need AgBOM for agents.| CycloneDX, SPDX, SWID | Extending existing standards with AI agent support |
| Observable | Agent platforms care a lot about observability, but they interpret its meaning differently. Some comprehensive, others severely lacking. Using different formats. OpenTelemety is an adopted standard, limited to operational logs. OCSF has made a big impact on the industry but does not apply to agents or AI. | OCSF, OpenTelemtry | Extending existing standards with AI agent support |
| Instrumentable| Agent platforms have begun to offer LLM guardrails, with different interfaces. All other agentic capabilities are kept inside the black box. Standard instrumentation mechanisms have been key to unlocking innovation and wide adoption of the instrumented platform. eBPF is a good example of that. | None | Standard needed |

## Agent Instrumentation Standard

Core idea: standardize hooks that agent platforms can support to allow instrumentation of observed agents by a security agent. Security agents can register hooks on different events, including tool use, capability change and agent communication. Upon an event, the observed agent communicates with the security agent for inspection, and receives a verdict. The agent platform is responsible for forcing the observed agent to comply with the verdict. A2A is adopted as a communication protocol between the observed and security agents. The observed agent can explain its reasoning for taking the action. The security agent can approve or deny an action, and to demand mutation of action parameters.

Implementation: native support for MCP, A2A and Open Source agent platforms. Add one line to leverage these protocols for instrumentation.

## Scope of work

1. Extend CycloneDX, SPDX, SWID to support AI agent components
1. Extend OCSF and OpenTelemetry with a schema for AI agent cybersecurity observability
1. Standardize instrumentation hooks required to build robust middleware for AI agents
    1. Establish communication with a Security Agent adopting A2A for communication
    1. Extend MCP, A2A with instrumentation hooks
    1. Standardize instrumentation hooks for unstandardized agent capabilities (e,g, long-term, RAG)
    1. Implement the standard on popular Open Source agent platforms (e,g, CrewAI)
1. Provide a reference architecture for usage of instrumentation hooks to deliver OCSF and OpenTelemetry compliant audit log