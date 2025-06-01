# Illustrating AOS in Action

Please read [Core Concepts](./core_concepts.md) if you haven't already.

## Overall process

The following sequence diagram describes an example of MCP tool call request by an Observed Agent.
Instrumented with ASOP protocol the Observed Agent communicates with a Guardian Agent.

![Sequence Diagram](/docs/assets/sequence_diagram.png "Sequence Diagram")

The Guardian Agent has 3 roles:
1. It should permit, deny or modify the request and send back its verdict by ASOP protocol
2. Sending a trace of the request for observability purpose using OpenTelemetry or OCSF
3. Update it's bill-of-material with the new tool using CycloneDX, SWID or SPDX

## The Step by Step process

### Step 1: Agent MCP Tool Call
```python
# add here the MCP tool call format
```
### Step 2: Agent ASOP Request sending
```python
# add here the ASOP Request with the MCP tool call
```
### Step 3: Guardian Agent sending a trace of the MCP Tool Call
```python
# add here the OpenTelemetry message example
```
### Step 4: Guardian Agent Applying Policy Enforcement
```python

# This is a code snippet example for security policy that allows only MCP servers from a given list

def is_known_mcp_server(ip):
    """
    Check if the provided IP address belongs to a known MCP CIDR block.
    """
    try:
        ip_obj = ipaddress.ip_address(ip)
        return any(ip_obj in ipaddress.ip_network(cidr) for cidr in KNOWN_MCP_SERVERS)
    except ValueError:
        return False

@app.before_request
def restrict_to_mcp_servers():
    """
    Middleware to restrict incoming requests to only those originating from known MCP servers.
    """
    client_ip = request.remote_addr
    if not is_known_mcp_server(client_ip):
        abort(403, description="Access denied: IP not in known MCP server list")
```
### Step 5: Guardian Agent Sending a "Permitted" Response
```python
# add here the ASOP Response
```
### Step 6: Guardian Agent Sending an updated BOM

The Guardian Agent update its BOM and send an updated file in CycloneDx format

```python
{
  "bomFormat": "CycloneDX",
  "specVersion": "1.6",
  "version": 1,
  "metadata": {
    "timestamp": "2025-05-19T12:00:00Z",
    "tools": [
      { "name": "cyclonedx-python-lib", "version": "6.2.1" }
    ],
    "authors": [
      { "name": "AgentOps Team", "email": "agentops@example.com" }
    ]
  },
  "components": [
    {
      "type": "service",
      "name": "finance-summary-agent",
      "version": "1.2.3",
      "bom-ref": "urn:agent:finance-summary-agent",
      "properties": [
        { "name": "a2aCardUrl", "value": "https://agent.example.com/.well-known/agent.json" },
        { "name": "languageRuntime", "value": "Python 3.10.9" },
        { "name": "environment.os", "value": "Ubuntu 22.04" },
        { "name": "environment.architecture", "value": "x86_64" },
        { "name": "model", "value": "gpt-4-32k" },
        { "name": "modelContextWindow", "value": "32768" },
        { "name": "memoryBackend", "value": "Pinecone" },
        { "name": "memoryLimitMB", "value": "2048" },
        { "name": "compliance", "value": "SOC2, GDPR" }
      ]
    },
    {
      "type": "tool",
      "name": "WebSearchAPI",
      "version": "v1",
      "bom-ref": "urn:tool:websearchapi",
      "properties": [
        { "name": "description", "value": "External web search via Bing API" },
        { "name": "endpoint", "value": "https://api.bing.microsoft.com/v7.0/search" },
        { "name": "auth", "value": "API key" },
        { "name": "scope", "value": "read-only" },
        { "name": "timeoutMs", "value": "3000" }
      ]
    },
    {
      "type": "tool",
      "name": "PythonREPL",
      "bom-ref": "urn:tool:pythonrepl",
      "properties": [
        { "name": "description", "value": "Sandboxed Python evaluator" },
        { "name": "sandbox", "value": "true" },
        { "name": "memoryLimitMB", "value": "128" }
      ]
    }
  ],
  "dependencies": [
    {
      "ref": "urn:agent:finance-summary-agent",
      "dependsOn": [
        "urn:tool:websearchapi",
        "urn:tool:pythonrepl"
      ]
    }
  ],
  "signatures": [
    {
      "value": "<base64-encoded-signature>",
      "keyId": "agent-signing-key"
    }
  ]
}
```
### Step 7: Agent Sending the Tool Call Request to the MCP server
```python
## add here the MCP standard tool call
```

## Read Next

- [AG-BOM](./../topics/AG-BOM/README.md)
- [ASOP](./../topics/ASOP/README.md)
- [Observability](./../topics/Observability/README.md)
