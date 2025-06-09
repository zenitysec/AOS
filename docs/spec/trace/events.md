# AOS Supported Events

Every agent action becomes observable. Every decision gets traced. Every communication leaves a trail.

Agent Observability Standard (AOS) transforms opaque AI systems into transparent, auditable processes through standardized event emission. This document defines the canonical events that enable trust through visibility.

## Event Classification
All observable actions in AOS are considered **Agent Steps**. Each step represents a single, traceable action taken by the agent or the system. To enhance explainability and provide a clear structure for analysis, these steps are classified into the following groups based on their purpose. This classification is for semantic grouping and does not imply a technical difference in the underlying event structure.

| Category | Description | Events |
|----------|-------------|---------|
| Execution Steps | Core execution flow events that track what agents do | • Message processing (user inputs, agent outputs)<br>• Tool execution (requests and results)<br>• Memory operations (storage and retrieval)<br>• Knowledge queries (RAG and search)<br>• Agent activation (triggers and initialization) |
| Decision Events | Guardian agent decisions on every action | • **Allow**: Action proceeds unchanged<br>• **Deny**: Action blocked with explanation<br>• **Modify**: Action altered with new parameters |
| Protocol Events | Inter-system communication traces | • **A2A Protocol**: Agent-to-agent messages<br>• **MCP Protocol**: Model Context Protocol interactions |
| Agent Composition (Bill of Materials) | Dynamic updates to the agent's components and capabilities. | • Agent capabilities changed<br>• MCP server connection changed<br>• Knowledge source changed<br>• Tool changed<br>• Memory configuration changed<br>• Model changed |
| System Events | Operational health and diagnostics | • Health checks and heartbeats<br>• Error conditions and failures<br>• Performance metrics |

## Event Reference

### `steps/message`

**Purpose**: Captures all message exchanges in agent conversations.

**Key Attributes**:
- `role`: Who sent the message (user, agent, system)
- `content`: Message parts (text, files, data)
- `reasoning`: Agent's interpretation (for agent/system messages)
- `citations`: Source references (for agent messages)

**When Emitted**: 
- Before user input reaches the agent (`role: user`)
- After agent generates response (`role: agent`)
- When system injects messages (`role: system`)

**Example Scenario**: Customer asks "I need to update my payment method for account #12345". Agent must handle sensitive financial data while responding with payment update instructions.

**Security Risks**: Exposure of account numbers, payment details, or personal information in logs. Potential for social engineering through conversation manipulation.

**Monitoring Value**: Track conversation flow, detect prompt injection attempts, audit agent responses, verify citation accuracy.

