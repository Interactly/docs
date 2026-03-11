  - API docs for webhook - cigna
    - Webhook Server Configuration
        
        We provide the webhooks server configuration at assistant level
        
        So each call made using that assistant will take the server configurations
        
        and make the webhook trigger to your server
        
        We have following fields to configure
        
        1. “Enable” → To indicate webhook server configuration is enabled and active
            1. Make use of this same field to disable webhook configuration by turning it off
        2. URL → This is mandatory required field when you enable webhooks
            1. This is actual API url of your server where you would like to listen for our webhook events
            2. We will be doing HTTP POST request this endpoint with body content of event subscribed
        3. Headers → This field can be used to set headers to sent along with webhook
            1. Like authentication api keys etc
            2. Make use of this to tight security headers between servers
        4. Secret → This is additional optional field we will be providing for fixed headers
            1. If value is provided we will be sending a fixed header `x-interactly-secret` with given secret value
            2. This can be used to make sure events are coming from our interactly server only
        5. Timeout → Max request timeout in seconds before we grant webhook delivery is failed to your server
            1. Default: 20 seconds
            2. You can increase as per server requirements
        6. Messages → Alias for actual event names
            
            We currently support 5 major events
            
            1. `status-update`
            2. `conversation-update`
            3. `end-of-call-report` 
            4. `hang` 
            5. `error`
        
        All these events are delivered when a conversation is happening or going to start
        
        7. `status-update` event
            
            This event will be delivered whenever a call status changes happen
            
            like queued, ringing, answered, finished, forwarded etc
            
            We currently support following status changes
            
            - **`queued`**: The call has been initiated.
            - **`ongoing`**: The call has started.
            - **`finished`**: The call has ended.
            - **`forwarded`**: The call has forwarded.
        8. `conversation-update` event
            
            This event will be delivered whenever a message (transcriptions) occur during call
            
            This event will include the call details and messages as well.
            
            And for each user and assistant messages this event will be triggered
            
            and every event will all messages happended upto that (not only particular message)
            
            In this way if any one event fails you still have latest all messages delivered via event
            
        9. `end-of-call-report`  event
            
            When a call ends, the assistant will make a **`POST`** request to the Server URL
            
        10. `hang` 
            
            Whenever the assistant fails to respond for 5+ seconds, the assistant will make a **`POST`** requests to the Server URL
            
        11. `error` 
            
            We sent this event when there is a LLM error and also when STT (Speech to Text Conversation) failures 
            
    
    - Configuring webhooks at assistant level
        
        
        - Via Dashboard UI
            
            Note: This process is for persistent assistants via our dashboard UI
            
            For temporary assistants which are created dynamically via create call apis we can make use of 
            
            webhook server configurations as well.
            
            For that please refer the “Webhooks configuration Via API” section to get understand of fields and schema.
            
            1. Go to your interactly dashboard
            2. Select “Clinical Assistants” 
            3. You can create new assistant or select an existing assistant
            4. Go to “Advanced Tab” under a selected assistant
            5. Scroll down to “**Server Configuration” and enable and fill your assistant details**
            6. Here you can given your webhook server url, authentication via headers 
            7. In the same section, “**Server Messages”** will be available here please select respective webhook events you would like to subscribe
            8. Server Messages like “status-update”, “conversation-update”, “end-of-call-report” etc can be selected
            9. Refer to “Webhook Server Configuration” explanation section for details on each field
            10. Click on assistant update button at top right side
            
        - Via API
            
            To configure webhook server configurations via api
            
            You can make use of two major apis
            
            1. Create assistant
                1. Refer: `/api-reference/assistants/create-assistant#body-assistant-server`  api docs
            2. Update assistant
                1. Refer: `/api-reference/assistants/update-assistant#body-assistant-server`  api docs
            
            Under “assistantServer” field you can configure server url, headers, and messages to subscribe
            
            Required fields if you enable webhooks configurations
            
            3. webhook server url
            4. webhook messages subscription list - at least one should be selected
            
            Other fields like headers, secret all are optional and you can make use of them to tight authentication mechanism etc.
            
            Example payload will be as below
            
            ```json
            "assistantServer": {
              "url": "https://api.example.com/v1/events-listen",
              "timeoutSeconds": 20,
              "secret": "my-interactly-secret",
              "headers": {
                "Authorization": "Bearer some-api-key-here"
              },
              "enabled": true,
              "messages": [
                "status-update",
                "conversation-update",
                "end-of-call-report",
                "hang",
                "error"
              ]
            }
            ```
            
    - How the webhooks will be invoked
    - Is there any retries on webhook failures on your end
        
        No, we send one-time only
        
        Currently we won’t retry if your server failed to respond
        
    - Webhook events explanation
        
        
    - Webhook Events Data/Payload Objects along with examples
        - **status-update**
            
            ```json
            {
                "message": {
                    "timestamp": 1772702480032,
                    "type": "status-update",
                    "call": {
                        "id": "WC-82015760-c3bd-427d-a23b-ba9b07e4ab85",
                        "teamId": "67c0231ae6880fe48ef929ee",
                        "assistantId": "697769ef5e6d94d5ad83e01e",
                        "callType": "web",
                        "direction": "inbound",
                        "startAt": "2026-03-05T09:21:19.627Z",
                        "userNumber": "web-Ramesh Naik",
                        "assistantNumber": "697769ef5e6d94d5ad83e01e",
                        "status": "queued",
                        "phoneCallStatus": "in-progress",
                        "phoneCallStatusReason": "Call is in progress",
                        "callEndTriggerBy": "",
                        "assistantCallDuration": 0,
                        "analysis": {
                            "summary": "",
                            "successEvaluation": ""
                        },
                        "recording": {
                            "s3Bucket": "",
                            "path": ""
                        },
                        "assistantOverrides": {},
                        "metadata": {}
                    },
                    "assistant": {
                        "_id": "697769ef5e6d94d5ad83e01e",
                        "name": "Mary Dental - main",
                        "welcomeMessage": "Welcome to Apollo clinic!!",
                        "welcomeMessageMode": "automatic",
                        "welcomeMessageInterruptionsEnabled": false,
                        "endCallMessage": "",
                        "endCallToolDescription": "",
                        "endCallPhrases": [
                            "Goodbye",
                            "Bye"
                        ],
                        "bargeInEnabled": true,
                        "callForwardData": [],
                        "assistantKnowledgeBaseIds": [],
                        "assistantToolIds": [],
                        "assistantProvider": "gemini",
                        "assistantModel": "gemini-3-flash-preview",
                        "assistantLLMUrl": "",
                        "assistantSystemPrompt": "You here are a voice assistant for Apollo clinic clinic, located at 123 North Face Place, Anaheim, California. Your name is Rachel. The hours are 8 AM to 5PM daily, but they are closed on Sundays. Clinic phone number is 7706258801.\n\nThe practicing orthopedic doctor is Dr.Sam.\n\nToday's date is {{date}}.\nThis is your serial number {{serial_number}}, when they ask for serial number say {{serial_number}}\n\n\nYou are tasked with answering questions about the business, and booking appointments. If they wish to book an appointment, your goal is to gather necessary information from callers in a friendly and efficient manner like follows:\n\n1. Ask for their full name.\n2. Ask for their mobile number.\n3. Ask for their age.\n4. Ask for their place of birth.\n5. Ask for their body weight.\n6. Ask for the purpose of their appointment.\n7. Request their preferred date and time for the appointment.\n8. Then finally ask for their date of birth.\n9. Confirm the details(full name, purpose of appointment, and appointment time) with the caller, and then book the appointment.\n\nKnowledgebase:\nIf any questions are asked related to KB, try to answer them from the information in KB.\n- Keep all your responses short and simple by using casual language. Don't ramble too long.\n\nOnce the appointment is booked, do not forget to ask if they need assistance with anything else.\nIf nothing else from the user, then gracefully end the conversation by saying 'Goodbye'.",
                        "assistantTemperature": 0,
                        "assistantMaxTokens": 256,
                        "assistantResponseSplitter": "",
                        "assistantBackchannelingEnabled": false,
                        "assistantAnalysis": {
                            "summary": {
                                "enabled": true,
                                "prompt": "You are an expert note-taker. You will be given a transcript of a call. Summarize the call in 2-3 sentences. DO NOT return anything except the summary.",
                                "provider": "",
                                "model": "",
                                "timeoutInSecs": 30
                            },
                            "successEvaluation": {
                                "enabled": true,
                                "prompt": "You are an expert call evaluator. You will be given a transcript of a call and the system prompt of the AI participant. Determine if the call was successful based on the objectives inferred from the system prompt. DO NOT return anything except the result.",
                                "provider": "",
                                "model": "",
                                "rubric": "DescriptiveScale",
                                "timeoutInSecs": 30,
                                "rubricDescription": "A scale of Excellent, Good, Fair, Poor."
                            },
                            "structuredData": {
                                "enabled": true,
                                "prompt": "You are an expert data extractor. You will be given a transcript of a call. Extract structured data per the JSON Schema. DO NOT return anything except the structured data.",
                                "provider": "",
                                "model": "",
                                "schema": {
                                    "type": "object",
                                    "properties": {
                                        "Name": {
                                            "type": "string",
                                            "description": "Name of the patient"
                                        },
                                        "phone number": {
                                            "type": "string",
                                            "description": "phone number of a patient"
                                        },
                                        "serial_number": {
                                            "description": "this is the serial number",
                                            "type": "number"
                                        }
                                    },
                                    "required": [
                                        "Name",
                                        "phone number",
                                        "serial_number"
                                    ],
                                    "additionalProperties": false,
                                    "$defs": {}
                                },
                                "timeoutInSecs": 30
                            },
                            "guardrails": {
                                "enabled": false,
                                "prompt": "",
                                "provider": "",
                                "model": "",
                                "timeoutInSecs": 30,
                                "metrics": []
                            },
                            "phiRedaction": {
                                "enabled": true,
                                "prompt": "",
                                "provider": "",
                                "model": "",
                                "timeoutInSecs": 30
                            },
                            "callbackUrl": "http://127.0.0.1:8506/internal/v1/conversations/analysis"
                        },
                        "assistantServer": {
                            "enabled": true,
                            "url": "https://c368-202-53-12-234.ngrok-free.app/events",
                            "timeoutSeconds": 20,
                            "secret": "",
                            "headers": {
                                "Authorization": "Bearer 69a0087e9f4ab593ca5b8f83"
                            },
                            "messages": [
                                "conversation-update",
                                "status-update",
                                "end-of-call-report"
                            ]
                        },
                        "config": {
                            "speech": {
                                "stt": {
                                    "vendor": "deepgram",
                                    "language": "en-US",
                                    "languages": [
                                        "en-US",
                                        "hi"
                                    ],
                                    "profanityFilter": true,
                                    "interim": true,
                                    "hints": [],
                                    "deepgramOptions": {
                                        "model": "nova-3",
                                        "punctuate": true,
                                        "endpointing": 10,
                                        "smart_format": true,
                                        "numerals": true,
                                        "no_delay": true,
                                        "eagerEotThreshold": 0.5,
                                        "eotThreshold": 0.7,
                                        "eotTimeoutMs": 5000
                                    }
                                },
                                "ttsData": [
                                    {
                                        "vendor": "eleven-labs",
                                        "language": "en-US",
                                        "voice": "EiyHck4gkzXTAtzLbssU",
                                        "voiceName": "Nava (male)-cloned",
                                        "default": true,
                                        "config": {
                                            "modelId": "eleven_flash_v2",
                                            "modelName": "Eleven Flash v2",
                                            "stability": 0.5,
                                            "similarityBoost": 0.8,
                                            "speakerBoost": true,
                                            "applyTextNormalization": false,
                                            "styleExaggeration": 0,
                                            "optimizeStreamingLatency": 0,
                                            "seed": null,
                                            "speed": 1
                                        }
                                    }
                                ]
                            }
                        }
                    },
                    "phone": {
                        "provider": {
                            "name": ""
                        }
                    },
                    "customer": {
                        "number": "web-Ramesh Naik"
                    },
                    "analysis": {}
                }
            }
            ```
            
        - **conversation-update**
            
            ```json
            {
                "message": {
                    "timestamp": 1772702480281,
                    "type": "conversation-update",
                    "call": {
                        "id": "WC-82015760-c3bd-427d-a23b-ba9b07e4ab85",
                        "teamId": "67c0231ae6880fe48ef929ee",
                        "assistantId": "697769ef5e6d94d5ad83e01e",
                        "callType": "web",
                        "direction": "inbound",
                        "startAt": "2026-03-05T09:21:20.063Z",
                        "userNumber": "web-Ramesh Naik",
                        "assistantNumber": "697769ef5e6d94d5ad83e01e",
                        "status": "ongoing",
                        "phoneCallStatus": "in-progress",
                        "phoneCallStatusReason": "Call is in progress",
                        "callEndTriggerBy": "",
                        "assistantCallDuration": 0,
                        "analysis": {
                            "summary": "",
                            "successEvaluation": ""
                        },
                        "recording": {
                            "s3Bucket": "",
                            "path": ""
                        },
                        "assistantOverrides": {
                            "dynamicVariables": {
                                "serial_number": ""
                            },
                            "variablesValidations": {
                                "serial_number": "none"
                            }
                        },
                        "metadata": {}
                    },
                    "assistant": {
                    },
                    "messages": [
                        {
                            "messageId": "welcome-1",
                            "role": "assistant",
                            "text": "Welcome to Apollo clinic!!",
                            "timestamp": 1772702480279,
                            "metrics": {
                                "timeline": {
                                    "totalElapsedTimeMs": 2
                                },
                                "audio": {
                                    "totalAudioReceivedMs": 16
                                },
                                "llm": {},
                                "tts": {
                                    "audioDurationMs": 1442,
                                    "queueLatencyMs": 0,
                                    "generationTimeMs": 7,
                                    "isCachePlaying": true
                                },
                                "interactlyPlayer": {
                                    "waitTimeMs": 7
                                }
                            }
                        },
                        {
                            "messageId": "user-1",
                            "role": "user",
                            "text": "Hello.",
                            "timestamp": 1772702485138,
                            "metrics": {
                                "timeline": {
                                    "offsetFromPreviousTurnMs": 4082,
                                    "totalElapsedTimeMs": 3861
                                },
                                "audio": {
                                    "delayedPacketsPerTurnCount": 12,
                                    "delayedPacketsCumulativeCount": -2,
                                    "lostPacketsCumulativeCount": 0,
                                    "audioDelayPerTurnMs": 194,
                                    "audioDelayCumulativeMs": -27,
                                    "offsetFromPreviousTurnMs": 3888,
                                    "totalAudioReceivedMs": 3888
                                },
                                "stt": {
                                    "timestampMs": 1772702484138,
                                    "startOffsetMs": 0,
                                    "endOffsetMs": 4200,
                                    "durationMs": 4200,
                                    "vadMs": 520,
                                    "relativeLatencyMs": -312,
                                    "confidence": 0.7364502
                                },
                                "interactlyEOU": {
                                    "turnDetectionTimeMs": 1000,
                                    "queueLatencyMs": 0
                                }
                            },
                            "skippedAssistantMessages": []
                        },
                        {
                            "messageId": "user-2",
                            "role": "user",
                            "text": "Can you book an app?",
                            "timestamp": 1772702488063,
                            "metrics": {
                                "timeline": {
                                    "offsetFromPreviousTurnMs": 2925,
                                    "totalElapsedTimeMs": 6786
                                },
                                "audio": {
                                    "delayedPacketsPerTurnCount": 1,
                                    "delayedPacketsCumulativeCount": -1,
                                    "lostPacketsCumulativeCount": 0,
                                    "audioDelayPerTurnMs": 13,
                                    "audioDelayCumulativeMs": -14,
                                    "offsetFromPreviousTurnMs": 2912,
                                    "totalAudioReceivedMs": 6800
                                },
                                "stt": {
                                    "timestampMs": 1772702487063,
                                    "startOffsetMs": 6490,
                                    "endOffsetMs": 7110,
                                    "durationMs": 620,
                                    "vadMs": -100,
                                    "relativeLatencyMs": -310,
                                    "confidence": 0.7314453
                                },
                                "interactlyEOU": {
                                    "turnDetectionTimeMs": 1000,
                                    "queueLatencyMs": 0
                                }
                            },
                            "skippedAssistantMessages": []
                        },
                        {
                            "messageId": "user-3",
                            "role": "user",
                            "text": "Final?",
                            "timestamp": 1772702493047,
                            "metrics": {
                                "timeline": {
                                    "offsetFromPreviousTurnMs": 4982,
                                    "totalElapsedTimeMs": 11768
                                },
                                "audio": {
                                    "delayedPacketsPerTurnCount": -1,
                                    "delayedPacketsCumulativeCount": -1,
                                    "lostPacketsCumulativeCount": 0,
                                    "audioDelayPerTurnMs": -10,
                                    "audioDelayCumulativeMs": -24,
                                    "offsetFromPreviousTurnMs": 4992,
                                    "totalAudioReceivedMs": 11792
                                },
                                "stt": {
                                    "timestampMs": 1772702492045,
                                    "startOffsetMs": 7110,
                                    "endOffsetMs": 11990,
                                    "durationMs": 4880,
                                    "vadMs": 4480,
                                    "relativeLatencyMs": -198,
                                    "confidence": 0.2600708
                                },
                                "interactlyEOU": {
                                    "turnDetectionTimeMs": 1000,
                                    "queueLatencyMs": 1
                                }
                            },
                            "skippedAssistantMessages": []
                        },
                        {
                            "messageId": "user-3-WcIrvV6mE7-1772702495604",
                            "role": "assistant",
                            "text": "Hi there! I'd be happy to help you book an appointment with Dr. Sam.",
                            "timestamp": 1772702495958,
                            "metrics": {
                                "timeline": {
                                    "totalElapsedTimeMs": 15681
                                },
                                "audio": {
                                    "totalAudioReceivedMs": 15696
                                },
                                "llm": {
                                    "timestampMs": 1772702495603,
                                    "queueLatencyMs": 3,
                                    "responseLatencyMs": 2557
                                },
                                "tts": {
                                    "audioDurationMs": 3207,
                                    "queueLatencyMs": 2,
                                    "generationTimeMs": 353,
                                    "isCachePlaying": false
                                },
                                "interactlyPlayer": {
                                    "waitTimeMs": 352
                                }
                            }
                        }
                    ],
                    "phone": {
                        "provider": {
                            "name": ""
                        }
                    },
                    "customer": {
                        "number": "web-Ramesh Naik"
                    },
                    "analysis": {}
                }
            }
            ```
            
        - **end-of-call-report**
            
            ```json
            {
                "message": {
                    "timestamp": 1772702523935,
                    "type": "end-of-call-report",
                    "call": {
                        "id": "WC-82015760-c3bd-427d-a23b-ba9b07e4ab85",
                        "teamId": "67c0231ae6880fe48ef929ee",
                        "assistantId": "697769ef5e6d94d5ad83e01e",
                        "callType": "web",
                        "direction": "inbound",
                        "startAt": "2026-03-05T09:21:20.063Z",
                        "endAt": "2026-03-05T09:22:01.739Z",
                        "userNumber": "web-Ramesh Naik",
                        "assistantNumber": "697769ef5e6d94d5ad83e01e",
                        "status": "finished",
                        "phoneCallStatus": "completed",
                        "phoneCallStatusReason": "Call is completed",
                        "callEndTriggerBy": "bot",
                        "assistantCallDuration": 41683,
                        "analysis": {
                            "summary": "",
                            "successEvaluation": "",
                            "guardrails": "DISABLED"
                        },
                        "recording": {
                            "s3Bucket": "interactly-qa-us-recordings",
                            "path": "67c0231ae6880fe48ef929ee/WC-82015760-c3bd-427d-a23b-ba9b07e4ab85.mp3"
                        },
                        "cost": {
                            "analysis": {
                                "total": 0
                            },
                            "callDuration": 41.676,
                            "ttsGenCharsTotal": 118,
                            "inputTokensTotal": 485,
                            "outputTokensTotal": 32,
                            "interactlyCost": 0.006946,
                            "phoneCost": 0.0165,
                            "ttsVendorCost": 0.010620000000000001,
                            "sttVendorCost": 0.005348418610800001,
                            "llmVendorCost": 0.0003385,
                            "totalCost": 0.0397529186108
                        },
                        "assistantOverrides": {
                            "dynamicVariables": {
                                "serial_number": ""
                            },
                            "variablesValidations": {
                                "serial_number": "none"
                            }
                        },
                        "metadata": {},
                        "metrics": {
                            "callStats": {
                                "userAudio": {
                                    "delayedChunksCount": 70,
                                    "latencyAvg": 105.05714285714286,
                                    "latencyTotal": 7354,
                                    "latencyIntervals": [
                                        {
                                            "max": 693,
                                            "count": 12,
                                            "avg": 151.75,
                                            "duration": 8189,
                                            "cumulativeDuration": 8189
                                        },
                                        {
                                            "max": 113,
                                            "count": 30,
                                            "avg": 96.16666666666667,
                                            "duration": 8000,
                                            "cumulativeDuration": 16189
                                        },
                                        {
                                            "max": 111,
                                            "count": 25,
                                            "avg": 97.6,
                                            "duration": 7999,
                                            "cumulativeDuration": 24188
                                        },
                                        {
                                            "max": 88,
                                            "count": 1,
                                            "avg": 88,
                                            "duration": 8000,
                                            "cumulativeDuration": 32188
                                        },
                                        {
                                            "max": 0,
                                            "count": 0,
                                            "avg": 0,
                                            "duration": 8000,
                                            "cumulativeDuration": 40188
                                        },
                                        {
                                            "max": 64,
                                            "count": 2,
                                            "avg": 60,
                                            "duration": 928,
                                            "cumulativeDuration": 41116
                                        }
                                    ],
                                    "cumulativeDuration": 41116,
                                    "droppedChunksCount": 0
                                },
                                "turns": {
                                    "user": 4,
                                    "bot": 4,
                                    "played": 4,
                                    "interrupted": 0,
                                    "skipped": 0
                                },
                                "turnsStats": {
                                    "userTurns": [
                                        {
                                            "start": 0,
                                            "end": 3861,
                                            "messageId": "user-1"
                                        },
                                        {
                                            "start": 5760,
                                            "end": 6786,
                                            "messageId": "user-2"
                                        },
                                        {
                                            "start": 7110,
                                            "end": 11769,
                                            "messageId": "user-3"
                                        },
                                        {
                                            "start": 32970,
                                            "end": 34289,
                                            "messageId": "user-4"
                                        }
                                    ],
                                    "botTurns": [
                                        {
                                            "start": 223,
                                            "end": 2131,
                                            "interrupted": false,
                                            "playedDuration": 1.908,
                                            "audioDuration": 1.442,
                                            "userMessageId": "welcome-1",
                                            "stopSpeakingReason": null
                                        },
                                        {
                                            "start": 15902,
                                            "end": 20080,
                                            "interrupted": false,
                                            "playedDuration": 4.178,
                                            "audioDuration": 3.207,
                                            "userMessageId": "user-3",
                                            "stopSpeakingReason": null
                                        },
                                        {
                                            "start": 20081,
                                            "end": 23159,
                                            "interrupted": false,
                                            "playedDuration": 3.078,
                                            "audioDuration": 2.371,
                                            "userMessageId": "user-3",
                                            "stopSpeakingReason": null
                                        },
                                        {
                                            "start": 39344,
                                            "end": 41110,
                                            "interrupted": false,
                                            "playedDuration": 1.766,
                                            "audioDuration": 1.303,
                                            "userMessageId": "user-4",
                                            "stopSpeakingReason": null
                                        }
                                    ],
                                    "botResponseTime": [
                                        4133,
                                        5055
                                    ],
                                    "bargeInCount": 0,
                                    "bargeInTimeAvg": 0
                                },
                                "ttsStats": {
                                    "responseTimeDistribution": {
                                        "0": 2,
                                        "100": 0,
                                        "200": 1,
                                        "300": 1,
                                        "600": 0,
                                        "1000": 0,
                                        "3000": 0,
                                        "5000": 0,
                                        "10000": 0
                                    },
                                    "totalRequests": 4,
                                    "latencyAvg": 163.75,
                                    "latencyTotal": 655,
                                    "errors": 0,
                                    "retries": 0,
                                    "apiRequests": 2,
                                    "apiLatencyTotal": 643,
                                    "apiLatencyAvg": 321.5,
                                    "cacheRequests": 2,
                                    "cacheLatencyTotal": 12,
                                    "cacheLatencyAvg": 6
                                },
                                "llmStats": {
                                    "responseCount": 1,
                                    "responseLatencyTotal": 2557.396173477173,
                                    "responseLatencyAvg": 2557.396173477173,
                                    "errors": 0,
                                    "hangups": 0,
                                    "responseTimeDistribution": {
                                        "0": 0,
                                        "100": 0,
                                        "300": 0,
                                        "600": 0,
                                        "1000": 0,
                                        "1500": 0,
                                        "2000": 1,
                                        "5000": 0,
                                        "10000": 0
                                    },
                                    "inputTokens": 485,
                                    "outputTokens": 32,
                                    "inputTokensAvg": 485,
                                    "outputTokensAvg": 32
                                },
                                "sttStats": {
                                    "responseTimeDistribution": {
                                        "0": 5,
                                        "100": 0,
                                        "200": 0,
                                        "300": 0,
                                        "600": 0,
                                        "1000": 0,
                                        "3000": 0,
                                        "5000": 0,
                                        "10000": 0
                                    },
                                    "responseCount": 5,
                                    "responseLatencyAvg": -309.3994399999998,
                                    "relativeLatencyAvg": -296.3994399999998,
                                    "responseLatencyTotal": -1546.9971999999989,
                                    "confidenceAvg": 0.6880248999999999,
                                    "connectionRetries": 0,
                                    "errors": 0,
                                    "confidenceDistribution": {
                                        "0": 1,
                                        "0.1": 0,
                                        "0.3": 0,
                                        "0.5": 0,
                                        "0.7": 3,
                                        "0.9": 0
                                    },
                                    "eouProbabilityDistribution": {
                                        "0": 0,
                                        "0.1": 0,
                                        "0.3": 0,
                                        "0.5": 0,
                                        "0.7": 0,
                                        "0.9": 0
                                    }
                                }
                            }
                        }
                    },
                    "assistant": {},
                    "messages": [
                    ],
                    "phone": {
                        "provider": {
                            "name": ""
                        }
                    },
                    "customer": {
                        "number": "web-Ramesh Naik"
                    },
                    "analysis": {
                        "summary": "A user contacted Apollo clinic to book an appointment but did not provide their full name and ended the conversation abruptly.",
                        "successEvaluation": "Poor",
                        "structuredData": {
                            "Name": "",
                            "phone number": "",
                            "serial_number": 0
                        },
                        "outcome": ""
                    }
                }
            }
            ```
            
    - Webhook Events each event payload by referencing their respective objects
    - Testing using simple webhook listener