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
1. Agents using A2A ***must*** use AOS as a transport protocol to deliver A2A messages to the guardian agent using [A2A protocol hooks](hooks.md#a2a-protocol-hooks).
2. Agents using A2A ***must*** understand and enforce AOS responses.



#### The following flow explains how this should be done:
1. Agent **A** prepares A2A-compliant message.
2. Agent **A** uses AOS as a transport to send the message to the guardian agent.
3. The guardian agent understands and processes the A2A transported message and send the result back to agent **A**.
4. Agent **A** interprets and enforces the response from guardian agent.
5. In case response is `allow`, agent **A** sends the A2A message to agent **B**.
6. Agent **B** processes the message and sends back to agent **A** the response.
7. Agent **A** uses AOS as a transport to send the A2A response to the guardian agent.
8. The guardian agent understands and processes the A2A transported response and send the result back to agent **A**.
9. Agent **A** interprets and enforces the response from guardian agent.


## Examples
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
       }
     }
   }
   ```

#### 2. Agent **A** uses ASOP as a transport and sends `protocols/A2A` message 
   ```json
   {
        "jsonrpc": "2.0",
        "id": 70,
        "method": "protocols/A2A",
        "params": {
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
        "method": "protocols/A2A",
        "params": {
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
                "method": "protocols/A2A",
                "params": {
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
                                    "insurance_number": "**********"
                                }
                                }
                            }
                            ],
                            "messageId": "9229e770-767c-417b-a0b0-f0741243c589"
                        }
                    }
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
            }
        }
    }
   ```


#### 2. Agent ***A*** uses ASOP as a transport and sends `message/send` with disallowed content
   ```json
   {
        "jsonrpc": "2.0",
        "id": 100,
        "method": "protocols/A2A",
        "params": {
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
