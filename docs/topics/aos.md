# Agent Observability Standard

Core idea: Standardize hooks that agent platforms can support to allow instrumentation of observed agents by a security agent.

Security agents can register hooks on different events including tool use, capability change and agent communication. 
Upon event, the observed agent communicates with the security agent for inspection, and receives a verdict. 
The agent platform is responsible for forcing the observed agent to comply with the verdict. 
A2A is adopted as a communication protocol between the observed and security agents. 
The observed agent can explain its reasoning for taking the action. 
The security agent can approve or deny an action, and demand mutation of action parameters.

Implementation: native support for MCP, A2A and Open Source agent platforms. 
Add one line to leverage these protocols for instrumentation.