# Claude & Claude Code: Complete Guide

## Table of Contents
1. [What is Claude?](#what-is-claude)
2. [Available Models](#available-models)
3. [Getting Started with the Claude API](#getting-started-with-the-claude-api)
4. [Key API Features](#key-api-features)
5. [Claude Code CLI](#claude-code-cli)
6. [Tool Use & Agentic Loops](#tool-use--agentic-loops)
7. [Managed Agents](#managed-agents)
8. [Prompt Caching](#prompt-caching)
9. [Streaming](#streaming)
10. [Extended Thinking](#extended-thinking)

---

## What is Claude?

Claude is Anthropic's family of AI assistants designed for a wide range of tasks: reasoning, coding, writing, analysis, and more. Claude is available via:

- **Claude.ai** - web and mobile chat interface
- **Claude API** - REST API and official SDKs for building applications
- **Claude Code** - CLI tool and IDE extensions for software engineering
- **Claude Platform on AWS** - same API, hosted on AWS infrastructure

---

## Available Models

| Model | Model ID | Context | Input $/1M | Output $/1M |
|-------|----------|---------|------------|-------------|
| Claude Fable 5 | `claude-fable-5` | 1M | $10.00 | $50.00 |
| Claude Opus 4.8 | `claude-opus-4-8` | 1M | $5.00 | $25.00 |
| Claude Sonnet 4.6 | `claude-sonnet-4-6` | 1M | $3.00 | $15.00 |
| Claude Haiku 4.5 | `claude-haiku-4-5` | 200K | $1.00 | $5.00 |

**Choosing a model:**
- **Opus 4.8** - Default for most tasks; best balance of capability and cost
- **Fable 5** - Most capable, for the hardest reasoning and long-horizon agentic work
- **Sonnet 4.6** - Good balance of speed and intelligence for mid-tier tasks
- **Haiku 4.5** - Fastest and cheapest; ideal for simple tasks at scale

---

## Getting Started with the Claude API

### Installation

**Python:**
```bash
pip install anthropic
```

**TypeScript/Node.js:**
```bash
npm install @anthropic-ai/sdk
```

### Set your API key

```bash
export ANTHROPIC_API_KEY="your-api-key-here"
```

### Basic API Call

**Python:**
```python
import anthropic

client = anthropic.Anthropic()

message = client.messages.create(
    model="claude-opus-4-8",
    max_tokens=1024,
    messages=[
        {"role": "user", "content": "Explain quantum computing in simple terms."}
    ]
)

print(message.content[0].text)
```

**TypeScript:**
```typescript
import Anthropic from "@anthropic-ai/sdk";

const client = new Anthropic();

const message = await client.messages.create({
  model: "claude-opus-4-8",
  max_tokens: 1024,
  messages: [
    { role: "user", content: "Explain quantum computing in simple terms." }
  ],
});

console.log(message.content[0].text);
```

---

## Key API Features

### System Prompts

```python
client.messages.create(
    model="claude-opus-4-8",
    max_tokens=1024,
    system="You are a helpful Python programming assistant.",
    messages=[{"role": "user", "content": "How do I read a CSV file?"}]
)
```

### Vision (Image Input)

```python
import base64, anthropic

with open("image.png", "rb") as f:
    image_data = base64.standard_b64encode(f.read()).decode("utf-8")

client.messages.create(
    model="claude-opus-4-8",
    max_tokens=1024,
    messages=[{
        "role": "user",
        "content": [
            {"type": "image", "source": {"type": "base64", "media_type": "image/png", "data": image_data}},
            {"type": "text", "text": "What is in this image?"}
        ]
    }]
)
```

---

## Claude Code CLI

Claude Code is an AI coding assistant that runs in your terminal or IDE.

### Installation

```bash
npm install -g @anthropic-ai/claude-code
```

### Basic Usage

```bash
claude                          # Start interactive session
claude "What does this do?"     # One-off question
```

### Key Commands

| Command | Description |
|---------|-------------|
| `/help` | Show available commands |
| `/clear` | Clear conversation context |
| `/compact` | Compress conversation to save context |
| `/model` | Switch Claude model |
| `/fast` | Toggle fast mode |

---

## Tool Use & Agentic Loops

```python
import anthropic

client = anthropic.Anthropic()

tools = [{
    "name": "get_weather",
    "description": "Get current weather for a location",
    "input_schema": {
        "type": "object",
        "properties": {
            "location": {"type": "string"}
        },
        "required": ["location"]
    }
}]

messages = [{"role": "user", "content": "What's the weather in Tokyo?"}]

while True:
    response = client.messages.create(
        model="claude-opus-4-8",
        max_tokens=1024,
        tools=tools,
        messages=messages
    )

    if response.stop_reason == "end_turn":
        print(response.content[0].text)
        break

    if response.stop_reason == "tool_use":
        tool_block = next(b for b in response.content if b.type == "tool_use")
        result = "Sunny, 72F"  # call your actual function here

        messages.append({"role": "assistant", "content": response.content})
        messages.append({
            "role": "user",
            "content": [{"type": "tool_result", "tool_use_id": tool_block.id, "content": result}]
        })
```

---

## Streaming

```python
with client.messages.stream(
    model="claude-opus-4-8",
    max_tokens=2048,
    messages=[{"role": "user", "content": "Write a short story."}]
) as stream:
    for text in stream.text_stream:
        print(text, end="", flush=True)

final = stream.get_final_message()
```

---

## Extended Thinking

```python
client.messages.create(
    model="claude-opus-4-8",
    max_tokens=16000,
    thinking={"type": "adaptive"},
    messages=[{"role": "user", "content": "Solve this complex problem..."}]
)
```

---

## Useful Links

- **API Reference**: https://docs.anthropic.com/en/api/
- **Claude Code Docs**: https://docs.anthropic.com/en/docs/claude-code/
- **Python SDK**: https://github.com/anthropics/anthropic-sdk-python
- **TypeScript SDK**: https://github.com/anthropics/anthropic-sdk-typescript
- **Model Pricing**: https://www.anthropic.com/pricing
