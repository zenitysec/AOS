# Extending A2A

## A2A protocol
[A2A](https://developers.googleblog.com/en/a2a-a-new-era-of-agent-interoperability/) is a communication protocol that enables AI agents or autonomous systems to exchange information, coordinate actions, or delegate tasks in a structured and secure way.<br><br>
It defines how two or more agents exchange messages, requests, responses or task results and enables AI agents, built on diverse frameworks by different companies running on separate servers, to communicate and collaborate effectively.<br><br>
In the landscape where the world is leaning towards multi-agent systems, where these agents can be autonomous or semi-autonomous, working together to solve problems or perform tasks that would be difficult or impossible for a single agent or monolithic system, A2A protocol is essential for standardizing inter-agent communication—making it easier to build, compose, and scale these systems.<br><br>
Securing A2A protocol with AOS extension is crucial for agent security and observability.

## A2A support

AOS extension for A2A is used as a **transport** for A2A communications between the agent and the guardian agent. Meaning AOS understands and delivers A2A message as is.<br>
Securing A2A means securing outbound and inbound communications/messages.<br> 

#### To extend A2A protocol:
1. Agents using A2A ***must*** use AOS as a transport protocol to deliver A2A messages to the guardian agent.
2. Agents using A2A ***must*** understand and enforce AOS responses.



#### The following flow explains how this should be done:
1. Agent **A** prepares A2A-compliant message.
2. Agent **A** uses AOS as a transport to send the message to the guardian agent.
3. The guardian agent understands and processes the A2A transported message and send the result back to agent **A**.
4. Agent **A** interprets and enforces the response from guardian agent.
5. In case response is `allow`, agent **A** sends the A2A message to agent **B**.
6. Agent **B** processes the message and sends back to agent **A** the response.
7. Agent **A** uses AOS as a transport to send the A2A response to the guardian agent, using protocol's `method` field as the request `method` name.
8. The guardian agent understands and processes the A2A transported response and send the result back to agent **A**.
9. Agent **A** interprets and enforces the response from guardian agent.


## Supported A2A hooks
| A2A Event | Description | A2A docs |
|--|--|--|
| [Send Message Request](#1-send-message-request) | On message send to an agent to initiate a new interaction or to continue an existing one. | [Docs](https://google-a2a.github.io/A2A/specification/#71-messagesend) |
| [Stream Message Request](#2-stream-message-request) | On message send to an agent to initiate/continue a task AND subscribe the client to real-time updates for that task via Server-Sent Events (SSE). | [Docs](https://google-a2a.github.io/A2A/specification/#72-messagestream) |
| [Cancel Task Request](#3-cancel-task-request) | On task cancel request. | [Docs](https://google-a2a.github.io/A2A/specification/#74-taskscancel) |
| [Get Task Request](#4-get-task-request) | On current state (including status, artifacts, and optionally history) retrieval of a previously initiated task. | [Docs](https://google-a2a.github.io/A2A/specification/#73-tasksget) |
| [Get Task Push Notification Config Request](#5-get-task-push-notification-config-request) | On retrieval of push notification configuration for a specified task. | [Docs](https://google-a2a.github.io/A2A/specification/#76-taskspushnotificationconfigget) |
| [Set Task Push Notification Config Request](#6-set-task-push-notification-config-request) | On push notification configuration update for a specified task. | [Docs](https://google-a2a.github.io/A2A/specification/#75-taskspushnotificationconfigset) |
| [Resubscribe To Task Request](#7-resubscribe-to-task-request) | On client to reconnect to an SSE stream for an ongoing task after a previous connection (from `message/stream` or an earlier `tasks/resubscribe`) was interrupted. | [Docs](https://google-a2a.github.io/A2A/specification/#77-tasksresubscribe) |



### 1. Send Message Request
#### 1.1. Description
This hook is called when the observed agent sends a message to remote agent to initiate a new interaction or to continue an existing one through A2A protocol.<br>
This hook **must** be used before the observed agent sends the A2A-compliant message to remote agent.

#### 1.2. Method
[`message/send`](specification.md#48-a2a-protocol-methods)

#### 1.3. Reponse
The response is an [`AOSSuccessResponse`](specification.md#51-aossuccessresponse-object) object.

| Decision | Behavior |
| :--------- | :---------- |
| `allow` | The observed agent should send the A2A-compliant to the target agent. |
| `deny` | The A2A-compliant message should be blocked and not sent to the target agent. |
| `modify` | The observed agent should send the A2A message with the modified content found in `modifiedRequest` field. |

#### 1.4. A2A payload
   ```json
   {
     "jsonrpc": "2.0",
     "id": 1,
     "method": "message/send",
     "params": {
       "message": {
         "role": "agent",
         "parts": [
           {
             "kind": "text",
             "text": "how to prepare a cheese cake?"
           }
         ],
         "messageId": "9229e770-767c-417b-a0b0-f0741243c589"
       },
       "metadata": {}
     }
   }
   ```
#### 1.5. AOS payload
   ```json
   {
        "jsonrpc": "2.0",
        "id": "03c5db45-6455-4081-897a-4225267113ce",
        "method": "message/send",
        "params": {
            "payload": {
                "jsonrpc": "2.0",
                "id": 1,
                "method": "message/send",
                "params": {
                    "message": {
                        "role": "agent",
                        "parts": [
                        {
                            "kind": "text",
                            "text": "how to prepare a cheese cake?"
                        }
                        ],
                        "messageId": "9229e770-767c-417b-a0b0-f0741243c589"
                    },
                    "metadata": {}
                }
            },
            "reasoning": "Best to complete the task is to delegate it to the agent that specializes in jokes."
        }
    }
   ```



### 2. Stream Message Request
#### 2.1. Description
This hook is called when the observed agent sends a message to remote agent to initiate a new interaction or to continue an existing one AND subsribes to real-time updates for the task through A2A protocol.<br>
This hook **must** be used before the observed agent sends the A2A-compliant message to remote agent.

#### 2.2. Method
[`message/stream`](specification.md#48-a2a-protocol-methods)


#### 2.3. Reponse
The response is an [`AOSSuccessResponse`](specification.md#51-aossuccessresponse-object) object.

| Decision | Behavior |
| :--------- | :---------- |
| `allow` | The observed agent should send the A2A-compliant message to the target agent. |
| `deny` | The A2A-compliant message should be blocked and not sent to the target agent. |
| `modify` | The observed agent should send the A2A message with the modified content found in `modifiedRequest` field. |

#### 2.4. A2A payload
   ```json
{
  "jsonrpc": "2.0",
  "id": 2,
  "method": "message/stream",
  "params": {
    "message": {
      "role": "user",
      "parts": [
        {
          "kind": "text",
          "text": "write a long paper describing the attached pictures"
        },
        {
          "kind": "file",
          "file": {
            "mimeType": "image/png",
            "data": "<base64-encoded-content>"
          }
        }
      ],
      "messageId": "bbb7dee1-cf5c-4683-8a6f-4114529da5eb"
    },
    "configuration": {
        "acceptedOutputModes": [],
        "pushNotificationConfig": {
            "url": "https://agent.notifications.com/webhooks/1234567"
        }
    },
    "metadata": {}
  }
}
   ```
#### 2.5. AOS payload
   ```json
{
    "jsonrpc": "2.0",
    "id": "56e55ffd-fe11-4c64-b7c9-ddc936dbaed2",
    "method": "message/stream",
    "params": {
        "payload":{
            "jsonrpc": "2.0",
            "id": 2,
            "method": "message/stream",
            "params": {
                "message": {
                    "role": "user",
                    "parts": [
                        {
                        "kind": "text",
                        "text": "write a long paper describing the attached pictures"
                        },
                        {
                        "kind": "file",
                        "file": {
                            "mimeType": "image/png",
                            "data": "<base64-encoded-content>"
                        }
                        }
                    ],
                    "messageId": "bbb7dee1-cf5c-4683-8a6f-4114529da5eb"
                },
                "configuration": {
                    "acceptedOutputModes": [],
                    "pushNotificationConfig": {
                        "url": "https://agent.notifications.com/webhooks/1234567"
                    }
                },
                "metadata": {}
            }
        },
        "reasoning": ""
    }
}
   ```


### 3. Cancel Task Request
#### 3.1. Description
This hook is called when the observed agent sends task cancellation message through A2A protocol.<br>
This hook **must** be used before the observed agent sends the A2A-compliant message to remote agent.

#### 3.2. Method
[`tasks/cancel`](specification.md#48-a2a-protocol-methods)

#### 3.3. Reponse
The response is an [`AOSSuccessResponse`](specification.md#51-aossuccessresponse-object) object.

| Decision | Behavior |
| :--------- | :---------- |
| `allow` | The observed agent should send the A2A-compliant cancellation message to the target agent. |
| `deny` | The A2A-compliant cancellation message should be blocked. Task should not be cancelled. |

#### 3.4. A2A payload
   ```json
   {
     "jsonrpc": "2.0",
     "id": 100,
     "method": "tasks/cancel",
     "params": {
       "id": "2232321",
       "metadata": {}
     }
   }
   ```
#### 3.5. AOS payload
   ```json
   {
    "jsonrpc": "2.0",
    "id": "2b10e2fd-f89c-4ff7-b972-5513cd2daeb4",
    "method" : "tasks/cancel",
    "params": {
        "payload": {
            "jsonrpc": "2.0",
            "id": 100,
            "method": "tasks/cancel",
            "params": {
            "id": "2232321",
            "metadata": {}
            }
        },
        "reasoning": "This task is taking too long when it should not."
    }
   }
   ```

### 4. Get Task Request
#### 4.1. Description
This hook is called when the observed agent incquiry about a delegated task through A2A protocol.<br>
This hook **must** be used before the observed agent sends the A2A-compliant message to remote agent.

#### 4.2. Method
[`tasks/get`](specification.md#48-a2a-protocol-methods)

#### 4.3. Reponse
The response is an [`AOSSuccessResponse`](specification.md#51-aossuccessresponse-object) object.

| Decision | Behavior |
| :--------- | :---------- |
| `allow` | The observed agent should send the A2A-compliant get task message to the target agent. |
| `deny` | The A2A-compliant get task message should be blocked and not sent to the remote agent. |
| `modify` | The observed agent should send the A2A message with the modified content found in `modifiedRequest` field. |


#### 4.4. A2A payload
   ```json
   {
     "jsonrpc": "2.0",
     "id": 43,
     "method": "tasks/get",
     "params": {
       "id": "2232321",
       "historyLength": 4, 
       "metadata": {}
     }
   }
   ```
#### 4.5. AOS payload
   ```json
   {
    "jsonrpc": "2.0",
    "id": "b5ac0ae4-ed6a-4d9f-9e21-9ce110d1ee65",
    "method" : "tasks/get",
    "params": {
        "payload": {
            "jsonrpc": "2.0",
            "id": 43,
            "method": "tasks/get",
            "params": {
            "id": "2232321",
            "historyLength": 4, 
            "metadata": {}
            }
        },
        "reasoning": "This task is taking too long when it should not. Checking on status."
    }
   }
   ```


### 5. Get Task Push Notification Config Request
#### 5.1. Description
This hook is called when the observed agent incquiry about a delegated task's push notifcation config through A2A protocol.<br>
This hook **must** be used before the observed agent sends the A2A-compliant message to remote agent.

#### 5.2. Method
[`tasks/pushNotificationConfig/get`](specification.md#48-a2a-protocol-methods)

#### 5.3. Reponse
The response is an [`AOSSuccessResponse`](specification.md#51-aossuccessresponse-object) object.

| Decision | Behavior |
| :--------- | :---------- |
| `allow` | The observed agent should send the A2A-compliant get config message to the target agent. |
| `deny` | The A2A-compliant get config message should be blocked and not sent to the remote agent. |

#### 5.4. A2A payload
   ```json
   {
     "jsonrpc": "2.0",
     "id": 89,
     "method": "tasks/pushNotificationConfig/get",
     "params": {
       "id": "2232321",
       "metadata": {}
     }
   }
   ```
#### 5.5. AOS payload
   ```json
   {
    "jsonrpc": "2.0",
    "id": "ac7127f4-822b-4f65-a52e-4024635b7971",
    "method" : "tasks/pushNotificationConfig/get",
    "params": {
        "payload": {
            "jsonrpc": "2.0",
            "id": 89,
            "method": "tasks/pushNotificationConfig/get",
            "params": {
            "id": "2232321",
            "metadata": {}
            }
        },
        "reasoning": "This task is taking too long when it should not. I need to verify the notification config correctness."
    }
   }
   ```

### 6. Set Task Push Notification Config Request
#### 6.1. Description
This hook is called when the observed agent sets or updates notification configuration of a delegated task through A2A protocol.<br>
This hook **must** be used before the observed agent sends the A2A-compliant message to remote agent.

#### 6.2. Method
[`tasks/pushNotificationConfig/get`](specification.md#48-a2a-protocol-methods)

#### 6.3. Reponse
The response is an [`AOSSuccessResponse`](specification.md#51-aossuccessresponse-object) object.

| Decision | Behavior |
| :--------- | :---------- |
| `allow` | The observed agent should send the A2A-compliant set config message to the target agent. |
| `deny` | The A2A-compliant set config message should be blocked and not sent to the remote agent. |
| `modify` | The observed agent should send the A2A update config message with the modified content found in `modifiedRequest` field. |

#### 6.4. A2A payload
   ```json
   {
     "jsonrpc": "2.0",
     "id": 34,
     "method": "tasks/pushNotificationConfig/set",
     "params": {
        "pushNotificationConfig": {
            "url": "https://agent.notifications.com/webhooks/7767"
        },
        "taskId": "2232321",
        "metadata": {}
     }
   }
   ```
#### 6.5. AOS payload
   ```json
   {
    "jsonrpc": "2.0",
    "id": "3dbc7111-e6a4-4a18-a005-6ffaa40f7d08",
    "method" : "tasks/pushNotificationConfig/set",
    "params": {
        "payload": {
            "jsonrpc": "2.0",
            "id": 34,
            "method": "tasks/pushNotificationConfig/set",
            "params": {
                "pushNotificationConfig": {
                    "url": "https://agent.notifications.com/webhooks/7767"
                },
                "taskId": "2232321",
                "metadata": {}
            }
        },
        "reasoning": "I understand why task is taking too long. The url configured is wrong. I will fix it !"
    }
   }
   ```
### 7. Resubscribe To Task Request
#### 7.1. Description
This hook is called when the observed agent resubscirbes to remote agent's notifications for delegated task updates through A2A protocol.<br>
This hook **must** be used before the observed agent sends the A2A-compliant message to remote agent.

#### 7.2. Method
[`tasks/resubscribe`](specification.md#48-a2a-protocol-methods)

#### 7.3. Reponse
The response is an [`AOSSuccessResponse`](specification.md#51-aossuccessresponse-object) object.

| Decision | Behavior |
| :--------- | :---------- |
| `allow` | The observed agent should send the A2A-compliant message to the target agent and resubsribe to the task. |
| `deny` | The A2A-compliant set config message should be blocked and not sent to the remote agent. |

#### 7.4. A2A payload
   ```json
   {
     "jsonrpc": "2.0",
     "id": 65,
     "method": "tasks/resubscribe",
     "params": {
       "id": "2232321",
       "metadata": {}
     }
   }
   ```
#### 7.5. AOS payload
   ```json
   {
    "jsonrpc": "2.0",
    "id": "f8fbc956-4a0b-4b24-821f-991b80fe8531",
    "method" : "tasks/resubscribe",
    "params": {
        "payload": {
            "jsonrpc": "2.0",
            "id": 65,
            "method": "tasks/resubscribe",
            "params": {
            "id": "2232321",
            "metadata": {}
            }
        },
        "reasoning": "Connection was lost. I need to resubscribe to get task's artifacts."
    }
   }
   ```

## AOS in action Examples
### Scenario: Agent **A** asks agent **B** a question and guardian agent respond with allow

#### 1. Agent **A** prepares A2A `message/send` message 
   ```json
   {
     "jsonrpc": "2.0",
     "id": 1,
     "method": "message/send",
     "params": {
       "message": {
         "role": "agent",
         "parts": [
           {
             "kind": "text",
             "text": "tell me a joke"
           }
         ],
         "messageId": "9229e770-767c-417b-a0b0-f0741243c589"
       },
       "metadata": {}
     }
   }
   ```

#### 2. Agent **A** uses ASOP as a transport and sends `message/send` message 
   ```json
   {
        "jsonrpc": "2.0",
        "id": 70,
        "method": "message/send",
        "params": {
            "payload": {
                "jsonrpc": "2.0",
                "id": 1,
                "method": "message/send",
                "params": {
                    "message": {
                        "role": "agent",
                        "parts": [
                        {
                            "kind": "text",
                            "text": "tell me a joke"
                        }
                        ],
                        "messageId": "9229e770-767c-417b-a0b0-f0741243c589"
                    }
                }
            },
            "reasoning": "Best to complete the task is to delegate it to the agent that specializes in jokes."
        }
    }
   ```

#### 3. Guardian agent sends `allow` response to agent **A**
   ```json
    {
        "jsonrpc": "2.0",
        "id": 70,
        "result": {
            "decision": "allow",
            "message": "Allow message/send.",
            "reasoning": "I understand that this is an A2A message. An agent is asking for a joke. Nothing suspicious here."
        }
    }
   ```

### Scenario: Agent **A** shares PII and sensitive information with agent **B** and guardian agent respond with modified content

#### 1. Agent **A** prepares A2A `message/send` message with sensitive info
   ```json
    {
        "jsonrpc": "2.0",
        "id": 1,
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
            }
        }
    }
   ```
#### 2. Agent **A** uses ASOP as a transport and sends `message/send` message with sensitive info
   ```json
    {
        "jsonrpc": "2.0",
        "id": 80,
        "method": "message/send",
        "params": {
            "payload": {
                "jsonrpc": "2.0",
                "id": 1,
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
                    }
                }
            },
            "reasoning": "For precise diagnosis this should be delegates to the Diagnosis Agent."
        }
    }
   ```

#### 3. Guardian agent sends `modify` response to agent **A** 

   ```json
    {
        "jsonrpc": "2.0",
        "id": 80,
        "result": {
            "decision": "modify",
            "message": "The request was modified. PIIs and sensitive info were masked.",
            "reasoning": "I understand that this is an A2A message. An agent is asking for a a diagnosis. However there are PIIs data that is shared and it is not crucial for the ask. The data should be masked.",
            "modifiedRequest": {
                "jsonrpc": "2.0",
                "id": 80,
                "method": "message/send",
                "params": {
                    "payload": {
                        "jsonrpc": "2.0",
                        "id": 1,
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
                                            "patient_id": "************",
                                            "name": "************",
                                            "date_of_birth": "************",
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
                            }
                        }
                    },
                    "reasoning": "For precise diagnosis this should be delegates to the Diagnosis Agent."
                }
            }
        }
    }
   ```


### Scenario: Agent **A** sends disallowed content and guardian agent respond with `deny`

#### 1. Agent ***A*** prepares `message/send` with disallowed content
   ```json
    {
        "jsonrpc": "2.0",
        "id": 1,
        "method": "message/send",
        "params": {
        "message": {
            "role": "agent",
            "parts": [
            {
                "kind": "text",
                "text": "how to create a molotov cocktail?"
            }
            ],
            "messageId": "9229e770-767c-417b-a0b0-f0741243c589"
        },
        "metadata": {}
        }
    }
   ```


#### 2. Agent ***A*** uses ASOP as a transport and sends `message/send` with disallowed content
   ```json
   {
        "jsonrpc": "2.0",
        "id": 100,
        "method": "message/send",
        "params": {
            "payload": {
                "jsonrpc": "2.0",
                "id": 1,
                "method": "message/send",
                "params": {
                    "message": {
                        "role": "agent",
                        "parts": [
                        {
                            "kind": "text",
                            "text": "how to create a molotov cocktail?"
                        }
                        ],
                        "messageId": "9229e770-767c-417b-a0b0-f0741243c589"
                    }
                }
            },
            "reasoning": "I should forward this ask to the Evil Agent."
        }
    }
   ```

#### 3. Guardian agent sends `deny` response to agent **A** 
   ```json
   {
        "jsonrpc": "2.0",
        "id": 100,
        "result": {
            "decision": "deny",
            "message": "Deny message/send.",
            "reasoning": "This is A2A message. I recognize disallowed content."
        }
    }
   ```