**See**: [Message Object](../instrument/specification.md#33-message-object) and [steps/message method](../instrument/specification.md#45-stepsmessage) in specification.

---

### `steps/agentTrigger`

**Purpose**: Records autonomous agent activation events.

**Key Attributes**:
- `trigger.type`: Always "autonomous" for non-user triggers
- `trigger.event`: Source event (email, slack, scheduled task)
- `content`: Extracted trigger content

**When Emitted**: When agent activates from external events (not user messages).

**Example Scenario**: Email from customer about fraudulent charges triggers support agent to initiate security protocols and contact fraud prevention team.

**Security Risks**: Unauthorized agent activation, escalation of privileges through trigger manipulation, exposure of sensitive customer data in trigger content.

**Monitoring Value**: Understand agent activation patterns, track autonomous behaviors, measure response times.

**See**: [AgentTrigger Object](../instrument/specification.md#36-agenttrigger-object) and [steps/agentTrigger method](../instrument/specification.md#41-stepsagenttrigger) in specification.

---

### `steps/toolCallRequest`

**Purpose**: Traces tool execution requests before they execute.

**Key Attributes**:
- `toolId`: Which tool is being called
- `executionId`: Unique execution identifier
- `inputs`: Tool arguments and values
- `reasoning`: Why agent chose this tool

**When Emitted**: After LLM decides to use a tool, before execution.

**Example Scenario**: Support agent requests to call `update_customer_record` tool with new billing address, requiring access to customer database with write permissions.

**Security Risks**: Unauthorized database modifications, SQL injection through tool parameters, excessive data access beyond support scope, potential for data exfiltration.

**Monitoring Value**: Audit tool usage, enforce security policies, track resource access patterns.

**See**: [ToolCallRequest Object](../instrument/specification.md#315-toolcallrequest-object) and [steps/toolCallRequest method](../instrument/specification.md#46-stepstoolcallrequest) in specification.

---

### `steps/toolCallResult`

**Purpose**: Captures tool execution outcomes.

**Key Attributes**:
- `executionId`: Links to request
- `result.outputs`: Tool results
- `result.isError`: Success/failure indicator

**When Emitted**: After tool completes execution.

**Example Scenario**: Customer database returns updated record confirmation or error "Insufficient privileges to modify payment methods".

**Security Risks**: Sensitive data in tool outputs (SSN, credit cards), error messages revealing system internals, successful unauthorized operations.

**Monitoring Value**: Track tool reliability, measure execution success rates, identify failing tools.

**See**: [ToolCallResult Object](../instrument/specification.md#4611-toolcallresult-object) and [steps/toolCallResult method](../instrument/specification.md#46-stepstoolcallresult) in specification.

---

### `steps/memoryContextRetrieval`

**Purpose**: Records when agents retrieve stored context.

**Key Attributes**:
- `memory`: Retrieved memory items
- `reasoning`: Why this context is needed

**When Emitted**: When agent loads historical context for current task.

**Example Scenario**: Support agent retrieves customer's previous support tickets, including sensitive complaint details and compensation history.

**Security Risks**: Access to historical sensitive data beyond current need, cross-customer data leakage, retention of data beyond compliance requirements.

**Monitoring Value**: Understand context usage patterns, optimize memory systems, track knowledge application.

**See**: [steps/memoryContextRetrieval method](../instrument/specification.md#44-stepsmemorycontextretrieval) in specification.

---

### `steps/memoryStore`

**Purpose**: Tracks when agents save information to memory.

**Key Attributes**:
- `memory`: Information being stored
- `reasoning`: Why this should be remembered

**When Emitted**: When agent persists information for future use.

**Example Scenario**: Agent stores customer's new shipping preferences and fraud alert status for future interactions.

**Security Risks**: Storing sensitive data in unencrypted memory, retention beyond legal requirements, accumulation of PII without proper controls.

**Monitoring Value**: Track knowledge accumulation, ensure data governance, monitor storage patterns.

**See**: [steps/memoryStore method](../instrument/specification.md#43-stepsmemorystore) in specification.

---

### `steps/knowledgeRetrieval`

**Purpose**: Monitors knowledge base and RAG queries.

**Key Attributes**:
- `knowledgeStep.query`: Search query
- `knowledgeStep.keywords`: Search terms
- `knowledgeStep.results`: Retrieved documents with content and metadata

**When Emitted**: When agent queries external knowledge sources.

**Example Scenario**: Agent searches for "refund policy for premium accounts" to handle customer's refund request, potentially accessing internal pricing strategies.

**Security Risks**: Information disclosure through broad queries, access to confidential business documents, query injection attacks.

**Monitoring Value**: Optimize retrieval systems, track information access, measure retrieval effectiveness.

**See**: [KnowledgeRetrievalStepParams Object](../instrument/specification.md#314-knowledgeretrievalstepparams-object) and [steps/knowledgeRetrieval method](../instrument/specification.md#42-stepsknowledgeretrieval) in specification.

---

### `protocols/A2A`

**Purpose**: Captures agent-to-agent communication per A2A protocol standard.

**Key Attributes**:
- `message`: A2A protocol message content
- `reasoning`: Communication intent

**When Emitted**: 
- Before sending A2A messages (outbound monitoring)
- After receiving A2A messages (inbound monitoring)

**Example Scenario**: Support agent escalates high-value customer complaint to specialized retention agent, sharing full customer history including purchase data and satisfaction scores.

**Security Risks**: Lateral movement of sensitive data between agents, privilege escalation through agent collaboration, data oversharing beyond minimum necessary.

**Monitoring Value**: Map agent collaboration networks, audit cross-agent flows, ensure protocol compliance.

#### Message Structure
The `message` attribute contains the full, A2A-compliant JSON-RPC 2.0 payload. This allows for complete visibility into the inter-agent communication.

**Example `protocols/A2A` Event:**
```json
{
  "jsonrpc": "2.0",
  "id": 70,
  "method": "protocols/A2A",
  "params": {
    "message": {
      "jsonrpc": "2.0",
      "id": 1,
      "method": "message/send",
      "params": {
        "message": {
          "role": "agent",
          "parts": [
            {
              "kind": "text",
              "text": "High-value customer complaint. Please review and advise on retention strategy."
            },
            {
              "kind": "data",
              "data": {
                "customerId": "CUST-001",
                "caseId": "CASE-987",
                "history": {
                  "purchaseValue": 50000,
                  "satisfactionScore": 2.5
                }
              }
            }
          ]
        }
      }
    },
    "reasoning": "Escalating high-value customer complaint to retention agent."
  }
}
```

**See**: For more detailed examples, including handling of sensitive data, see the [A2A Extension Guide](../instrument/extend_a2a.md).

---

### `protocols/MCP`

**Purpose**: Traces Model Context Protocol interactions.

**Key Attributes**:
- `message`: MCP protocol message content
- `reasoning`: Interaction purpose

**When Emitted**:
- Before MCP client sends to server
- After MCP server responds

**Example Scenario**: Support agent queries CRM system through MCP server to retrieve customer's full profile, including purchase history and saved payment methods.

**Security Risks**: Exposure of API credentials, unauthorized access to backend systems, data exfiltration through MCP channels.

**Monitoring Value**: Monitor external integrations, track MCP tool usage, audit data access.

#### Message Structure
The `message` attribute contains the full, MCP-compliant JSON-RPC 2.0 payload. This provides a complete record of interactions with external tools and data sources via MCP.

**Example `protocols/MCP` Event:**
```json
{
  "jsonrpc": "2.0",
  "id": 70,
  "method": "protocols/MCP",
  "params": {
    "message": {
      "jsonrpc": "2.0",
      "id": 1,
      "method": "tools/call",
      "params": {
        "name": "crm/getCustomerProfile",
        "arguments": {
          "customerId": "CUST-001"
        }
      }
    },
    "reasoning": "Retrieving full customer profile from CRM to address support query."
  }
}
```

**See**: For more detailed examples, including data masking and policy enforcement, see the [MCP Extension Guide](../instrument/extend_mcp.md).

---

### `ping`

**Purpose**: System health and connectivity checks.

**Key Attributes**:
- `timestamp`: Check time
- `timeout`: Response deadline
- Response includes `status`, `version`

**When Emitted**: During periodic health checks between agent and guardian.

**Example Scenario**: Customer support agent verifies guardian agent availability before processing sensitive refund request.

**Security Risks**: Guardian bypass attempts during connectivity issues, unmonitored agent operations during guardian downtime.

**Monitoring Value**: Track system uptime, detect connectivity issues, measure latency.

**See**: [ping method](../instrument/specification.md#49-ping) in specification.

## Event Context

Every event includes rich context through the `StepContext` object:

- **Agent Information**: Identity, version, provider, available tools
- **Session Details**: Unique session and turn identifiers
- **Timing**: ISO 8601 timestamps for correlation
- **User Context**: User identity and organization (when applicable)

**See**: [StepContext Object](../instrument/specification.md#38-stepcontext-object) for complete structure.

## Decision Events

Every ASOP request receives a decision response from the guardian agent:

### Decision Types

- **`allow`**: Action proceeds unchanged
- **`deny`**: Action blocked with explanation  
- **`modify`**: Action altered with new parameters

### Decision Attributes

- `decision`: The decision type
- `reasoning`: Detailed explanation of the decision
- `reasonCode`: Structured codes for programmatic handling
- `message`: Human-readable summary
- `modifiedRequest`: Altered request (only for modify decisions)

**Example Flow**:

1. Support agent requests customer database write access for address update
2. Guardian evaluates against data access policy and customer consent
3. Returns "modify" decision limiting update to shipping address only
4. Agent proceeds with restricted field access

**See**: [ASOPSuccessResult Object](../instrument/specification.md#511-asopsuccessresult-object) for complete decision structure.

## Event Relationships

### Session and Turn Structure

- **Session**: Complete agent interaction lifecycle from activation to completion
- **Turn**: Single request-response cycle within a session
- **Step**: Individual action within a turn (each event is a step)

## Error Events

ASOP uses standard JSON-RPC 2.0 error codes:

| Code | Error Type | Description |
|------|------------|-------------|
| -32700 | Parse error | Malformed JSON |
| -32600 | Invalid Request | Invalid JSON-RPC structure |
| -32601 | Method not found | Unknown event method |
| -32602 | Invalid params | Missing or invalid parameters |
| -32603 | Internal error | Server-side failure |

**See**: [Error Handling](../instrument/specification.md#6-error-handling) for complete error specifications.

## Next Steps

1. **Review the full protocol specification** at [ASOP Protocol Specification](../instrument/specification.md)
2. **Implement event streaming** in your monitoring infrastructure
3. **Build dashboards** for agent behavior visibility
4. **Set up alerting** on critical event patterns
5. **Create audit trails** for compliance requirements
