# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

AOS (Agent Observability Standard) is an industry standard for building secure observable agents. The project aims to make AI agents trustworthy for enterprise adoption by providing:
- **Inspectability**: Know what's inside agents (tools, models, capabilities)
- **Auditability**: Trace what agents did and why with full reasoning chains
- **Instrumentability**: Add controls and apply policies to agent behaviors

## Development Commands

### Documentation Development
```bash
# Serve documentation locally on port 8000
uv run mkdocs serve

# Build documentation static files
uv run mkdocs build
```

### Python Development
```bash
# Install dependencies using UV (preferred)
uv pip install -e .
```

## Architecture Overview

### Core Components
1. **ASOP (Agent Security & Observability Protocol)**: JSON-RPC 2.0 protocol defining interactions between Observed Agents and Guardian Agents
2. **Observability Layer**: Built on OpenTelemetry and OCSF standards for comprehensive agent tracing
3. **AG-BOM (Agent Bill of Materials)**: Dynamic inventory using CycloneDX, SPDX, and SWID standards

### Key Protocol Concepts
- **Observed Agent**: The AI agent being monitored
- **Guardian Agent**: Enforces security policies and observability
- **Step Types**: Message, ToolCall, KnowledgeRetrieval, MemoryStore, AgentTrigger
- **Guardian Actions**: permit, deny, or modify agent operations

### Important Files
- `specification/ASOP/asop_schema.json`: Complete JSON Schema for ASOP protocol
- `docs/topics/ASOP/specification.md`: Detailed protocol specification
- `docs/topics/core_concepts.md`: Fundamental concepts and terminology
- `mkdocs.yml`: Documentation configuration