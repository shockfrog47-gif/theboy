# API

This API documents the local AI integration for the Java project.

The application communicates with Ollama at `http://localhost:11434`.
The model runs locally, so no external server is required.

## Base URL

```text
http://localhost:11434
```

## Model Used

```text
mistral
```

---

## 1. Generate Text

Generates a response for a single prompt.

### Endpoint

```http
POST /api/generate
```

### Request Body

```json
{
  "model": "mistral",
  "prompt": "Explain Java in 2 sentences.",
  "stream": false
}
```

### Fields

- `model`: The model to use, here `mistral`.
- `prompt`: The input text for the model.
- `stream`: `false` if you want the full response at once.

### Example with cURL

```bash
curl http://localhost:11434/api/generate -d '{
  "model": "mistral",
  "prompt": "Explain Java in 2 sentences.",
  "stream": false
}'
```

### Example Response

```json
{
  "model": "mistral",
  "created_at": "2026-04-27T19:51:00Z",
  "response": "Java is an object-oriented programming language. It is often used for servers, desktop apps, and Android.",
  "done": true
}
```

---

## 2. Chat

Generates the next message in a conversation with history.

### Endpoint

```http
POST /api/chat
```

### Request Body

```json
{
  "model": "mistral",
  "messages": [
    {
      "role": "user",
      "content": "What is Java?"
    }
  ],
  "stream": false
}
```

### Fields

- `model`: The model to use, here `mistral`.
- `messages`: The chat history as a list of messages.
- `stream`: `false` if you want the response returned as a single result.

### Roles

- `user`: Message from the user.
- `assistant`: Response from the model.
- `tool`: Tool output, if used.

### Example with cURL

```bash
curl http://localhost:11434/api/chat -d '{
  "model": "mistral",
  "messages": [
    {
      "role": "user",
      "content": "What is Java?"
    }
  ],
  "stream": false
}'
```

### Example Response

```json
{
  "model": "mistral",
  "created_at": "2026-04-27T19:51:00Z",
  "message": {
    "role": "assistant",
    "content": "Java is a widely used programming language for many kinds of applications."
  },
  "done": true
}
```

---

## 3. Java Integration

The Java app can call this API directly using `WebClient` or `HttpClient`.

### Example for Generate

```json
{
  "model": "mistral",
  "prompt": "Write a Hello World class in Java.",
  "stream": false
}
```

### Example for Chat

```json
{
  "model": "mistral",
  "messages": [
    {
      "role": "system",
      "content": "You are a helpful Java assistant."
    },
    {
      "role": "user",
      "content": "How do I build a REST API?"
    }
  ],
  "stream": false
}
```

---

## 4. Error Cases

### Model not available

If `mistral` is not installed locally yet, the model must be downloaded first.

### Ollama not running

If no service is listening on `localhost:11434`, the request will fail.

### Invalid JSON

Malformed JSON or unsupported fields will cause an API error.

---

## 5. Expected Behavior

- The application runs locally.
- No cloud service is needed.
- Responses come directly from the local model.
- The default model name is `mistral`.
