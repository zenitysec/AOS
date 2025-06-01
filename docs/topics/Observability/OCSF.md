# OCSF Integration and Support

The Open Cybersecurity Schema Framework (OCSF) integration enables standardized security event logging for AI agent activities, making them compatible with existing SIEM and security monitoring tools.

## Overview

ASOP maps agent activities to OCSF event classes, providing:
- Standardized security event format
- SIEM compatibility out of the box
- Unified view of agent and traditional security events
- Compliance-ready audit trails

## Event Mapping

### Agent Activity Events

ASOP extends OCSF's API Activity class (6003) for agent-specific events:

```json
{
  "class_uid": 6003,
  "class_name": "API Activity",
  "category_uid": 6,
  "category_name": "Application Activity",
  "activity_id": 1,
  "activity_name": "Agent Tool Use",
  "type_uid": 600301,
  "type_name": "Agent API Call",
  "time": 1706550000000,
  "severity_id": 1,
  "metadata": {
    "version": "1.0.0",
    "product": {
      "name": "ASOP Security Layer",
      "vendor_name": "ASOP"
    },
    "ocsf": {
      "version": "1.0.0"
    },
    "trace_id": "4bf92f3577b34da6a3ce929d0e0e4736",
    "span_id": "00f067aa0ba902b7"
  },
  "actor": {
    "user": {
      "uid": "agent-123",
      "name": "CustomerServiceAgent",
      "type": "AI Agent"
    }
  },
  "api": {
    "service": {
      "name": "MCP Server",
      "version": "2.0.1"
    },
    "operation": "tool_execution"
  },
  "unmapped": {
    "agent_context": {
      "model": "gpt-4",
      "temperature": 0.7,
      "tool_name": "database_query",
      "tool_parameters": {
        "query": "SELECT * FROM customers WHERE id = ?"
      }
    }
  }
}
```

### Security Events

Security-relevant agent activities map to appropriate OCSF classes:

| Agent Activity | OCSF Class | Class UID | Status |
|---------------|------------|-----------|---------|
| Authentication | Authentication | 3002 | Standard OCSF class |
| Authorization Check | Authorization | 3003 | Standard OCSF class |
| Data Access | File Activity | 1001 | Standard OCSF class |
| Network Request | Network Activity | 4001 | Standard OCSF class |
| API Tool Usage | API Activity | 6003 | Standard OCSF class |
| Policy Violation | Security Finding | 2001 | Schema Dependent |

## Implementation

### Event Producer

```python
from asop.ocsf import OCSFEventProducer
from asop.events import AgentEvent

class OCSFAgentLogger:
    def __init__(self, ocsf_version="1.0.0"):
        self.producer = OCSFEventProducer(ocsf_version=ocsf_version)
    
    def log_tool_use(self, event: AgentEvent):
        ocsf_event = self.producer.map_to_ocsf(
            event,
            class_uid=6003,  # API Activity
            activity_name="Agent Tool Use"
        )
        
        # Send to SIEM
        self.send_to_siem(ocsf_event)
```

### Custom Fields

Agent-specific data is preserved in the `unmapped` field following OCSF extension guidelines:

```json
{
  "unmapped": {
    "agent_context": {
      "conversation_id": "conv-456",
      "turn_number": 3,
      "reasoning": "User requested customer information",
      "confidence_score": 0.95
    },
    "ag_bom": {
      "model_version": "gpt-4-0125-preview",
      "tools": ["database_query", "send_email"],
      "capabilities": ["sql", "email"]
    }
  }
}
```

## Integration with SIEMs

### Splunk

```spl
index=security sourcetype=ocsf
| where class_uid=6003 AND type_name="Agent API Call"
| stats count by actor.user.name, unmapped.agent_context.tool_name
```

### Elastic

```json
{
  "query": {
    "bool": {
      "must": [
        { "term": { "class_uid": 6003 }},
        { "term": { "type_name": "Agent API Call" }}
      ]
    }
  },
  "aggs": {
    "by_agent": {
      "terms": { "field": "actor.user.name" }
    }
  }
}
```

## Compliance Mapping

OCSF events support compliance requirements:

| Requirement | OCSF Field | Example |
|------------|------------|---------|
| Who | actor.user | Agent identity |
| What | activity_name | Tool use, API calls |
| When | time | Unix timestamp |
| Where | device, network | Execution environment |
| Why | unmapped.agent_context.reasoning | Agent's explanation |

## Best Practices

### 1. Severity Levels
Use OCSF standard severity values:
- **Informational (1)**: Normal agent operations
- **Low (2)**: Minor policy deviations  
- **Medium (3)**: Unauthorized access attempts
- **High (4)**: Data exfiltration attempts
- **Critical (5)**: System compromise indicators

### 2. Schema Versioning
Always specify OCSF schema version in metadata:
```json
{
  "metadata": {
    "ocsf": {
      "version": "1.0.0"
    }
  }
}
```

### 3. Trace Context Preservation
- Include trace_id and span_id in metadata for workflow correlation
- Link related events across agent workflows
- Use parent_span_id for hierarchical relationships

### 4. Extension Fields
- Use `unmapped` for agent-specific data
- Include AG-BOM data for security context
- Preserve model/tool versions for forensics
- Maintain agent reasoning for audit trails

## Example: Multi-Agent Workflow

When agents collaborate, OCSF events maintain trace context:

```json
[
  {
    "class_uid": 6003,
    "class_name": "API Activity",
    "activity_name": "Agent Request",
    "metadata": {
      "trace_id": "4bf92f3577b34da6a3ce929d0e0e4736",
      "span_id": "00f067aa0ba902b7"
    },
    "actor": { "user": { "name": "PlannerAgent" }},
    "api": {
      "operation": "task_delegation"
    },
    "unmapped": {
      "target_agent": "ExecutorAgent",
      "request_type": "task_delegation"
    }
  },
  {
    "class_uid": 6003,
    "class_name": "API Activity", 
    "activity_name": "Agent Response",
    "metadata": {
      "trace_id": "4bf92f3577b34da6a3ce929d0e0e4736",
      "span_id": "00f067aa0ba902b8",
      "parent_span_id": "00f067aa0ba902b7"
    },
    "actor": { "user": { "name": "ExecutorAgent" }},
    "api": {
      "operation": "task_acceptance"
    },
    "unmapped": {
      "source_agent": "PlannerAgent",
      "response_type": "task_accepted"
    }
  }
]
```

## Schema Considerations

### Standard OCSF Mappings
- **Authentication (3002)**: Standard OCSF class
- **Authorize Session (3003)**: Standard OCSF class  
- **File Activity (1001)**: Standard OCSF class
- **Network Activity (4001)**: Standard OCSF class
- **API Activity (6003)**: Standard OCSF class

### Schema-Dependent Features
- **Security Finding (2001)**: Verify availability in your OCSF schema version
- **Custom severity levels**: Align with your organization's OCSF implementation

---

**Note**: This integration assumes OCSF schema version 1.0.0. Verify class availability and field mappings against your specific OCSF implementation.
