# OCSF Implementation Examples

![Status: Conceptual](https://img.shields.io/badge/Status-Conceptual-purple)

This document provides detailed implementation examples and patterns for ASOP's OCSF integration.

## Dependencies

```bash
pip install py-ocsf-models
```

## Event Producer Implementation

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
                    "asop": {
                        "context": {
                            "agent": {
                                "id": agent_event.get("agent_id"),
                                "name": agent_event.get("agent_name"),
                                "version": agent_event.get("agent_version"),
                                "provider": {
                                    "name": agent_event.get("provider_name"),
                                    "url": agent_event.get("provider_url")
                                }
                            },
                            "session": {
                                "id": agent_event.get("session_id")
                            },
                            "model": {
                                "id": agent_event.get("model_id"),
                                "provider": {
                                    "name": agent_event.get("model_provider")
                                }
                            }
                        },
                        "step": {
                            "id": agent_event.get("step_id"),
                            "type": agent_event.get("step_type"),
                            "turn_id": agent_event.get("turn_id"),
                            "reasoning": agent_event.get("reasoning")
                        }
                    }
                }
            }

            # Add operation details based on step type
            operation = {}
            if agent_event.get("tool_id"):
                operation = {
                    "type": "tool_execution",
                    "tool": {
                        "id": agent_event.get("tool_id"),
                        "execution_id": agent_event.get("execution_id"),
                        "inputs": agent_event.get("tool_inputs", []),
                        "outputs": agent_event.get("tool_outputs", []),
                        "is_error": agent_event.get("is_error", False)
                    }
                }
            elif agent_event.get("protocol_type"):
                operation = {
                    "type": "protocol_message",
                    "protocol": {
                        "type": agent_event.get("protocol_type"),
                        "message": agent_event.get("protocol_message", {}),
                        "reasoning": agent_event.get("protocol_reasoning")
                    }
                }
            elif agent_event.get("memory_type"):
                operation = {
                    "type": "memory_operation",
                    "action": agent_event.get("memory_type"),
                    "content": agent_event.get("memory_content", [])
                }
            elif agent_event.get("knowledge_query"):
                operation = {
                    "type": "knowledge_operation",
                    "query": agent_event.get("knowledge_query"),
                    "keywords": agent_event.get("knowledge_keywords", []),
                    "results": agent_event.get("knowledge_results", [])
                }

            if operation:
                event_data["unmapped"]["asop"]["step"]["operation"] = operation

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
```

## Advanced Usage with Validation

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
        unmapped = event.get("unmapped", {})
        asop = unmapped.get("asop", {})
        context = asop.get("context", {})
        step = asop.get("step", {})
        
        # Validate agent information
        agent = context.get("agent", {})
        agent_required = ["id", "name", "version"]
        missing_agent = [f for f in agent_required if not agent.get(f)]
        if missing_agent:
            raise ValidationError(
                f"Missing required agent fields: {', '.join(missing_agent)}"
            )

        # Validate provider information
        provider = agent.get("provider", {})
        if not provider.get("name"):
            raise ValidationError("Missing required provider name")

        # Validate session context
        if not context.get("session", {}).get("id"):
            raise ValidationError("Missing required session id")

        # Validate step information
        step_required = ["id", "type", "turn_id"]
        missing_step = [f for f in step_required if not step.get(f)]
        if missing_step:
            raise ValidationError(
                f"Missing required step fields: {', '.join(missing_step)}"
            )

        # Validate model information if present
        model = context.get("model")
        if model:
            if not model.get("id"):
                raise ValidationError("Missing required model id")
            if not model.get("provider", {}).get("name"):
                raise ValidationError("Missing required model provider name")

        # Validate operation if present
        operation = step.get("operation", {})
        if operation:
            op_type = operation.get("type")
            if not op_type:
                raise ValidationError("Missing required operation type")

            # Validate based on operation type
            if op_type == "tool_execution":
                tool = operation.get("tool", {})
                tool_required = ["id", "execution_id"]
                missing_tool = [f for f in tool_required if not tool.get(f)]
                if missing_tool:
                    raise ValidationError(
                        f"Missing required tool fields: {', '.join(missing_tool)}"
                    )

            elif op_type == "protocol_message":
                protocol = operation.get("protocol", {})
                if not protocol.get("type"):
                    raise ValidationError("Missing required protocol type")
                if not protocol.get("message"):
                    raise ValidationError("Missing required protocol message")

            elif op_type == "memory_operation":
                if not operation.get("action"):
                    raise ValidationError("Missing required memory operation action")
                if not operation.get("content"):
                    raise ValidationError("Missing required memory operation content")

            elif op_type == "knowledge_operation":
                if not operation.get("query"):
                    raise ValidationError("Missing required knowledge query")
```

## SIEM Integration Examples

### Splunk

