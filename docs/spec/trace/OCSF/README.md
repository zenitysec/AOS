# OCSF Integration and Support

The Open Cybersecurity Schema Framework (OCSF) integration enables standardized security event logging for AI agent activities, making them compatible with existing SIEM and security monitoring tools.

## Overview

ASOP maps agent activities to OCSF event classes, providing:
- Standardized security event format
- SIEM compatibility out of the box
- Unified view of agent and traditional security events
- Compliance-ready trace trails

## Event Mapping

### Agent Activity Events

ASOP extends OCSF's API Activity class (6003) for agent-specific events. Here's a basic example:

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
    }
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
      "name": "database_mcp_server",
      "version": "1.0.0"
    },
    "operation": {
      "method": "tools/call",
      "params": {
        "name": "database_query",
        "arguments": {
          "query": "SELECT * FROM customers WHERE id = ?"
        }
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
| Policy Violation | Security Finding | 2001 | Standard OCSF class |

## Key Features

### 1. Standardized Event Format
- Consistent structure across all agent activities
- Compatible with existing security tools
- Extensible for custom agent attributes

### 2. SIEM Integration
- Ready for Splunk, Elasticsearch, and other SIEM platforms
- Built-in support for common security use cases
- Customizable dashboards and alerts

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

---

**Note**: This integration uses the `py-ocsf-models` package for OCSF compliance. Always verify class availability and field mappings against your specific OCSF implementation.
