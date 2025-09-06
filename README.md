# LongCat Unofficial Python Client

An unofficial Python client library for interacting with the LongCat Chat API, providing unlimited, no-billing access with integrated search and reasoning features. Lightweight wrapper for LongCat's streaming chat API with conversation history management.

## Installation

```bash
pip install requests
```

Clone the repository:
```bash
git clone https://github.com/yourusername/longcat-unofficial.git
cd longcat-unofficial
```

## Quick Start

```python
from chat import LongCatChat

# Initialize client
chatbot = LongCatChat()

# Simple chat message
response = chatbot.send_message("What is the capital of France?")
print(response)

# Enable search and reasoning
response = chatbot.send_message(
    "Explain quantum computing",
    enable_search=True,
    enable_reason=True
)
print(response)
```

## Features

- **Unlimited Usage**: No billing or rate limits
- **Search Integration**: Enable web search for up-to-date information
- **Reasoning Mode**: Advanced reasoning capabilities for complex queries
- **Streaming Responses**: Real-time response streaming
- **Conversation History**: Automatic message history management
- **Simple CLI Interface**: Interactive command-line chat interface
- **Regenerate Support**: Regenerate previous responses

## API Reference

### Client Initialization

```python
chatbot = LongCatChat(
    api_url="https://longcat.chat/api/v1/chat-completion-oversea"  # Default API endpoint
)
```

### Send Message

```python
response = chatbot.send_message(
    content="Your message here",
    enable_reason=False,     # Enable reasoning mode
    enable_search=False,     # Enable web search
    regenerate=False         # Regenerate last response
)
```

### Message with Search and Reasoning

```python
# Enable both search and reasoning for comprehensive responses
response = chatbot.send_message(
    "What are the latest developments in AI?",
    enable_search=True,      # Get current information
    enable_reason=True       # Apply advanced reasoning
)
print(response)
```

### Conversation Management

```python
# Clear conversation history
chatbot.clear_history()

# Access conversation ID
print(f"Current conversation: {chatbot.conversation_id}")

# View message history
for message in chatbot.message_history:
    print(f"{message['role']}: {message['content']}")
```

## Interactive CLI Mode

Run the interactive command-line interface:

```bash
python chat.py
```

### CLI Commands

- **Regular message**: Type any message to chat
- **`/reason`**: Toggle reasoning mode on/off
- **`/search`**: Toggle search mode on/off
- **`exit`**: Quit the application

Example CLI session:
```
You: /reason
Reason is now enabled
You: /search
Search is now enabled
You: What are the latest breakthroughs in quantum computing?
LongCat: [Response with search results and reasoning...]
You: exit
```

## Advanced Usage

### Custom API Headers

The client uses these default headers:
```python
headers = {
    "accept": "text/event-stream,application/json",
    "content-type": "application/json",
    "m-appkey": "fe_com.sankuai.friday.fe.longcat",
    "x-client-language": "en"
}
```

### Streaming Response Processing

The client automatically handles Server-Sent Events (SSE) streaming:

```python
# The send_message method processes streaming responses internally
response = chatbot.send_message("Tell me a story")
# Full response is returned after streaming completes
```

### Message History Structure

Each message in history contains:
```python
{
    "role": "user" | "assistant",
    "content": "message content",
    "chatStatus": "FINISHED" | "LOADING",
    "messageId": int,
    "idType": "custom"
}
```

### Regenerating Responses

```python
# Regenerate the last assistant response
response = chatbot.send_message(
    "Your original message",
    regenerate=True
)
```

## API Payload Structure

The client sends structured payloads to the LongCat API:

```python
payload = {
    "content": "User message",
    "messages": [/* message history array */],
    "reasonEnabled": 1,  # or 0
    "searchEnabled": 1,  # or 0
    "regenerate": 1      # or 0
}
```

## Response Format

LongCat responses are streamed as JSON objects:
```json
{
    "choices": [{
        "delta": {
            "content": "Response chunk"
        }
    }],
    "conversationId": "unique-conversation-id"
}
```

## Error Handling

```python
import requests

try:
    response = chatbot.send_message("Hello")
except requests.exceptions.RequestException as e:
    print(f"Network error: {e}")
except json.JSONDecodeError as e:
    print(f"Response parsing error: {e}")
except Exception as e:
    print(f"Unexpected error: {e}")
```

## Architecture

The project structure:

```
longcat-unofficial/
├── chat.py              # Main LongCatChat class
├── README.md           # This file
└── requirements.txt    # Dependencies (if created)
```

### Core Components

- **LongCatChat**: Main client class handling API communication
- **Message History**: Automatic conversation state management
- **Streaming Handler**: SSE response processing
- **CLI Interface**: Interactive command-line chat

## Requirements

- Python 3.6+
- requests library

## Features Overview

| Feature | Description | CLI Command |
|---------|-------------|-------------|
| Basic Chat | Standard conversational AI | Just type your message |
| Search Mode | Web search integration | `/search` to toggle |
| Reasoning Mode | Advanced logical reasoning | `/reason` to toggle |
| History Management | Automatic conversation tracking | Built-in |
| Streaming | Real-time response delivery | Automatic |
| Regeneration | Retry last response | Use `regenerate=True` |

## Limitations

- **Unofficial**: Not officially supported by LongCat
- **API Dependent**: Relies on LongCat's public API endpoint
- **No Authentication**: Uses public headers (no personal API key required)
- **Network Dependent**: Requires internet connection for all features

## Contributing

Contributions are welcome! Areas for improvement:

- Error handling enhancements
- Additional CLI features
- Response formatting options
- Configuration file support
- Async/await support

## License

This project is licensed under the MIT License.

## Disclaimer

This is an unofficial client for LongCat Chat. It is not affiliated with, endorsed by, or supported by LongCat or its parent company. Use at your own discretion and respect the service's terms of use.

## Author

Community-driven unofficial LongCat client

## Support

For issues, questions, or suggestions, please open an issue on the GitHub repository.

---

**Note**: This client provides unlimited access with no billing, but please use responsibly and respect the LongCat service.
