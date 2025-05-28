# ASOP Protocol Specification

**Version:** `1.0.0`

## 1. Core concepts
- **Guardian Agent**: An agent that monitors other agents behavior for anomalous and risky decisions.
- **Agent**: An agent that implements ASOP-compliant HTTP endpoints, sending data and providing visibility into its plans, reasoning and context. Also understands ASOP responses and enforces results.
- **Session**: A session is a scoped unit of interaction, starting when an agent is activated (by user or environment) and ending when the task or interaction is complete. The session consists of turns, where turn is a single end to end loop between user and agent.
- **Step**: A step is a single unit of action or decision taken by the agent as part of its reasoning or execution process. It can be a user message,  tool/function call, memory operation (retrieve or store context), knowledge retrieval etc. 
- **A2A Message**: A2A-protocol message captured between agents communication.
- **MCP Message**: MCP-protocol message captured between mcp client and mcp server communication.
- **User**: The user involved in agent/user interaction.

## 2. Transport and Format

### 2.1. Transport Protocol

- ASOP communication **MUST** occur over **HTTP(S)**.

### 2.2. Data Format

ASOP uses **[JSON-RPC 2.0](https://www.jsonrpc.org/specification)** as the payload format for all requests and responses

- Agent requests and guardian agent responses **MUST** adhere to the JSON-RPC 2.0 specification.
- The `Content-Type` header for HTTP requests and responses containing JSON-RPC payloads **MUST** be `application/json`.


## 3. Protocol Data Objects
### 3.1 `Agent` Object



| Field Name                          | Type                                                               | Required | Description                                                                                                                                 |
| :---------------------------------- | :----------------------------------------------------------------- | :------- | :------------------------------------------------------------------------------------------------------------------------------------------ |
| `name`                              | `string`                                                           | Yes      | Human-readable name of the agent.                                                                                                           |
| `id`                              | `string`                                                           | Yes      | Id of the agent.                                                                                                           |
| `description`                       | `string`                                                           | No      | Human-readable description.                                                             |
| `instructions`                       | `string`                                                           | Yes      | Agent internal instrucions, known as system prompt.                                                             |
| `url`                               | `string`                                                           | No      | Base URL for the agent's A2A service. Must be absolute. HTTPS for production.                                                               |
| `version`                           | `string`                                                           | Yes      | Agent version string.                                                                                                 |
| `provider`                          | [`AgentProvider`](#311-agentprovider-object)                       | Yes       | Information about the agent's provider.                                                                                                     |
| `model`                           | [`Model`](#312-model-object)                                                           | No      | Agent's underlying LLM.                                                                                                 |
| `tools`                  | [`ToolDefinition`](#32-tooldefinition-object)[]                                                         | No       | Available tools.                                                                                          |
| `mcpServers`                      | [`MCPServer`](#314-mcpserver-object)[]               | No      | Available MCP servers.                                                   |
| `resources`                      | [`Resource`](#315-resource-object)[]               | No      | Available resources.                                                   |
| `organization`                   | [`Organization`](#311-organization-object) | No       | Organization / entity that agent belongs to. |
| `metadata`                   | `Record<string, any>` | No       | Arbitrary key-value metadata associated with the agent. |

#### 3.1.1 `AgentProvider` Object

Information about the organization or entity providing the agent.


| Field Name                          | Type                                                               | Required | Description                                                                                                                                 |
| :---------------------------------- | :----------------------------------------------------------------- | :------- | :------------------------------------------------------------------------------------------------------------------------------------------ |
| `name`                              | `string`                                                           | Yes      | Human-readable name of the agent provider.                                                                                                           |
| `url`                   | `string` | Yes       | URL for the provider's website/contact. |
| `metadata`                   | `Record<string, any>` | No       | Arbitrary key-value metadata associated with the agent provider. |

#### 3.1.2 `Model` Object

Information about LLM associated with the agent.


| Field Name                          | Type                                                               | Required | Description                                                                                                                                 |
| :---------------------------------- | :----------------------------------------------------------------- | :------- | :------------------------------------------------------------------------------------------------------------------------------------------ |
| `name`                              | `string`                                                           | Yes      | Human-readable name of the model (LLM).                                                                                                           |
| `id`                              | `string`                                                           | Yes      | Id of the model (LLM).                                                                                                           |
| `provider`                   | [`LlmProvider`](#313-llmprovider-object) | Yes       | The LLM provider. |
| `metadata`                   | `Record<string, any>` | No       | Arbitrary key-value metadata associated with the model. |


#### 3.1.3 `LlmProvider` Object

Information about the organization or entity providing the LLM.


| Field Name                          | Type                                                               | Required | Description                                                                                                                                 |
| :---------------------------------- | :----------------------------------------------------------------- | :------- | :------------------------------------------------------------------------------------------------------------------------------------------ |
| `name`                              | `string`                                                           | Yes      | Human-readable name of the LLM provider.                                                                                                           |
| `metadata`                   | `Record<string, any>` | No       | Arbitrary key-value metadata associated with the LLM provider. |

#### 3.1.4 `MCPServer` Object

Information about the available MCP servers.


| Field Name                          | Type                                                               | Required | Description                                                                                                                                 |
| :---------------------------------- | :----------------------------------------------------------------- | :------- | :------------------------------------------------------------------------------------------------------------------------------------------ |
| `name`                              | `string`                                                           | Yes      | Name of the MCP server.                                                                                                           |
| `version`                   |  `string`  | Yes       | Version of the MCP server. |

#### 3.1.5 `Resource` Object

Information about the available resources.


| Field Name                          | Type                                                               | Required | Description                                                                                                                                 |
| :---------------------------------- | :----------------------------------------------------------------- | :------- | :------------------------------------------------------------------------------------------------------------------------------------------ |
| `name`                              | `string`                                                           | Yes      | Name of the resource.                                                                                                           |
| `id`                              | `string`                                                           | Yes      | Id of the resource.                                                                                                           |
| `description`                              | `string`                                                           | No      | Resource description.                                                                                                           |
| `content`                              | `string`                                                           | Yes      | Resource content.                                                                                                           |
| `mimeType`                              | `string`                                                            | No      | [MIME type](https://developer.mozilla.org/en-US/docs/Web/HTTP/Basics_of_HTTP/MIME_types) (e.g., text/plain, image/png). Strongly recommended.                                                                                                           |
| `metadata`                   | `Record<string, any>` | No       | Arbitrary key-value metadata associated with the resource. |

### 3.2 `ToolDefinition` Object

Describes the tool schema used by the agent.


| Field Name                          | Type                                                               | Required | Description                                                                                                                                 |
| :---------------------------------- | :----------------------------------------------------------------- | :------- | :------------------------------------------------------------------------------------------------------------------------------------------ |
| `name`                              | `string`                                                           | Yes      | Tool name.                                                                                                           |
| `id`                              | `string`                                                           | Yes      | Tool Id.                                                                                                           |
| `description`                              | `string`                                                           | No      | Tool description. Usually used by the LLM to determine on the tool call request.                                                                                                           |
| `type`                              | `string`                                                           | Yes      | The type of the tool. Such as function_call, api_request etc.                                                                                                           |
| `arguments`                              | [`ToolArgumentDefinition`](#321-toolargumentdefinition-object)[] \| `null`                                                            | Yes      | Array of tool arguments. Can be null if the tool has no arguments.                                                                                                           |
| `outputs`                              | [`ToolOutputDefinition`](#322-tooloutputdefinition-object)[] \| `null`                                                           | Yes      | Array of tool outputs. Can be null if the tool has no outputs.                                                                                                           |
| `metadata`                   | `Record<string, any>` | No       | Arbitrary key-value metadata associated with the LLM provider. |


#### 3.2.1 `ToolArgumentDefinition` Object

Describes the tool argument schema.


| Field Name                          | Type                                                               | Required | Description                                                                                                                                 |
| :---------------------------------- | :----------------------------------------------------------------- | :------- | :------------------------------------------------------------------------------------------------------------------------------------------ |
| `name`                              | `string`                                                           | Yes      | Argument name.                                                                                                           |
| `id`                              | `string`                                                           | No      | Argument Id. This should be used when tool argument can be defined and accessed by Id.                                                                                                          |
| `description`                              | `string`                                                           | No      | Argument description. Usually used by the LLM to determine on argument value.                                                                                                           |
| `type`                              | `string`                                                           | No      | The type of the argument. Allowed values are: `string`, `number`, `boolean`, `object`, `array`, `null`.                                                                                                            |
| `mimeType`                              | `string` \| `null`                                                            | No      | [MIME type](https://developer.mozilla.org/en-US/docs/Web/HTTP/Basics_of_HTTP/MIME_types) (e.g., text/plain, image/png). Strongly recommended.                                                                                                           |
| `required`                              | `boolean`                                                           | Yes      | Whether the tool argument is required.                                                                                                           |

#### 3.2.2 `ToolOutputDefinition` Object

Describes the tool output schema.


| Field Name                          | Type                                                               | Required | Description                                                                                                                                 |
| :---------------------------------- | :----------------------------------------------------------------- | :------- | :------------------------------------------------------------------------------------------------------------------------------------------ |
| `name`                              | `string`                                                           | Yes      | Output name. This should be used when tool output can be defined and accessed by Id.                                                                                                           |
| `id`                              | `string`                                                           | No      | Output Id. This should be used when tool output can be defined and accessed by Id.                                                                                                          |
| `description`                              | `string`                                                           | No      | Output description. Usually used by the LLM to determine on output value.                                                                                                           |
| `type`                              | `string`                                                           | No      | The type of the argument. Allowed values are: `string`, `number`, `boolean`, `object`, `array`, `null`.                                                                                                            |
| `mimeType`                              | `string` \| `null`                                                            | No      | [MIME type](https://developer.mozilla.org/en-US/docs/Web/HTTP/Basics_of_HTTP/MIME_types) (e.g., text/plain, image/png). Strongly recommended.                                                                                                           |


### 3.3 `Message` Object

Represents a single communication turn or a piece of contextual information between a user and an agent.


| Field Name                          | Type                                                               | Required | Description                                                                                                                                 |
| :---------------------------------- | :----------------------------------------------------------------- | :------- | :------------------------------------------------------------------------------------------------------------------------------------------ |
| `id`                              | `string`                                                           | Yes      | Message Id. A unique identifier must be provided for messages in the same session.                                                                                                           |
| `role`                              | `string`                                                           | Yes      | Indicates the sender:  `user`, `agent` or `system`.                                                                                                      |
| `content`            | [`Part[]`](#34-part-union-type) | Yes      | Array of content parts. Must contain at least one part.                          |
| `metadata`                   | `Record<string, any>` | No       | Arbitrary key-value metadata associated with the message. |


### 3.4 `Part` Union Type

Represents a distinct piece of content within a `Message`. A `Part` is a union type representing exportable content as either `TextPart`, `FilePart`, or `DataPart`. All `Part` types also include an optional `metadata` field (`Record<string, any>`) for part-specific metadata.


It **MUST** be one of the following:

#### 3.4.1. `TextPart` Object

For conveying plain textual content.


| Field Name | Type                  | Required | Description                                   |
| :--------- | :-------------------- | :------- | :-------------------------------------------- |
| `kind`     | `"text"` (literal)    | Yes      | Identifies this part as textual content.      |
| `text`     | `string`              | Yes      | The textual content of the part.              |
| `metadata` | `Record<string, any>` | No       | Optional metadata specific to this text part. |

#### 3.4.2. `FilePart` Object

For conveying file-based content.


| Field Name | Type                  | Required    | Description                                   |
| :--------- | :-------------------- | :---------- | :-------------------------------------------- |
| `kind`     | `"file"` (literal)    | Yes         | Identifies this part as file content.         |
| `file`     | `FileWithBytes` \| `FileWithUri` | Yes  | Contains the file details and data/reference. |
| `metadata` | `Record<string, any>` | No          | Optional metadata specific to this file part. |

#### 3.4.3. `DataPart` Object

For conveying structured JSON data. Useful for forms, parameters, or any machine-readable information.


| Field Name | Type                  | Required | Description                                                                 |
| :--------- | :-------------------- | :------- | :-------------------------------------------------------------------------- |
| `kind`     | `"data"` (literal)    | Yes      | Identifies this part as structured data.                                    |
| `data`     | `Record<string, any>` | Yes      | The structured JSON data payload (an object or an array).                   |
| `metadata` | `Record<string, any>` | No       | Optional metadata specific to this data part (e.g., reference to a schema). |

### 3.5.1 `FileWithBytes` Object

Represents the data for a file, used within a `FilePart`.


| Field Name | Type     | Required | Description                                                                                                                         |
| :--------- | :------- | :------- | :---------------------------------------------------------------------------------------------------------------------------------- |
| `name`     | `string` | No       | Original filename (e.g., "report.pdf").                                                                                             |
| `mimeType` | `string` | No       | [MIME type](https://developer.mozilla.org/en-US/docs/Web/HTTP/Basics_of_HTTP/MIME_types) (e.g., `image/png`). Strongly recommended. |
| `bytes`    | `string` | Yes      | Base64 encoded file content.                                                                                                        |

### 3.5.2 `FileWithUri` Object

Represents the URI for a file, used within a `FilePart`.


| Field Name | Type     | Required | Description                                                                                                                         |
| :--------- | :------- | :------- | :---------------------------------------------------------------------------------------------------------------------------------- |
| `name`     | `string` | No       | Original filename (e.g., "report.pdf").                                                                                             |
| `mimeType` | `string` | No       | [MIME type](https://developer.mozilla.org/en-US/docs/Web/HTTP/Basics_of_HTTP/MIME_types) (e.g., `image/png`). Strongly recommended. |
| `uri`      | `string` | Yes      | URI (absolute URL strongly recommended) to file content. Accessibility is context-dependent.                                        |


### 3.6 `AgentTrigger` Object

Represents the trigger that resulted in agent activation.It can be a recurring trigger or responding to an event. <br>
User prompt is not included and it is included in `Message`. 


| Field Name                          | Type                                                               | Required | Description                                                                                                                                 |
| :---------------------------------- | :----------------------------------------------------------------- | :------- | :------------------------------------------------------------------------------------------------------------------------------------------ |
| `type`                              | `string`                                                           | Yes      | Type of the trigger. Allowed values: `autonomous`.                                                                                                          |
| `event`                              | [`AgentTriggerEvent`](#361-agenttriggerevent-object)                                                           | Yes      | The triggering event.                                                                                                  |
| `content`            | [`Part[]`](#34-part-union-type) | Yes      | Array of content parts. Must contain at least one part.                          |
| `metadata`                   | `Record<string, any>` | No       | Arbitrary key-value metadata associated with the trigger. |

#### 3.6.1 `AgentTriggerEvent` Object


Info about triggering event.   


| Field Name                          | Type                                                               | Required | Description                                                                                                                                 |
| :---------------------------------- | :----------------------------------------------------------------- | :------- | :------------------------------------------------------------------------------------------------------------------------------------------ |
| `type`                              | `string`                                                           | Yes      | Type of the event. Examples: email, recurring.                                                                                                           |
| `id`                              | `string`                                                           | Yes      | The Id of the triggering event.                                                                                                  |


### 3.7 `Source` Union Type

Represents a source that is used as a reference or citation in the Agent response (`Message`) to justify or explain the output.<br>
A `Source` is a union type representing the cited source as either `FileSource` or `SiteSource`.


It **MUST** be one of the following:

#### 3.7.1 `FileSource` Object
For conveying file source.


| Field Name                          | Type                                                               | Required | Description                                                                                                                                 |
| :---------------------------------- | :----------------------------------------------------------------- | :------- | :------------------------------------------------------------------------------------------------------------------------------------------ |
| `kind`                              | `"file"` (literal)                                                            | Yes      |  Identifies this source as file source.                                                                                                         |
| `id`                              | `string`                                                           | Yes      | The Id of the file. The Id is identical to the `Resource` id in the list of agent's available resources (`resources`) if found.                                                                                                |
| `name`                              | `string`                                                           | Yes      | The name of the file (e.g., report.pdf).                                                                                                  |
| `url`                              | `string`                                                           | No      | The url of the file if the file is available remotely to the agent. For example url to Sharepoint or GoogleDrive.                                                                                                   |


#### 3.7.1 `SiteSource` Object
For conveying site source.


| Field Name                          | Type                                                               | Required | Description                                                                                                                                 |
| :---------------------------------- | :----------------------------------------------------------------- | :------- | :------------------------------------------------------------------------------------------------------------------------------------------ |
| `kind`                              | `"site"` (literal)                                                            | Yes      |  Identifies this source as file source.                                                                                                         |
| `url`                              | `string`                                                           | Yes      | The url of the referenced site.                                                                                                  |

### 3.8 `StepContext` Object

Holds information about the context of agent step

| Field Name                          | Type                                                               | Required | Description                                                                                                                                 |
| :---------------------------------- | :----------------------------------------------------------------- | :------- | :------------------------------------------------------------------------------------------------------------------------------------------ |
| `agent`                              | [`Agent`](#31-agent-object)                                                           | Yes      | Holds information about the agent.                                                                                                         |
| `session`                              | [`Session`](#39-session-object)                                                           | Yes      | Holds information about the current conversation / interaction with the agent.                                                                                                  |
| `turnId`            | `string` | Yes      | A unique turn Id in the current session.                          |
| `stepId`                   | `string` | Yes       | A unique step Id in the current turn. |
| `timestamp`                   | `string` (ISO 8601) | Yes       | Timestamp (UTC recommended) when this step was recorded. |
| `user`                   | [`User`](#310-user-object) | No       | The user involved with the agent interaction. Exists if the agent was triggered by a user prompt. |


### 3.9 `Session` Object

Holds information about the session.



| Field Name                          | Type                                                               | Required | Description                                                                                                                                 |
| :---------------------------------- | :----------------------------------------------------------------- | :------- | :------------------------------------------------------------------------------------------------------------------------------------------ |
| `id`                              | `string`                                                           | Yes      | A unique identifier of the session.                                                                                                         |
| `metadata`                   | `Record<string, any>` | No       | Arbitrary key-value metadata associated with the session. |

### 3.10 `User` Object

Holds information about the user involved in the interaction with the agent. Used when the agent is triggered by a user prompt.


| Field Name                          | Type                                                               | Required | Description                                                                                                                                 |
| :---------------------------------- | :----------------------------------------------------------------- | :------- | :------------------------------------------------------------------------------------------------------------------------------------------ |
| `id`                              | `string`                                                           | Yes      | User Id.                                                                                                         |
| `name`                              | `string`                                                           | No      | User name.                                                                                                         |
| `email`                              | `string`                                                           | No      | User email.                                                                                                         |
| `organization`                              | [`Organization`](#311-organization-object)                                                           | Yes      | The organization that the user belong to.                                                                                                         |
| `metadata`                   | `Record<string, any>` | No       | Arbitrary key-value metadata associated with the user. |

### 3.11 `Organization` Object

Represents an organization. Used in: `Agent` as the owning organization of the agent, `User` as the organization the user belongs to.


| Field Name                          | Type                                                               | Required | Description                                                                                                                                 |
| :---------------------------------- | :----------------------------------------------------------------- | :------- | :------------------------------------------------------------------------------------------------------------------------------------------ |
| `id`                              | `string`                                                           | Yes      | Organization Id.                                                                                                         |
| `name`                              | `string`                                                           | No      | Human-readable name of the organization.                                                                                                         |
| `metadata`                   | `Record<string, any>` | No       | Arbitrary key-value metadata associated with the organization. |


## 4. Protocol Methods placeholder


## 5. Protocol Errors placeholder


