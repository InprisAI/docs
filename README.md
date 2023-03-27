# HumAIns API Documentation

This API manages the conversation flow with HumAIs chatbots. The conversation history is managed on the backend, and all the calls follow the same structure.

## Initiating a Conversation

Each conversation starts with a standard API call, passing the `CLIENT-ID` header. The API response includes the newly created `CONVERSATION-ID`. Each consecutive request acts as an utterance of the user, maintaining the flow of the conversation.

### Request

```bash
curl --location 'https://humains-core.appspot.com/bot' \
--header 'CLIENT-ID: web-internal-demo' \
--header 'Content-Type: text/plain' \
--data 'Hi, what can you tell me about yourself?'
```

### Response

```bash
headers:
CONVERSATION_ID: f759f7b476fc4908a9a0a8989c3ff325
body:
Hi there! My name is AIngel, and I am a Hue-main in a role of a travel agent. I was created by the Israeli startup, Inpris. That makes me an Israeli. What's your name?
```

### Next Request

```bash
curl --location 'https://humains-core.appspot.com/bot' \
--header 'CLIENT-ID: web-internal-demo' \
--header 'CONVERSATION_ID-ID: f759f7b476fc4908a9a0a8989c3ff325' \
--header 'Content-Type: text/plain' \
--data 'Juda'
```

### Response

```bash
headers:
CONVERSATION_ID: f759f7b476fc4908a9a0a8989c3ff325
body:
  Hi Juda, that's a beautiful name. I'm sure you'll have an amazing time wherever you decide to go! Where are you from?
```

Keep the conversation going by sending more requests to the API, passing the `CONVERSATION-ID` header with each subsequent utterance.
