# Mapping Traces with Open Telemetry

To thoroughly trace an AI agent, it is crucial to connect traces and events in a manner that accurately reflects the agent's atomic actions and its broader units of logical operation. This provides a transparent, step-by-step visualization of how an agent processes information, arrives at decisions, and executes tasks. The Agent Security & Observability Protocol (ASOP) schema offers a structured framework for defining these interactions, which can then be effectively mapped to OpenTelemetry concepts.

- **Mapping ASOP Steps to OpenTelemetry Spans**: Each distinct step defined within a protocol such as ASOP can be directly correlated with an OpenTelemetry span. For instance:
    
    - `steps/message`: A span can be initiated when a user message is processed or when an agent generates a message. Attributes for this span would ideally include the message role, its content (potentially summarized or hashed to protect Personally Identifiable Information (PII)), and relevant IDs.
        
    - `steps/agentTrigger`: A dedicated span for an autonomous agent trigger, detailing the type of event and the content that prompted the agent's action.
        
    - `steps/toolCallRequest` and `steps/toolCallResult`: These map intuitively to spans encapsulating tool invocations. The `toolCallRequest` span should capture the `toolId`, `inputs`, and the `executionId`. The corresponding `toolCallResult` span would include the `executionId` and the `result` (comprising content and error status).
        
    - `steps/memoryRetrieval` and `steps/memoryRetrievalResult`: Spans designed for instances when an agent queries its memory and processes the retrieved results. Attributes should encompass memory type, the query itself, and the content of the retrieved memory (again, with due consideration for PII).
        
    - `steps/knowledgeRetrieval` and `steps/knowledgeRetrievalResult`: Analogous to memory operations, these spans are for querying and retrieving information from knowledge bases, capturing the query, any keywords used, and the results obtained.
        
    - `steps/knowledgeStore`: A span representing the action of an agent storing information into a knowledge base.
        
- **Hierarchical Spans for Logical Operations**:
    
    - A top-level span (e.g., named `agent.run` or `session`) can encompass the entirety of an interaction or a specific task undertaken by the agent.
        
    - Within this primary span, child spans can represent major logical phases such as `agent.plan` or individual turns within a conversation.
        
    - Each "turn" (which can be identified by a `turnId` as per ASOP ) can itself be a parent span.
        
    - Individual "steps" (identifiable by a `stepId` in ASOP ) occurring within a turn, such as an LLM call followed by a tool call, then become child spans under the respective turn span. This structure aligns well with the `RequestContext` defined in ASOP, which includes `agent`, `session`, `turnId`, and `stepId`.
        
- **Enriching Spans with Attributes from Agent Logic**:
    
    - **Reasoning**: Many steps in the ASOP schema incorporate a `reasoning` field. This critical piece of information, which elucidates the agent's rationale for a particular action, should be added as a custom attribute to the corresponding OpenTelemetry span (e.g., `agent.thought`, `agent.reasoning`).
        
    - **Inputs and Outputs**: For every atomic action (such as an LLM call, a tool call, or memory access), the precise inputs and outputs must be attached as span attributes. For LLM calls, this includes the prompt, model parameters (like temperature and max tokens), and the generated completion. For tool calls, it involves the tool name, input arguments, and output data. Sensitive data contained within these attributes may necessitate hashing, truncation, or redaction in accordance with prevailing privacy policies.
        
    - **Agent and Model Information**: Specific details about the agent (`agent.id`, `agent.name`, `agent.version`) and the LLM (`llm.model.name`, `llm.provider.name`) involved in each step should be included either as resource attributes or span attributes to provide comprehensive context.
        
    - **Timestamps**: Accurate timestamps marking the beginning and end of each operation are fundamental for performance analysis and correctly sequencing events.
