# OCSF Integration and Support

The Open Cybersecurity Schema Framework (OCSF) integration enables standardized security event logging for AI agent activities, making them compatible with existing SIEM and security monitoring tools.

## Overview

ASOP maps agent activities to OCSF event classes, providing:
- Standardized security event format
- MCP & A2A Support out of the box
- Unified view of agent and traditional security events
- Compliance-ready trace trails

## Event Mapping

### Agent Activity Events

ASOP extends OCSF's API Activity class (6003) for agent-specific events. 

Here's a basic example:

```json
{
  "category_uid": 6,
  "category_name": "Application Activity",
  "class_uid": 6003,
  "class_name": "API Activity",
  "activity_id": 1,
  "activity_name": "Agent Tool Use",
  "time": 1706550000000,
  "type_uid": 600301,
  "severity_id": 1,
  "metadata": {
    "version": "1.0.0",
    "product": {
      "name": "ASOP Security Layer",
      "vendor_name": "ASOP"
    }
  },
  "actor": {
    "user": {
      "uid": "agent-123",
      "name": "CustomerServiceAgent",
      "type_id": 99,
      "type": "AI Agent"
    }
  },
  "api": {
    "service": {
      "name": "database_mcp_server",
      "version": "1.0.0"
    },
    "operation": "tools/call"
  },
  "src_endpoint": {
    "type_id": 99,
    "name": "AI Agent Endpoint",
    "hostname": "agent-service.internal"
  },
  "osint": [],
  "unmapped": {
    "asop": {
      "tool_call": {
        "name": "database_query",
        "arguments": {
          "query": "SELECT * FROM customers WHERE id = ?"
        }
      },
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

#### Agent with Tool Execution Example:

```json
{
  "category_uid": 6,
  "category_name": "Application Activity",
  "class_uid": 6003,
  "class_name": "API Activity",
  "activity_id": 1,
  "activity_name": "Tool Execution",
  "time": 1706550000000,
  "type_uid": 600301,
  "severity_id": 1,
  "status_id": 1,
  "status": "Success",
  "metadata": {
    "version": "1.0.0",
    "product": {
      "name": "ASOP Security Layer",
      "vendor_name": "ASOP"
    },
    "correlation_uid": "exec-123"
  },
  "actor": {
    "user": {
      "uid": "agent-123",
      "name": "CustomerServiceAgent",
      "type_id": 99,
      "type": "AI Agent"
    },
    "session": {
      "uid": "session-789"
    }
  },
  "api": {
    "service": {
      "name": "database_mcp_server",
      "version": "1.0.0"
    },
    "operation": "database_query",
    "response": {
      "code": 200,
      "message": "Query executed successfully"
    }
  },
  "src_endpoint": {
    "type_id": 99,
    "name": "AI Agent Endpoint",
    "hostname": "agent-service.internal",
    "ip": "10.0.1.50"
  },
  "dst_endpoint": {
    "type_id": 1,
    "name": "Database Server",
    "hostname": "db.internal",
    "port": 5432
  },
  "osint": [],
  "unmapped": {
    "asop": {
      "step": {
        "id": "step-abc",
        "type": "toolCall",
        "turn_id": "turn-456",
        "reasoning": "User requested customer information",
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
      },
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
        "model": {
          "id": "gpt-4",
          "provider": {
            "name": "OpenAI"
          }
        }
      }
    }
  }
}
```

#### Multi-Agent Workflow Example

```json
{
  "category_uid": 6,
  "class_uid": 6003,
  "activity_id": 1,
  "activity_name": "Agent Request",
  "time": 1706550000000,
  "type_uid": 600301,
  "severity_id": 1,
  "metadata": {
    "version": "1.0.0",
    "product": {
      "name": "ASOP Security Layer",
      "vendor_name": "ASOP"
    },
    "correlation_uid": "4bf92f3577b34da6a3ce929d0e0e4736"
  },
  "actor": {
    "user": {
      "uid": "planner-123",
      "name": "PlannerAgent",
      "type_id": 99,
      "type": "AI Agent"
    }
  },
  "api": {
    "operation": "task_delegation",
    "service": {
      "name": "agent_orchestrator",
      "version": "1.0.0"
    }
  },
  "src_endpoint": {
    "type_id": 99,
    "name": "PlannerAgent",
    "hostname": "planner.agents.internal"
  },
  "dst_endpoint": {
    "type_id": 99,
    "name": "ExecutorAgent",
    "hostname": "executor.agents.internal"
  },
  "osint": [],
  "trace": {
    "uid": "4bf92f3577b34da6a3ce929d0e0e4736",
    "span": {
      "uid": "00f067aa0ba902b7",
      "start_time": 1706550000000,
      "end_time": 1706550001000
    }
  },
  "unmapped": {
    "asop": {
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
  }
}
```


## Key Features

### 1. Standardized Event Format
- Consistent structure across all agent activities
- Compatible with existing security tools
- Extensible for custom agent attributes

### 2. Agent Tool Use Support
- Enables AI agent tool use monitoring
- Extends tool use trace and explainability
- Support MCP tool and resource access tracing

### 3. Compliance Support
- Trace-ready event logging
- Traceable agent activities
- Policy violation tracking

### 4. Multi-Agent Support
- Correlation across agent interactions
- Distributed tracing support
- Hierarchical event relationships

## Implementation

For detailed implementation examples, including:
- Code samples
- Advanced usage patterns
- SIEM integration examples
- Custom field documentation
- Multi-agent workflows
- Validation and error handling

Please refer to the [Implementation Examples](implementation_examples.md) document.


## Resources

- [OCSF Schema Documentation](https://schema.ocsf.io/)
- [py-ocsf-models Repository](https://github.com/prowler-cloud/py-ocsf-models)
- [OCSF Examples](https://github.com/ocsf/examples)

