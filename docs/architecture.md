# Architecture

This document describes the architecture of the offline-ai-java project.

## Overview

The application is a locally running Java chat client that communicates with a local LLM via [Ollama](https://ollama.com). No internet connection or cloud service is required at runtime.

```
User
 │
 ▼
ChatController
 │
 ▼
PromptService
 │
 ▼
LlmClient  ──►  Ollama (localhost:11434)  ──►  Mistral (local model)
```

---

## Tech Stack

| Layer        | Technology                        |
|--------------|-----------------------------------|
| Language     | Java 21                           |
| Framework    | Spring Boot 3.4.2                 |
| HTTP Client  | Spring WebFlux (`WebClient`)      |
| AI Runtime   | Ollama (local)                    |
| Model        | Mistral                           |
| Build Tool   | Gradle                            |

---

## Module Breakdown

### `Main.java`
Entry point of the Spring Boot application.

### `config/ModelConfig.java`
Holds configuration values such as the Ollama base URL and the model name. Values can be overridden via `application.properties`.

### `ai/LlmClient.java`
Handles all HTTP communication with the Ollama API. Sends requests to `/api/generate` or `/api/chat` and returns the model response.

### `ai/PromptService.java`
Builds prompts from the system instructions and user input. Loads the system prompt from `resources/prompts/system.txt`.

### `chat/ChatController.java`
Manages user interaction. Reads input, delegates to `PromptService` and `LlmClient`, and prints the response.

### `chat/ChatSession.java`
Maintains the conversation history for multi-turn chat. Keeps track of all messages sent and received.

### `util/TextUtils.java`
Utility methods for text processing, e.g. trimming or formatting model output.

---

## Data Flow

1. The user enters a message.
2. `ChatController` passes the input to `PromptService`.
3. `PromptService` builds the full message list (system prompt + history + new message).
4. `LlmClient` sends the request to Ollama via HTTP POST.
5. Ollama runs the Mistral model locally and returns a response.
6. The response is displayed to the user and added to `ChatSession`.

---

## Resources

- `resources/prompts/system.txt` — The system prompt that defines the assistant's behavior.
- `resources/models/.keep` — Placeholder directory for locally stored model files if needed outside Ollama.

---

## Native / JNI (Optional)

The `native/llama/` directory is reserved for potential future integration of native libraries (e.g. llama.cpp via JNI) if Ollama is replaced or supplemented.

---

## Scripts

| Script                    | Purpose                              |
|---------------------------|--------------------------------------|
| `scripts/run-local.sh`    | Starts the application locally       |
| `scripts/download-model.sh` | Downloads the Mistral model via Ollama |
