# Agora Fastify Server

This Fastify server implements endpoints for managing conversational AI agents using Agora's ConvoAI REST API.

## Build your own server

To learn how to build your own server, follow the instructions in the [docs/FASTIFY_GUIDE.md](../docs/FASTIFY_GUIDE.md) file.

## Run the server

1. Install dependencies

   ```bash
   npm install
   ```

2. Copy `.env.example` to `.env` and fill in your configuration values

   ```bash
   cp .env.example .env
   ```

3. Start the server

   Development

   ```bash
   npm run dev
   ```

   Production

   ```bash
   npm start
   ```

   ## Architecture

<picture>
  <source srcset="../.github/assets/Agora-ConvoAI-NodeJS-Server-Architecture-dark.png" media="(prefers-color-scheme: dark)">
  <img src="../.github/assets/Agora-ConvoAI-NodeJS-Server-Architecture-light.png" alt="Architecture diagram of Conversational Ai by Agora and OpenAi">
</picture>

## API Endpoints

### GET /token

Generate an Agora PUBLISHER token for uid 0, and generate a channel name.

```bash
curl -X GET "http://localhost:3030/token"
```

Generate token with specific channel and uid

```bash
curl -X GET "http://localhost:3030/token?channel=test-channel&uid=1234"
```

Response:

```json
{
  "token": "007eJxzYBB...",
  "uid": "1234",
  "channel": "test-channel"
}
```

### POST /agent/invite

Start a conversation with an AI agent.

```bash
curl -X POST "http://localhost:3030/agent/invite" \
  -H "Content-Type: application/json" \
  -d '{
    "requester_id": "1234",
    "channel_name": "test-channel",
    "input_modalities": ["text"],
    "output_modalities": ["text", "audio"]
  }'
```

Response:

```json
{
  "agent_id": "agent-123",
  "create_ts": 1234567890,
  "state": "active"
}
```

### POST /agent/remove

Remove an AI agent from the channel.

```bash
curl -X POST "http://localhost:3030/agent/remove" \
  -H "Content-Type: application/json" \
  -d '{
    "agent_id": "agent-123"
  }'
```

Response:

```json
{
  "success": true
}
```

## Environment Variables

See `.env.example` for all required environment variables.

## Validation

The server implements request [validation](src/utils/validation.ts) using Fastify's schema validation:

- All endpoints validate required environment variables
- Agent invite requests validate requester_id (string or positive integer) and channel_name (3-64 characters)
- Token requests validate uid (must be a number) and channel name (string 3-64 characters)

## Error Handling

The server implements consistent error handling with appropriate HTTP status codes:

- 400: Bad Request (invalid parameters)
- 415: Unsupported Media Type
- 500: Server Error (missing configuration or runtime errors)
