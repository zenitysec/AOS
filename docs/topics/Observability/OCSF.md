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
  "category_uid": 6,
  "category_name": "Application Activity",
  "class_uid": 6003,
  "class_name": "API Activity",
  "activity_id": 1,
  "activity_name": "Agent Tool Use",
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
      "tool_name": "database_query",
      "tool_parameters": {
        "query": "SELECT * FROM customers WHERE id = ?"
      },
      "conversation_id": "conv-456",
      "reasoning": "User requested customer information"
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
| Policy Violation | Security Finding | 2001 | Standard OCSF class |

## Implementation (Conceptual)
![Status: Developing](https://img.shields.io/badge/Status-Developing-yellow)

### Dependencies

```bash
pip install py-ocsf-models
```

### Event Producer

```python
from datetime import datetime
from uuid import uuid4
from py_ocsf_models.objects import Event, Activity, Finding
from py_ocsf_models.enums import CategoryType, ClassType, ActivityType
from py_ocsf_models.types import Metadata, Actor, API
from typing import Dict, Any, Optional

class OCSFEvent(Event):
    """Base OCSF event with configuration."""
    class Config:
        allow_population_by_field_name = True

    def __init__(self, **data):
        super().__init__(
            category_uid=CategoryType.APPLICATION_ACTIVITY,
            class_uid=ClassType.API_ACTIVITY,
            **data
        )

class SecurityFinding(Finding):
    """Security finding event with configuration."""
    class Config:
        allow_population_by_field_name = True

    def __init__(self, **data):
        super().__init__(
            category_uid=CategoryType.FINDINGS,
            class_uid=ClassType.SECURITY_FINDING,
            **data
        )

class OCSFAgentLogger:
    """OCSF-compliant logger for AI agent activities."""
    
    def __init__(self, product_name: str = "ASOP Security Layer",
                 vendor_name: str = "ASOP",
                 ocsf_version: str = "1.0.0"):
        self.product_name = product_name
        self.vendor_name = vendor_name
        self.ocsf_version = ocsf_version

    def _create_base_metadata(self) -> Dict[str, Any]:
        """Create base metadata for OCSF events."""
        return {
            "version": "1.0.0",
            "product": {
                "name": self.product_name,
                "vendor_name": self.vendor_name
            },
            "ocsf": {
                "version": self.ocsf_version
            }
        }

    def create_api_activity_event(self, 
                                agent_event: Dict[str, Any],
                                validate: bool = True) -> Dict[str, Any]:
        """Create and validate an OCSF API Activity event."""
        try:
            event_data = {
                "metadata": self._create_base_metadata(),
                "activity_id": ActivityType.CREATE,
                "activity_name": "Agent Tool Use",
                "time": int(datetime.utcnow().timestamp() * 1000),
                "severity_id": agent_event.get("severity_id", 1),
                "actor": {
                    "user": {
                        "uid": agent_event.get("agent_id"),
                        "name": agent_event.get("agent_name"),
                        "type": "AI Agent"
                    }
                },
                "api": {
                    "service": {
                        "name": agent_event.get("service_name"),
                        "version": agent_event.get("service_version")
                    },
                    "operation": agent_event.get("operation")
                },
                "unmapped": {
                    "agent_context": {
                        "model": agent_event.get("model"),
                        "tool_name": agent_event.get("tool_name"),
                        "tool_parameters": agent_event.get("tool_parameters"),
                        "conversation_id": agent_event.get("conversation_id"),
                        "reasoning": agent_event.get("reasoning")
                    }
                }
            }

            # Add trace context if available
            for field in ["trace_id", "span_id", "parent_span_id"]:
                if field in agent_event:
                    event_data["metadata"][field] = agent_event[field]

            if validate:
                event = OCSFEvent(**event_data)
                return event.dict(by_alias=True, exclude_none=True)
            return event_data

        except Exception as e:
            raise ValueError(f"Failed to create OCSF event: {str(e)}")

    def create_security_finding(self, 
                              finding_data: Dict[str, Any],
                              validate: bool = True) -> Dict[str, Any]:
        """Create a security finding event."""
        try:
            event_data = {
                "metadata": self._create_base_metadata(),
                "activity_id": ActivityType.CREATE,
                "activity_name": "Create",
                "time": int(datetime.utcnow().timestamp() * 1000),
                "severity_id": finding_data.get("severity_id", 3),
                "finding": {
                    "title": finding_data.get("title", "Agent Policy Violation"),
                    "desc": finding_data.get("description"),
                    "types": finding_data.get("types", ["Policy Violation"]),
                    "uid": finding_data.get("finding_id", str(uuid4()))
                },
                "resources": [
                    {
                        "type": "AI Agent",
                        "uid": finding_data.get("agent_id"),
                        "name": finding_data.get("agent_name")
                    }
                ],
                "unmapped": {
                    "policy_violated": finding_data.get("policy_name"),
                    "agent_context": finding_data.get("agent_context", {}),
                    "remediation": finding_data.get("remediation")
                }
            }

            if validate:
                event = SecurityFinding(**event_data)
                return event.dict(by_alias=True, exclude_none=True)
            return event_data

        except Exception as e:
            raise ValueError(f"Failed to create security finding: {str(e)}")

    def _send_to_siem(self, ocsf_event: Dict[str, Any]) -> Dict[str, Any]:
        """Send OCSF event to SIEM or logging system."""
        # Implement your SIEM integration here
        return ocsf_event
```

### Advanced Usage with Validation

```python
from py_ocsf_models.validators import EventValidator
from py_ocsf_models.exceptions import ValidationError

class ValidatedOCSFLogger:
    """OCSF logger with strict validation."""
    
    def __init__(self):
        self.validator = EventValidator()
        self.logger = OCSFAgentLogger()
    
    async def create_validated_event(self, 
                                   agent_data: Dict[str, Any]) -> Dict[str, Any]:
        """Create and validate OCSF event with full schema validation."""
        try:
            # Create base event
            raw_event = self.logger.create_api_activity_event(
                agent_data, validate=False
            )
            
            # Validate against OCSF schema
            validated = await self.validator.validate_event(raw_event)
            
            # Additional custom validations
            self._validate_agent_context(validated)
            
            return validated

        except ValidationError as e:
            raise ValueError(f"OCSF validation error: {str(e)}")
        except Exception as e:
            raise ValueError(f"Event creation failed: {str(e)}")
    
    def _validate_agent_context(self, event: Dict[str, Any]) -> None:
        """Validate agent-specific context requirements."""
        context = event.get("unmapped", {}).get("agent_context", {})
        required = ["model", "tool_name", "conversation_id"]
        
        missing = [f for f in required if not context.get(f)]
        if missing:
            raise ValidationError(
                f"Missing required agent context fields: {', '.join(missing)}"
            )
```

### Usage Example

```python
# Initialize loggers
logger = OCSFAgentLogger()
validated_logger = ValidatedOCSFLogger()

# Log a tool usage event
tool_event = await validated_logger.create_validated_event({
    "agent_id": "agent-123",
    "agent_name": "CustomerServiceAgent",
    "tool_name": "database_query",
    "model": "gpt-4",
    "tool_parameters": {"query": "SELECT * FROM customers WHERE id = ?"},
    "conversation_id": "conv-456",
    "reasoning": "User requested customer information",
    "trace_id": "4bf92f3577b34da6a3ce929d0e0e4736",
    "span_id": "00f067aa0ba902b7"
})

# Log a policy violation
violation_event = logger.create_security_finding({
    "agent_id": "agent-123",
    "agent_name": "CustomerServiceAgent",
    "policy_name": "data_access_restriction",
    "description": "Agent attempted to access restricted customer data",
    "severity_id": 3,
    "agent_context": {
        "attempted_action": "read_pii",
        "data_classification": "restricted"
    },
    "remediation": "Access blocked by security policy"
})
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
| where category_uid=6 AND class_uid=6003
| stats count by actor.user.name, unmapped.agent_context.tool_name
| sort count desc
```

### Elasticsearch

```json
{
  "query": {
    "bool": {
      "must": [
        { "term": { "category_uid": 6 }},
        { "term": { "class_uid": 6003 }},
        { "term": { "activity_id": 1 }}
      ]
    }
  },
  "aggs": {
    "by_agent": {
      "terms": { 
        "field": "actor.user.name.keyword",
        "size": 10
      }
    }
  }
}
```

## Key Considerations

### 1. Error Handling
- Always validate events before sending
- Implement proper exception handling
- Log validation failures for debugging
- Include trace context in error reports

### 2. Performance Considerations
- Use async validation for high-volume events
- Implement event batching where appropriate
- Consider caching validated event templates
- Monitor SIEM ingestion performance

### 3. Security Guidelines
- Sanitize all user inputs before event creation
- Implement rate limiting for event generation
- Use secure transport for SIEM communication
- Regular audit of logged data for sensitive information

### 4. Schema Versioning
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

### 5. Trace Context
- Include trace_id and span_id in metadata for workflow correlation
- Link related events across agent workflows
- Use parent_span_id for hierarchical relationships

### 6. Validation
- Use py-ocsf-models validators for schema compliance
- Implement custom validation for agent-specific fields
- Validate events before sending to SIEM
- Handle validation errors gracefully

## Example: Multi-Agent Workflow

When agents collaborate, OCSF events maintain trace context:

```json
[
  {
    "category_uid": 6,
    "class_uid": 6003,
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
    "category_uid": 6,
    "class_uid": 6003,
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

## Resources

- [OCSF Schema Documentation](https://schema.ocsf.io/)
- [py-ocsf-models Repository](https://github.com/prowler-cloud/py-ocsf-models)
- [OCSF Examples](https://github.com/ocsf/examples)

---

**Note**: This integration uses the `py-ocsf-models` package for OCSF compliance. Always verify class availability and field mappings against your specific OCSF implementation.
