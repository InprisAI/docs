# HumAIns API Documentation

This API manages the conversation flow with HumAIs chatbots. The conversation history is managed on the backend, and all the calls follow the same structure.


## Authentication

Each request should come with an Authorization Bearer header with your API token.

If you have a user and password
```bash
curl -u user:password -H "Content-Type: application/json" http://chatwith.humains.com/auth/login
```
this will return a JWT Bearer token which we call "USER_TOKEN"

if you already have a user token and want to start a conversation:

curl --location 'https://chatwith.humains.com/start_conversation' \
--header "Authorization": "Bearer USER_TOKEN" \
--header 'CLIENT-ID: web-internal-demo' \
--header 'Content-Type: text/plain' \
--data 'Hi, what can you tell me about yourself?'
```

this will return a conversation id + CONVERSATION_TOKEN

to continue thw conversation use the conversation token and use the /p_bot endpoint

```bash
curl --location 'https://chatwith.humains.com/p_bot' \
--header "Authorization": "Bearer YOUR_API_KEY" \
--header 'CLIENT-ID: web-internal-demo' \
--header 'Content-Type: text/plain' \
--data 'Hi, what can you tell me about yourself?'
```

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
CONVERSATION-ID: f759f7b476fc4908a9a0a8989c3ff325
body:
Hi there! My name is AIngel, and I am a Hue-main in a role of a travel agent. I was created by the Israeli startup, Inpris. That makes me an Israeli. What's your name?
```

### Next Request

```bash
curl --location 'https://chatwith.humains.com/bot' \
--header 'CLIENT-ID: web-internal-demo' \
--header "Authorization": "Bearer YOUR_API_KEY" \
--header 'CONVERSATION-ID: f759f7b476fc4908a9a0a8989c3ff325' \
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

## Complex Response Structure

The API now supports a more complex response structure that can be configured per client. This allows for more detailed and customized responses with additional information and metadata.

### Configuration

To enable the complex response structure for a client, you need to set a configuration in the database using settings:

```bash
  "resp_structure:<client_name>": "{
      "additional_info": ["keys", "you\'d", "like", "to", "get"],
      "utterance_with_pronunciation": true
  }"
```
(see __resp_structure.conf)

Where:
- `client_name` is the name of your client
- `additional_info` is an array of keys to include in the response
- `utterance_with_pronunciation` (optional) enables returning both the plain utterance and the utterance with pronunciation

### Response Format

When the complex response structure is enabled, the API will return a JSON response instead of plain text:

```json
{
  "utterance": "Plain text response without pronunciation",
  "utterance_with_pronunciation": "Response with pronunciation marks",
  "additional_info": {
    "key1": "value1",
    "key2": "value2"
  },
  "metadata": {
    "conversation_id": "f759f7b476fc4908a9a0a8989c3ff325",
    "client_id": "web-internal-demo"
  }
}
```

This structure allows clients to receive both the plain text response and additional contextual information in a single API call.

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
  "value": "{\"investor_name\": \"John\", \"assertivness_level\": \"confident\"}"
}'
```

### Response

```bash
{
  "conversation_id": "generated_conversation_id"
}
```

## Deleting Conversation Information (Admin Only)

Admin users can manage conversation-related metadata through the following DELETE endpoints.

### Delete All Conversation Information

**Endpoint:** `DELETE /clients/info/delete`

Use this endpoint to purge all conversation information for a specific conversation. This operation requires admin-level authentication.

**Request:**
```bash
curl --location 'https://chatwith.humains.com/clients/info/delete' \
--header "Authorization: Bearer ADMIN_TOKEN" \
--header 'Content-Type: application/json' \
--request DELETE \
--data '{
  "client_id": "clientName",
  "conversation_id": "conversationId"
}'
```

**Response:**
```json
{"status": "ok"}
```

### Delete Specific Conversation Information Key

**Endpoint:** `DELETE /clients/info_key/delete`

This endpoint allows you to delete a specific key from the conversation's metadata. Admin privileges are required.

**Request:**
```bash
curl --location 'https://chatwith.humains.com/clients/info_key/delete' \
--header "Authorization: Bearer ADMIN_TOKEN" \
--header 'Content-Type: application/json' \
--request DELETE \
--data '{
  "client_id": "clientName",
  "conversation_id": "conversationId",
  "key": "keyName"
}'
```