```spl
# Count tool usage by agent and tool
index=security sourcetype=ocsf
| where category_uid=6 AND class_uid=6003
| where unmapped.asop.step.operation.type="tool_execution"
| stats count by 
    unmapped.asop.context.agent.name,
    unmapped.asop.step.operation.tool.id
| sort count desc

# Monitor agent activities by step type
index=security sourcetype=ocsf
| where category_uid=6
| stats count by 
    unmapped.asop.context.agent.name,
    unmapped.asop.step.type
| sort count desc

# Track protocol message patterns
index=security sourcetype=ocsf
| where unmapped.asop.step.operation.type="protocol_message"
| stats count by 
    unmapped.asop.step.operation.protocol.type,
    unmapped.asop.step.operation.protocol.message.params.message.action
| sort count desc

# Monitor model usage across agents
index=security sourcetype=ocsf
| stats count by 
    unmapped.asop.context.agent.name,
    unmapped.asop.context.model.id,
    unmapped.asop.context.model.provider.name
| sort count desc
```

### Elasticsearch

```json
{
  "query": {
    "bool": {
      "must": [
        { "term": { "category_uid": 6 }},
        { "term": { "class_uid": 6003 }}
      ]
    }
  },
  "aggs": {
    "by_agent": {
      "terms": {
        "field": "unmapped.asop.context.agent.name.keyword",
        "size": 10
      },
      "aggs": {
        "by_step_type": {
          "terms": {
            "field": "unmapped.asop.step.type.keyword"
          }
        },
        "by_operation_type": {
          "terms": {
            "field": "unmapped.asop.step.operation.type.keyword"
          }
        }
      }
    }
  }
}
```

## Custom Fields Examples

### 1. Basic Agent Event Structure
```json
{
  "unmapped": {
    "asop": {
      "context": {
        "agent": {
          "id": "agent-123",
          "name": "CustomerServiceAgent",
          "version": "1.0.0",
          "provider": {
            "name": "ASOP",
            "url": "https://example.asop"
          }
        },
        "session": {
          "id": "session-789"
        },
        "model": {
          "id": "gpt-4",
          "provider": {
            "name": "OpenAI"
          }
        }
      },
      "step": {
        "id": "step-abc",
        "type": "toolCall",
        "turn_id": "turn-456",
        "reasoning": "User requested customer information"
      }
    }
  }
}
```

### 2. Tool Execution Event
```json
{
  "unmapped": {
    "asop": {
      "step": {
        "operation": {
          "type": "tool_execution",
          "tool": {
            "id": "database_query",
            "execution_id": "exec-123",
            "inputs": [
              {
                "name": "query",
                "value": "SELECT * FROM customers WHERE id = ?"
              }
            ],
            "outputs": [
              {
                "kind": "text",
                "text": "Query executed successfully"
              }
            ],
            "is_error": false
          }
        }
      }
    }
  }
}
```

### 3. Protocol Message Event
```json
{
  "unmapped": {
    "asop": {
      "step": {
        "operation": {
          "type": "protocol_message",
          "protocol": {
            "type": "A2A",
            "message": {
              "jsonrpc": "2.0",
              "method": "message/stream",
              "id": "stream-123",
              "params": {
                "message": {
                  "messageId": "msg-789",
                  "role": "agent",
                  "kind": "message",
                  "parts": [
                    {
                      "kind": "text",
                      "text": "Streaming response..."
                    }
                  ]
                }
              }
            }
          }
        }
      }
    }
  }
}
```

## Multi-Agent Workflow Example

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
      "agent_context": {
        "agent": {
          "id": "planner-123",
          "name": "PlannerAgent",
          "version": "1.0.0",
          "provider": {
            "name": "ASOP",
            "url": "https://example.asop"
          }
        },
        "session": {
          "id": "collab-789"
        },
        "turn": {
          "id": "turn-456"
        },
        "step": {
          "id": "step-abc",
          "type": "protocolMessage"
        },
        "model": {
          "id": "gpt-4",
          "provider": {
            "name": "OpenAI"
          }
        },
        "reasoning": "Task requires specialized database access"
      }
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
      "agent_context": {
        "agent": {
          "id": "executor-456",
          "name": "ExecutorAgent",
          "version": "1.0.0"
        },
        "session": {
          "id": "collab-789"
        },
        "reasoning": "Accepting database query task"
      }
    }
  }
]
```

## Implementation Guidelines

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
- Regular trace of logged data for sensitive information

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

### 6. Validation Best Practices
- Use py-ocsf-models validators for schema compliance
- Implement custom validation for agent-specific fields
- Validate events before sending to SIEM
- Handle validation errors gracefully

## Resources

- [OCSF Schema Documentation](https://schema.ocsf.io/)
- [py-ocsf-models Repository](https://github.com/prowler-cloud/py-ocsf-models)
- [OCSF Examples](https://github.com/ocsf/examples)
