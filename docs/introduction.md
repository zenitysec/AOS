# Trustworthy Agents

Everyone is moving with high urgency to adopt AI agents – capturing advancement in AI to transform our digital spaces.
Agents can act, plan, write and execute just-in-time apps to achieve their goals.
The industry is rapidly standardizing around agent tooling and interoperability protocols, with [MCP](https://modelcontextprotocol.io) and [A2A](https://google.github.io/A2A/) respectively.
We can build more complex agents, in higher quantities, and allow them to easily communicate.

But we cannot trust any of them – the biggest inhibitor to their adoption. 
LLMs are an opaque technology from the get go. 
Planning, reasoning and long-term memories make agents even more of a black box. 
Agents invent, but not always like we want them to — ignoring instructions and following other goals.
Goals can be baked-in inadvertidely by the training process, or forced by an attacker exploiting the agent's gullibility.

Understanding what and why an agent performed an action is a big challenge. 
Multi-agent systems, implicit dependencies and remote tools make it worse. 
Inconsistent identity and reliance on [impersonation](https://www.jpmorgan.com/technology/technology-blog/open-letter-to-our-suppliers) adds more fuel to the fire. 
Lack of standardization makes every agent different.

Agents must become [trustworthy](https://news.microsoft.com/2012/01/11/memo-from-bill-gates/) to enable widescale adoption.

Transparency is the foundation of trust.
Whether built in-house or adopted as part of a service.
Consumed on cloud, as SaaS, on-prem or on endpoints.
Agents must be fully observable by the enterprise that welcomes them in.
We cannot trust a magic black-box.

Knowing when things go wrong is crucial, but it is not enough.
Enterprises must have a clear way to steer agents into the right path.
Correct course when an agent goes astray.
Agents must be instrumentable, allowing intervention at run-time and preventing bad outcomes.
Providing hard-controls, not soft-guardrails.

For agents to become trustworthy they must be inspectable, auditable and instrumentable.

**Inspectable**: we don’t have to guess what’s inside. 
Which tools, models and capabilities are being used. 
What software version is running and who built it. 
What are the services behind them, and which data they can access.

**Auditable**: we know what the agent did and why. 
We can trace back any action taken to the reasoning behind it and the originating task. 
Even if the thread goes through multiple agents and software systems. 
In case of compromise, we can identify and remediate the root cause.

**Instrumentable**: we can hook into agent execution and steer it in the right direction. 
We can put hard controls around agents and define their scope of action. 
Apply centralized enterprise logic, be it security, compliance or legal, uniformly across agent platforms. 
Prevent or modify behavior to comply.

Driven by high risk and high rewards, we are collectively thinking about these cybersecurity challanges with agents early on. 
Early standardization provides a unique opportunity to build trustworthiness into agents.

## What's missing?

Standards around agent tools, resources (MCP), discovery, communication and interoperability (A2A) are a huge step forward. 
While these continue to evolve, and others emerge, they are making agents more useful. 
They simplify adding capabilities. 
Streamline communication and interoperability.

Trustworthiness of agents builds upon the foundation of making agents useful. 
It is concerned with understanding and controlling agents, independant of advancing their capabilities.

| Trustworthy agents are | Current state | Relevant standards and frameworks | What's missing |
|--|--|--|--|
| Inspectable | The industry has made a great progress standardizing around [SBOM](https://www.cisa.gov/sbom) for software. AIBOM represents important progress, but agentic reasoning and capabilities are missing. We need AgBOM for agents.| CycloneDX, SPDX, SWID | [Extend](./spec/inspect/introduction.md) existing standards with AI agent support |
| Auditable | Agent platforms care a lot about observability, but they interpretation and implementation varies widely. [OpenTelemety](https://opentelemetry.io) is an adopted standard for operational logs. In the cybersecurity industry, [OCSF](https://ocsf.io/) has made a big impact. but does not apply to AI agents. | OCSF, OpenTelemtry | [Extend](./spec/observe/introduction.md) existing standards with AI agent support |
| Instrumentable| Agent platforms have begun to offer LLM guardrails, with different interfaces. All other agentic capabilities are kept inside the black box. Standard instrumentation mechanisms have been key to unlocking innovation and wide adoption of the instrumented platform. [eBPF](https://ebpf.io/) is a good example of that. | None | [Standard needed](./topics/aos.md) |