**Response:**
```json
{"status": "ok"}
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

## Hub API Endpoints

### Settings Management

#### Set Multiple Settings
**Endpoint:** `POST /settings`  
**Auth Required:** Admin  
Sets multiple setting values at once.

**Request:**
```bash
curl --location 'https://chatwith.humains.com/settings' \
--header "Authorization: Bearer ADMIN_TOKEN" \
--header 'Content-Type: application/json' \
--data '{
  "setting_key1": "value1",
  "setting_key2": "value2"
}'
```

#### Get Setting Value
**Endpoint:** `GET /settings/{key}`  
**Auth Required:** Admin  
Retrieves the value for a specific setting key.

**Request:**
```bash
curl --location 'https://chatwith.humains.com/settings/your_setting_key' \
--header "Authorization: Bearer ADMIN_TOKEN"
```

### Value Management

#### Get Value for Humain
**Endpoint:** `GET /value/{humain}/{key}`  
**Auth Required:** Admin  
Retrieves a specific value for a humain.

**Request:**
```bash
curl --location 'https://chatwith.humains.com/value/humain_name/key_name' \
--header "Authorization: Bearer ADMIN_TOKEN"
```

#### Set Value
**Endpoint:** `POST /value/{key}`  
**Auth Required:** Admin  
Sets a value for a specific humain and key.

**Request:**
```bash
curl --location 'https://chatwith.humains.com/value/key_name' \
--header "Authorization: Bearer ADMIN_TOKEN" \
--header 'Content-Type: application/json' \
--data '{
  "humain": "humain_name",
  "value": "your_value"
}'
```

#### Get Multiple Values
**Endpoint:** `GET /values`  
**Auth Required:** Admin  
Retrieves multiple values for a humain.

**Request:**
```bash
curl --location 'https://chatwith.humains.com/values' \
--header "Authorization: Bearer ADMIN_TOKEN" \
--header 'Content-Type: application/json' \
--data '{
  "humain": "humain_name",
  "values": ["key1", "key2"]
}'
```

#### Set Multiple Values
**Endpoint:** `POST /values`  
**Auth Required:** Admin  
Sets multiple values for a humain.

**Request:**
```bash
curl --location 'https://chatwith.humains.com/values' \
--header "Authorization: Bearer ADMIN_TOKEN" \
--header 'Content-Type: application/json' \
--data '{
  "humain": "humain_name",
  "values": {
    "key1": "value1",
    "key2": "value2"
  }
}'
```

### Client Value Injection

#### Inject Single Client Value
**Endpoint:** `POST /inject_client_value`  
**Auth Required:** Admin  
Injects a single value for a specific client.

**Request:**
```bash
curl --location 'https://chatwith.humains.com/inject_client_value' \
--header "Authorization: Bearer ADMIN_TOKEN" \
--header 'Content-Type: application/json' \
--data '{
  "client_id": "client_name:humain_name",
  "key": "key_name",
  "value": "value_to_inject"
}'
```

#### Inject Multiple Client Values
**Endpoint:** `POST /inject_client_values`  
**Auth Required:** Admin  
Injects multiple values for a specific client.

**Request:**
```bash
curl --location 'https://chatwith.humains.com/inject_client_values' \
--header "Authorization: Bearer ADMIN_TOKEN" \
--header 'Content-Type: application/json' \
--data '{
  "client_id": "client_name:humain_name",
  "values": {
    "key1": "value1",
    "key2": "value2"
  }
}'
```

### Conversation Management

#### Register Clients
**Endpoint:** `GET /register_clients`  
Registers all available clients.

**Request:**
```bash
curl --location 'https://chatwith.humains.com/register_clients'
```

#### Get All Clients
**Endpoint:** `GET /clients`  
Retrieves a list of all registered clients.

**Request:**
```bash
curl --location 'https://chatwith.humains.com/clients'
```

#### Get Client Conversations (Full)
**Endpoint:** `GET /clients/{client_name}/conversations_full`  
Retrieves detailed conversation history for a client, with optional date filtering.

**Request:**
```bash
curl --location 'https://chatwith.humains.com/clients/client_name/conversations_full?start_date=1234567890&end_date=1234567899'
```

#### Get Client Conversations (Summary)
**Endpoint:** `GET /clients/{client_name}/conversations`  
Retrieves conversation summaries for a client, with optional date filtering.

**Request:**
```bash
curl --location 'https://chatwith.humains.com/clients/client_name/conversations?start_date=1234567890&end_date=1234567899'
```

#### Get Single Conversation
**Endpoint:** `GET /clients/{client_name}/conversations/{conversation_id}`  
Retrieves details of a specific conversation.

**Request:**
```bash
curl --location 'https://chatwith.humains.com/clients/client_name/conversations/conversation_id'
```

#### Get Conversation Info
**Endpoint:** `GET /clients/{client_name}/conversations/{conversation_id}/info`  
Retrieves metadata and external information for a specific conversation.

**Request:**
```bash
curl --location 'https://chatwith.humains.com/clients/client_name/conversations/conversation_id/info'
```

### Raw Data Access

#### Get Raw Log Entry
**Endpoint:** `GET /raw`  
Retrieves raw log entry data for specific parameters.

**Request:**
```bash
curl --location 'https://chatwith.humains.com/raw?client_id=client_name&conversation_id=conv_id&inference_id=inf_id&state_name=state&stage=stage_name'
```

Note: All endpoints marked with "Auth Required: Admin" require administrative privileges and the appropriate authentication token.
