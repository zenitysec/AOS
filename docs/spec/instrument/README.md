# Agent Security & Observability Protocol


In a landscape where AI agents increasingly orchestrate critical and complex workflows, write and execute apps acting on our behalf and making decisions autonomously, wide adoption remains blocked by lack of observability.

Agents use tools, memory and knowledge to achieve the goals. They collaborate, communicate and negotiate independently (A2A) to complete tasks. All the magic is wrapped in a black box and we remain blind, too blind to trust them.

**Agent Security & Observability Protocol (ASOP)** is a standard that establishes a unified, platform-agnostic framework for surfacing every decision, action, prompt, and output as structured events while simultaneously providing hooks for live intervention and control. 
By embedding lightweight call-outs at each step of an agent’s reasoning cycle—from retrieval-augmented generation through action execution—**ASOP** streams context-rich events to observability back-ends, and also allows supervisors or automated guards to modify, or veto agent bad behavior, enforcing security and compliance policies. The result is a transparent, traceable, and governable AI runtime that lets organizations widely adopt intelligent agents without sacrificing control, trust, or safety.


## The ASOP solution
ASOP provides a standardized way for these independent opaque agentic systems to inspect. It defines:

- **A common transport and format**: JSON-RPC 2.0 over HTTP(S) for how messages are structured and transmitted.
- **Support for various data modalities**: Understands not just text, but also files, structured data (like forms), and potentially other rich media.
- **Support for existing industry standards**: Supports widely adopted standards such as A2A and MCP.
- **Extendable schema**:To allow extendability of future innovations. 


### Design Principles

The Agent Security & Observability Protocol (ASOP) provides specification to buliding [trustworthy agents](./../introduction.md).
Agents that implement ASOP can be deployed with higher trust.
They are inspectable, auditable and instrumentable.
They are an open book 

They have a dynamic bill-of-material, a clear trace trail and hard inline-controls.

<!-- TODO: add diagram -->

Trustworthiness of agents builds upon the foundation of MCP and A2A, but provides value regardless. 
It is compatible with cybersecurity and obseravbility standards including OpenTelemetry, OCSF, CycloneDX, SPDX and SWID.

<!-- TODO: Change this to three-sections with a menu. 
Each section has the security property and an example of what the outcome looks like.
Support switching the examples to show a different language or agent framework.
Ideally this shows major agent frameworks and also A2A and MCP -->

### Extensions
Our mission is to enable secure usage and observability of AI agents everywhere. While the landscape is evolving very fast, new standards and protocols are published and adopted by users, vendors and companies.<br>
We love and appreciate standards, but we don't compromise on security. We believe that security should be implemented also on agent's ecosystem to fill in the security gaps.<br>
Introducing ASOP extensions we are committed to:
- Keep up with existing protocols and support new ones.
- Transparent support for native protocols.

Read more about [MCP](../extensions/extend_mcp.md) and [A2A](../extensions/extend_a2a.md) extensions.


### Read Next

- [Core concepts](./topics/core_concepts.md)
- [MCP & A2A](./topics/mcp_a2a.md)
