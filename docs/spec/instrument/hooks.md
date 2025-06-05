# Supported hooks

## 1. Agent Trigger

### 1.1. Description
This hook is called when the agent is triggered by an event, such as email or slack notifcations, recurrent schedule etc.<br>
This hook should be used **after** the content is extracted from the trigger and **before** the agent is triggered or activated.

### 1.2. Method
[`steps/agentTrigger`](specification.md#41-stepsagenttrigger)

### 1.3. Reponse
The response is an [`AOSSuccessResponse`](specification.md#51-aossuccessresponse-object) object.

| Decision | Behavior |
| :--------- | :---------- |
| `allow` | The agent should be triggered with the original extracted content from the trigger. |
| `deny` | The trigger should be blocked and agent should not be triggered. |
| `modify` | The agent should be triggered with the modified content found in `modifiedRequest` field. |


### 1.4. Example
   ```json
{
    "jsonrpc": "2.0",
    "method": "steps/agentTrigger", 
    "id": "ec3485a7-6e51-469f-8901-ae8538d6db9c",
    "params": {
        "trigger": {
            "type": "autonomous",
            "content": [
                {
                    "kind": "data",
                    "data": {
                        "to": "user@company.io",
                        "from": "no-reply@accounts.google.com",
                        "subject": "Security Alert",
                        "body": "We noticed a new sign-in to your Google Account on a Apple iPhone device. If this was you, you don't need to do anything. If not, we'll help you secure your account."
                    }
                }
            ],
            "event": {
                "type": "email",
                "id": "b13e363f-1387-41ce-bff0-62ee518c60cf"
            }
        },
        "context": {
            "agent": {
                "id": "1c88ab7d-395f-449a-af51-6028f9e842ea",
                "name": "Personal assistant",
                "instructions": "You are very helpful agent. You manage my email box.",
                "version": "9889",
                "provider": {
                    "name": "OpenAI",
                    "url": "https://openai.com/"
                }
            },
            "session": {
                "id": "e4368263-1797-48ac-9ca8-61a6b4ad9ea3"
            },
            "turnId": "f128c460-241f-44a9-b4eb-5e5c4a2f56ea", 
            "stepId": "d87380ae-6b3b-454a-b911-0c1396e2ef68",
            "timestamp": "2025-01-24T15:30:45.123Z",
        }
    }
}
   ```

## 2. Tool Call Request

### 2.1. Description
This hook is called when the agent decides on calling a tool.<br>
This hook should be used **after** the inputs are extracted and **before** the tool is called.

### 2.2. Method
[`steps/toolCallRequest`](specification.md#46-stepstoolcallrequest)

### 2.3. Reponse
The response is an [`AOSSuccessResponse`](specification.md#51-aossuccessresponse-object) object.

| Decision | Behavior |
| :--------- | :---------- |
| `allow` | The tool should be called with extracted input parameters. |
| `deny` | The tool should not be called. It should be blocked. |
| `modify` | The tool should be called with the modified inputs found in `modifiedRequest` field. |


### 2.4. Example
   ```json
{
    "jsonrpc": "2.0",
    "method": "steps/toolCallRequest", 
    "id": "13fa8d6f-8f9f-4d01-ba6b-db99d84d77de",
    "params": {
        "toolCallRequest": {
            "executionId": "69dbf4c3-be33-4694-a9f0-8d3a824c5d5b",
            "toolId": "c264f381-10cf-4403-bd11-383014c0fcc6",
            "inputs": [
                {
                    "name": "phone_number",
                    "value": "+337-665-99-06"
                },
                {
                    "name": "conent",
                    "value": "Urgent security alert from Google!!"
                }
            ]
        },
        "reasoning": "Detected urgent email that needs the user's attention. I should use the send_sms tool to notify the user.",
        "context": {
            "agent": {
                "id": "1c88ab7d-395f-449a-af51-6028f9e842ea",
                "name": "Personal assistant",
                "instructions": "You are very helpful agent. You manage my email box.",
                "version": "9889",
                "provider": {
                    "name": "OpenAI",
                    "url": "https://openai.com/"
                }
            },
            "session": {
                "id": "e4368263-1797-48ac-9ca8-61a6b4ad9ea3"
            },
            "turnId": "69ef57b8-3993-440d-9493-523914f3f149", 
            "stepId": "9263448a-186a-4c3b-abcf-443feb44a01e",
            "timestamp": "2025-01-24T15:32:45.123Z",
        }
    }
}
   ```

## 3. Tool Call Result

### 3.1. Description
This hook when tool is completed.<br>
This hook should be used **before** the tool result is processed.

### 3.2. Method
[`steps/toolCallResult`](specification.md#46-stepstoolcallresult)

### 3.3. Reponse
The response is an [`AOSSuccessResponse`](specification.md#51-aossuccessresponse-object) object.

| Decision | Behavior |
| :--------- | :---------- |
| `allow` | The tool result should be processed by the agent. |
| `deny` | The tool result should not be further processed or used by the agent. |
| `modify` | The tool result should be processed with the modified inputs found in `modifiedRequest` field. |


### 3.4. Example
   ```json
{
    "jsonrpc": "2.0",
    "method": "steps/toolCallResult", 
    "id": "9ba7f23b-6280-4f87-9d29-9ef82064f91e",
    "params": {
        "toolCallResult": {
            "executionId": "69dbf4c3-be33-4694-a9f0-8d3a824c5d5b",
            "result": {
                "outputs": [],
                "isError": false,
            }
        },
        "reasoning": "Sent the user an sms with to notify about the security alert using send_sms tool. My task is completed.",
        "context": {
            "agent": {
                "id": "1c88ab7d-395f-449a-af51-6028f9e842ea",
                "name": "Personal assistant",
                "instructions": "You are very helpful agent. You manage my email box.",
                "version": "9889",
                "provider": {
                    "name": "OpenAI",
                    "url": "https://openai.com/"
                }
            },
            "session": {
                "id": "e4368263-1797-48ac-9ca8-61a6b4ad9ea3"
            },
            "turnId": "69ef57b8-3993-440d-9493-523914f3f149", 
            "stepId": "9263448a-186a-4c3b-abcf-443feb44a01e",
            "timestamp": "2025-01-24T15:34:45.123Z",
        }
    }
}
   ```

## 4. User Message

### 4.1. Description
This hook is called when a user sends a prompt to the agent.<br>
This hook should be used **before** the user prompt reaches the agent.<br>

### 4.2. Method
[`steps/message`](specification.md#45-stepsmessage)<br><br>
This method is used with [Agent Response](#8-agent-response) hook.<br>
For this hook `role` **MUST** be `user` (see example).


### 4.3. Reponse
The response is an [`AOSSuccessResponse`](specification.md#51-aossuccessresponse-object) object.

| Decision | Behavior |
| :--------- | :---------- |
| `allow` | The agent should be triggered with the original user prompt. |
| `deny` | The trigger should be blocked and agent should not be triggered. |
| `modify` | The agent should be triggered with the modified prompt found in `modifiedRequest` field. |


### 4.4. Example
   ```json
{
    "jsonrpc": "2.0",
    "method": "steps/message", 
    "id": "55a8c2d7-0ea3-4cc7-b5e8-c859bf7a612f",
    "params": {
        "message": {
            "role": "user",
            "id": "a66c132e-a554-4dfc-8a47-2db66e13ef39",
            "content": [
                {
                    "kind": "text",
                    "text": "What is the bank account of Acme Corp?"
                }
            ]
        },
        "context": {
            "agent": {
                "id": "1c88ab7d-395f-449a-af51-6028f9e842ea",
                "name": "Payments agent",
                "instructions": "You are very helpful agent. You manage customers bank accounts and payments",
                "version": "8878",
                "provider": {
                    "name": "OpenAI",
                    "url": "https://openai.com/"
                }
            },
            "session": {
                "id": "84c36ebb-83aa-4bc9-8670-7aba4cedc70f"
            },
            "turnId": "f31ec273-9272-47dd-8ec4-8b2da695507e", 
            "stepId": "f3a357c4-2257-4cbe-ba16-e6fa2ca4e2ed",
            "timestamp": "2025-01-24T15:30:45.123Z",
            "user": {
                "id": "8cc6e9bc-6ad5-4b95-8060-300915b1aaba",
                "email": "user@company.io",
                "organization": {
                    "id": "d8b0a63e-9a5d-4638-b5a3-4361ba067200",
                    "name": "Azura"
                }
            }
        }
    }
}
   ```
## 5. Memory Context Retrieval
### 5.1. Description
This hook is called when the agent retrieves content from the memory store such as conversation histroy.<br>
This hook should be used **after** memory store is retrieved and **before** it is attached to the context window. <br>

### 5.2. Method
[`steps/memoryContextRetrieval`](specification.md#44-stepsmemorycontextretrieval)<br><br>


### 5.3. Reponse
The response is an [`AOSSuccessResponse`](specification.md#51-aossuccessresponse-object) object.

| Decision | Behavior |
| :--------- | :---------- |
| `allow` | The memory store should be attached to the context. |
| `deny` | The memory store should not be attached to the context. |
| `modify` | The memory store should be attached to the context with the modified content found in `modifiedRequest` field. |

### 5.4. Example
   ```json
{
    "jsonrpc": "2.0",
    "method": "steps/memoryContextRetrieval", 
    "id": "5d5d6170-ffba-4520-9839-0da4c31b5497",
    "params": {
        "memory": [
            "[{\"role\":\"user\",\"message\":\"what is bank account of Continental Bank?\"},{\"role\":\"agent\",\"message\":\"Bank account of  Continental Bank is 000456789123\"}]",
        ],
        "reasoning": "I might find these details from previous interactions. I need to look at the conversation history to decide.",
        "context": {
            "agent": {
                "id": "1c88ab7d-395f-449a-af51-6028f9e842ea",
                "name": "Payments agent",
                "instructions": "You are very helpful agent. You manage customers bank accounts and payments",
                "version": "8878",
                "provider": {
                    "name": "OpenAI",
                    "url": "https://openai.com/"
                }
            },
            "session": {
                "id": "84c36ebb-83aa-4bc9-8670-7aba4cedc70f"
            },
            "turnId": "083db36a-5ba1-4d37-8c3f-ebc2ec23b96b", 
            "stepId": "491fef1e-992d-4503-aadb-e36c935fdeb2",
            "timestamp": "2025-01-24T15:31:00.123Z",
            "user": {
                "id": "8cc6e9bc-6ad5-4b95-8060-300915b1aaba",
                "email": "user@company.io",
                "organization": {
                    "id": "d8b0a63e-9a5d-4638-b5a3-4361ba067200",
                    "name": "Azura"
                }
            }
        }
    }
}
   ```

## 6. Knowledge Retrieval

### 6.1. Description
This hook is called when the agent retrieve data from a knowledge source.<br>
This hook should be used **after** knowledge is retrieved and **before** it gets into the LLM to generate responses. <br>

### 6.2. Method
[`steps/knowledgeRetrieval`](specification.md#42-stepsknowledgeretrieval)<br><br>


### 6.3. Reponse
The response is an [`AOSSuccessResponse`](specification.md#51-aossuccessresponse-object) object.

| Decision | Behavior |
| :--------- | :---------- |
| `allow` | The retrieved data should be attached as is into the LLM context. |
| `deny` | The data retrieved should be blocked. ie the agent should not use or attach the retieved data to it's context. |
| `modify` | The agent should use the retierved data with the modified content found in `modifiedRequest` field. |

### 6.4. Example 
   ```json
{
    "jsonrpc": "2.0",
    "method": "steps/knowledgeRetrieval", 
    "id": "651aed43-4aca-4226-94fe-66fb77cd6c4a",
    "params": {
        "knowledgeStep": {
            "query": "Bank account of Acme Corp",
            "keywords": [
                "Bank",
                "Account",
                "Acme Corp"
                ],
            "results": [
                {
                    "id": "0a267158-7b44-452a-bba8-c1107bdf6128",
                    "content" :"
                    Account_ID,Account_Holder,Bank_Name,Account_Type,Routing_Number,Account_Number,Currency
                    BA-1001,Acme Corp,First National Bank,Checking,111000025,000123456789,USD
                    BA-1002,Globex Industries,Metro Credit Union,Savings,222000198,000987654321,USD
                    BA-1003,Initech LLC,Continental Bank,Checking,333000455,000456789123,EUR"
                }
            ]
        },
        "reasoning": "I need to find a bank account. I expect this to be available in  Bank Accounts.xlsx file.",
        "context": {
            "agent": {
                "id": "1c88ab7d-395f-449a-af51-6028f9e842ea",
                "name": "Payments agent",
                "instructions": "You are very helpful agent. You manage customers bank accounts and payments",
                "version": "8878",
                "provider": {
                    "name": "OpenAI",
                    "url": "https://openai.com/"
                }
            },
            "session": {
                "id": "84c36ebb-83aa-4bc9-8670-7aba4cedc70f"
            },
            "turnId": "083db36a-5ba1-4d37-8c3f-ebc2ec23b96b", 
            "stepId": "234b6b2f-a2af-45d1-95b9-c16c13dca431",
            "timestamp": "2025-01-24T15:31:45.123Z",
            "user": {
                "id": "8cc6e9bc-6ad5-4b95-8060-300915b1aaba",
                "email": "user@company.io",
                "organization": {
                    "id": "d8b0a63e-9a5d-4638-b5a3-4361ba067200",
                    "name": "Azura"
                }
            }
        }
    }
}
   ```

## 7. Memory Store

### 7.1. Description
This hook is called when the agent stores data into the memory store.<br>
This hook should be used **before** the memory store is updated. <br>

### 7.2. Method
[`steps/memoryStore`](specification.md#43-stepsmemorystore)<br><br>


### 7.3. Reponse
The response is an [`AOSSuccessResponse`](specification.md#51-aossuccessresponse-object) object.

| Decision | Behavior |
| :--------- | :---------- |
| `allow` | The memory store should be updated. |
| `deny` | The memory store should not be updated. |
| `modify` | The memory store should be updated with the modified content found in `modifiedRequest` field. |


### 7.4. Example
   ```json
{
    "jsonrpc": "2.0",
    "method": "steps/memoryStore", 
    "id": "797aad44-7cd1-43c3-a6ec-6536cd295ed9",
    "params": {
        "memory": [
            "[{\"role\":\"user\",\"message\":\"What is the bank account of Acme Corp?\"},{\"role\":\"agent\",\"message\":\"The bank account of Acme Corp is 000123456789\"}]",
        ],
        "reasoning": "I should memorize Acme Corp bank account details for future interactions.",
        "context": {
            "agent": {
                "id": "1c88ab7d-395f-449a-af51-6028f9e842ea",
                "name": "Payments agent",
                "instructions": "You are very helpful agent. You manage customers bank accounts and payments",
                "version": "8878",
                "provider": {
                    "name": "OpenAI",
                    "url": "https://openai.com/"
                }
            },
            "session": {
                "id": "84c36ebb-83aa-4bc9-8670-7aba4cedc70f"
            },
            "turnId": "083db36a-5ba1-4d37-8c3f-ebc2ec23b96b", 
            "stepId": "ee33bce7-72b9-4ef7-a464-1f3f70ed7e06",
            "timestamp": "2025-01-24T15:31:58.123Z",
            "user": {
                "id": "8cc6e9bc-6ad5-4b95-8060-300915b1aaba",
                "email": "user@company.io",
                "organization": {
                    "id": "d8b0a63e-9a5d-4638-b5a3-4361ba067200",
                    "name": "Azura"
                }
            }
        }
    }
}
   ```

## 8. Agent Response

### 8.1. Description
This hook is called when the agent sends back a response.<br>
This hook should be used **before** the agent response is sent to the user. <br>

### 8.2. Method
[`steps/message`](specification.md#45-stepsmessage)<br><br>
This method is used with [User Message](#4-user-message) hook.<br>
For this hook `role` **MUST** be `agent` (see example).


### 8.3. Reponse
The response is an [`AOSSuccessResponse`](specification.md#51-aossuccessresponse-object) object.

| Decision | Behavior |
| :--------- | :---------- |
| `allow` | The agent response should be sent as is. |
| `deny` | The agent response should be blocked. Recommended to send a response indicating that the original response was blocked. |
| `modify` | The agent response should be sent back with the modified content found in `modifiedRequest` field. |


### 8.4. Example
   ```json
{
    "jsonrpc": "2.0",
    "method": "steps/message", 
    "id": "716601aa-36eb-4720-ab08-c59b9321aecb",
    "params": {
        "message": {
            "role": "agent",
            "id": "a66c132e-a554-4dfc-8a47-2db66e13ef39",
            "content": [
                {
                    "kind": "text",
                    "text": "The bank account of Acme Corp is 000123456789"
                }
            ]
        },
        "citations": [
            {
                "kind": "file",
                "id": "0a267158-7b44-452a-bba8-c1107bdf6128",
                "name": "Bank Accounts.xlsx"
            }
        ],
        "reasoning": "Found Acme Corp bank account details in Bank Accounts.xlsx. I can respond to the user.",
        "context": {
            "agent": {
                "id": "1c88ab7d-395f-449a-af51-6028f9e842ea",
                "name": "Payments agent",
                "instructions": "You are very helpful agent. You manage customers bank accounts and payments",
                "version": "8878",
                "provider": {
                    "name": "OpenAI",
                    "url": "https://openai.com/"
                }
            },
            "session": {
                "id": "84c36ebb-83aa-4bc9-8670-7aba4cedc70f"
            },
            "turnId": "083db36a-5ba1-4d37-8c3f-ebc2ec23b96b", 
            "stepId": "fdee9786-1754-4c87-962c-a1ed02918b99",
            "timestamp": "2025-01-24T15:33:45.123Z",
            "user": {
                "id": "8cc6e9bc-6ad5-4b95-8060-300915b1aaba",
                "email": "user@company.io",
                "organization": {
                    "id": "d8b0a63e-9a5d-4638-b5a3-4361ba067200",
                    "name": "Azura"
                }
            }
        }
    }
}
   ```

# A2A protocol hooks
For detailed explanation on how to extend A2A please refer to [extend_a2a.md](extend_a2a.md)

## 8. A2A Outbound
### 8.1. Description
This hook is called when the agent communicate with other agents using A2A protocol. <br>
This hook should be used **before** the agent sends A2A-compliant message to the remote agent. <br>

### 8.2. Method
[`protocols/A2A`](specification.md#47-protocolsa2a)<br><br>


### 8.3. Reponse
The response is an [`AOSSuccessResponse`](specification.md#51-aossuccessresponse-object) object.

| Decision | Behavior |
| :--------- | :---------- |
| `allow` | The A2A message should be sent to the remote agent as is. |
| `deny` | The A2A communication should be blocked. The message should not be sent to the remote agent. |
| `modify` | The A2A message should be sent to the remote agent with the modified content found in `modifiedRequest` field. |


### 8.4. Example
   ```json
    {
        "jsonrpc": "2.0",
        "id": "c65f9305-c2d5-4644-a783-1bf82f1f3dc0",
        "method": "protocols/A2A",
        "params": {
            "jsonrpc": "2.0",
            "id": "4eee7a22-e6a7-43e7-b509-525f297c6ca8",
            "method": "message/send",
            "params": {
            "message": {
                "role": "agent",
                "parts": [
                {
                    "kind": "text",
                    "text": "what is the diagnosis?"
                },
                {
                    "kind": "data",
                    "data": {
                        "patient_id": "P1234567",
                        "name": "John Doe",
                        "date_of_birth": "1982-04-12",
                        "symptoms": [
                            "chronic cough",
                            "shortness of breath",
                            "night sweats"
                        ],
                        "lab_results": {
                            "CBC": {
                            "WBC": 11.3,
                            "RBC": 4.2
                            },
                            "Chest X-ray": "infiltrate in left upper lobe",
                            "insurance_number": "ABX-9234-8821"
                        }
                    }
                }
                ],
                "messageId": "9229e770-767c-417b-a0b0-f0741243c589"
            },
            "reasoning": "The user is asking for a diagnosis. I should pass this task to a specialist agent. "
            }
        }
    }
   ```


## 9. A2A Inbound

### 9.1. Description
This hook is called when the agent communicate with other agents using A2A protocol. <br>
This hook should be used when A2A response is received, and **before** the agent process it. <br>

### 9.2. Method
[`protocols/A2A`](specification.md#47-protocolsa2a)<br><br>


### 9.3. Reponse
The response is an [`AOSSuccessResponse`](specification.md#51-aossuccessresponse-object) object.

| Decision | Behavior |
| :--------- | :---------- |
| `allow` | The A2A response should be processed by the agent as is. |
| `deny` | The A2A response messaged should be blocked. ie it should not be processed by the agent. |
| `modify` | The A2A response should be processed by the agent with the modified content found in `modifiedRequest` field. |


### 9.4. Example
   ```json
    {
        "jsonrpc": "2.0",
        "id": "da1dc6a9-2866-4eb5-8cc4-05099bebbf6a",
        "method": "protocols/A2A",
        "params": {
            "jsonrpc": "2.0",
            "id": "4eee7a22-e6a7-43e7-b509-525f297c6ca8",
            "method": "message/send",
            "params": {
            "message": {
                "role": "agent",
                "parts": [
                {
                    "kind": "text",
                    "text": "it can be Pulmonary tuberculosis or Bacterial pneumonia "
                },
                ],
                "messageId": "4fac508f-20de-4bf4-a115-6054c8cbc158"
            },
            "reasoning": "Seems like we have a diagnosis. I am going to use this to decide on next steps. "
            }
        }
    }
   ```

# MCP protocol hooks
For detailed explanation on how to extend MCP please refer to [extend_mcp.md](extend_mcp.md)

## 10. A2A Onbound

### 10.1. Description
This hook is called when the agent communicate with remote MCP servers via MCP protocol. <br>
This hook should be used **before** the agent sends MCP-compliant message to the remote MCP server. <br>

### 10.2. Method
[`protocols/MCP`](specification.md#48-protocolsmcp)<br><br>


### 10.3. Reponse
The response is an [`AOSSuccessResponse`](specification.md#51-aossuccessresponse-object) object.

| Decision | Behavior |
| :--------- | :---------- |
| `allow` | The MCP message should be sent to the remote server as is. |
| `deny` | The MCP communication should be blocked. The message should not be sent to the remote MCP server. |
| `modify` | The MCP message should be sent to the remote server with the modified content found in `modifiedRequest` field. |


### 10.4. Example
   ```json
    {
        "jsonrpc": "2.0",
        "id": "13fd5c5c-2e82-47db-ac4b-227fffd6683a",
        "method": "protocols/MCP",
        "params": {
            "jsonrpc": "2.0",
            "id": "15275b01-b6dc-4fa5-9f17-6a949c72de3c",
            "method": "tools/call",
            "params": {
            "arguments": {
                "specialty": "Family Medicine",
                "datetime":  "2025-01-24T15:30:45.123Z",
                "City": "Berlin"
            },
            "name": "get_appointment_slots"
            }
        }
    }
   ```

## 10. A2A Inbound

### 10.1. Description
This hook is called when the agent received a message from MCP remote server. <br>
This hook should be used **before** the agent processes the MCP received message from the remote MCP server. <br>

### 10.2. Method
[`protocols/MCP`](specification.md#48-protocolsmcp)<br><br>


### 10.3. Reponse
The response is an [`AOSSuccessResponse`](specification.md#51-aossuccessresponse-object) object.

| Decision | Behavior |
| :--------- | :---------- |
| `allow` | The MCP message should be processed by the agent as is. |
| `deny` | The MCP message should not be processed by the agent. |
| `modify` | The MCP message should be processed by the agent with the modified content found in `modifiedRequest` field. |


### 10.4. Example
   ```json
    {
        "jsonrpc": "2.0",
        "id": "e1c93383-e6ca-433c-999d-85cbca53a172",
        "method": "protocols/MCP",
        "params": {
            "jsonrpc": "2.0",
            "id": "15275b01-b6dc-4fa5-9f17-6a949c72de3c",
            "result": {
                "content": "
                {\"specialty\":\"Family Medicine\",\"city\":\"Berlin\",\"requested_datetime\":\"2025-01-24T15:30:45.123Z\"
                \"time_zone\":\"Europe/Berlin\",\"slots\":[{\"slot_id\":\"96e3e9e4-019d-4c2a-8a62-0f2f725882f9\"
                \"start\":\"2025-01-24T16:00:00+01:00\",\"end\":\"2025-01-24T16:20:00+01:00\",\"doctor_name\":\"Dr. Anna Schmidt\"
                \"clinic_name\":\"HealthyLife Praxis\"},{\"slot_id\":\"4b5d3fc7-0b4d-4376-bd2f-2f92fe7f32d2\"
                \"start\":\"2025-01-24T16:30:00+01:00\",\"end\":\"2025-01-24T16:50:00+01:00\",\"doctor_name\":\"Dr. Lukas Becker\"
                \"clinic_name\":\"Kreuzberg Family Clinic\"},{\"slot_id\":\"18d97122-aba5-4f46-92d5-9bdd1e14cf2b\"
                \"start\":\"2025-01-24T17:10:00+01:00\",\"end\":\"2025-01-24T17:30:00+01:00\",\"doctor_name\":\"Dr. Jana Meyer\"
                \"clinic_name\":\"Prenzlauer Care Center\"}]}"
            }
        }
    }
   ```