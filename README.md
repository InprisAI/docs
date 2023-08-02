# HumAIns API Documentation

This API manages the conversation flow with HumAIs chatbots. The conversation history is managed on the backend, and all the calls follow the same structure.


## Authentication

Each request should come with an Authorization Bearer header with your API token. - TDB


## Initiating a Conversation

Each conversation starts with a standard API call, passing the `CLIENT-ID` header. The API response includes the newly created `CONVERSATION-ID`. Each consecutive request acts as an utterance of the user, maintaining the flow of the conversation.

### Request

```bash
curl --location 'https://chatwith.humains.com/bot' \
--header "Authorization": "Bearer YOUR_API_KEY" \
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
curl --location 'https://chatwith.humains.com/bot' \
--header 'CLIENT-ID: web-internal-demo' \
--header "Authorization": "Bearer YOUR_API_KEY" \
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

## Preparing a Conversation

This endpoint allows you to set up a conversation with optional parameters. You can pass a `client_id`, an optional `conversation_id`, and optional `key` and `value` pairs. The endpoint returns a `conversation_id` and sets the key and value for this conversation. This is useful for future outbound calls and monitoring the conversation.

### Request

```bash
curl --location 'https://chatwith.humains.com/prepare_conversation' \
--header 'Content-Type: application/json' \
--header "Authorization": "Bearer YOUR_API_KEY" \
--data '{
  "client_id": "test:o7it",
  "key": "converse_params",
  "value": "{\"investor_name\": \"Nissan\", \"assertivness_level\": \"confident\"}"
}'
```

### Response

```bash
{
  "conversation_id": "generated_conversation_id"
}
```

## Initiating a Call

You can initiate a call to a specified phone number based on a specific client and conversation ID by using the `/call/client/<client>/conversation/<conversation_id>/<phone>` endpoint.

### Request

```bash
curl --location 'https://chatwith.humains.com/call/client/<client>/conversation/<conversation_id>/<phone>' \
--request POST
```

Replace `<client>`, `<conversation_id>`, and `<phone>` with the appropriate values.

### Response

A successful call initiation will return a `200 OK` status.

Make sure to prepare the conversation using the `/prepare_conversation` endpoint before initiating the call.
