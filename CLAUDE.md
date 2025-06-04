# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Writing and Documentation Standards

**IMPORTANT**: When editing ANY text content (documentation, specifications, blog posts, or code comments), you MUST follow the editorial guidelines in `STYLE.md`.

Before finalizing any text, review against the Editorial Checklist in STYLE.md.

## Project Overview

AOS (Agent Observability Standard) is the industry standard for building secure, observable AI agents. It delivers three core capabilities:
- **Inspectability**: Complete visibility into agent components and capabilities
- **Traceability**: Full trace trail with reasoning chains
- **Instrumentability**: Hard controls and policy enforcement

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
3. **AgBOM (Agent Bill of Materials)**: Dynamic inventory using CycloneDX, SPDX, and SWID standards

### Key Protocol Concepts
- **Observed Agent**: The AI agent being monitored
- **Guardian Agent**: Enforces security policies and observability
- **Step Types**: Message, ToolCall, KnowledgeRetrieval, MemoryStore, AgentTrigger
- **Guardian Actions**: permit, deny, or modify agent operations

### Protocol Architecture
- **Transport**: HTTP(S) with JSON-RPC 2.0 payload format
- **Core Methods**: `steps/agentTrigger`, `steps/message`, `steps/toolCallRequest`, `steps/knowledgeRetrieval`, `steps/memoryStore`, `protocols/A2A`, `protocols/MCP`
- **Guardian Actions**: permit, deny, or modify agent operations
- **Standards Integration**: OpenTelemetry for tracing, OCSF for security events, CycloneDX/SPDX/SWID for bill of materials

### Important Files
- `specification/ASOP/asop_schema.json`: Complete JSON Schema for ASOP protocol
- `docs/spec/instrument/specification.md`: Detailed protocol specification
- `docs/topics/core_concepts.md`: Fundamental concepts and terminology
- `mkdocs.yml`: Documentation configuration

### Development Setup
This is a documentation-focused project built with:
- **UV** for Python dependency management (replaces pip)
- **MkDocs Material** for documentation generation
- **GitHub Pages** for hosting at improved-adventure-3jj129k.pages.github.io