# 🧪 casual-mcp + Blender MCP Example

This repository provides a working example of using [casual-mcp](https://pypi.org/project/casual-mcp) with the [Blender MCP Server](https://github.com/ahujasid/blender-mcp).

It demonstrates how to:
- Configure OpenAI models (or any compatible API)
- Use a tuned system prompt for Blender tool calling
- Launch the `casual-mcp` API server via UVX
- Send requests to the `/chat` endpoint to manipulate Blender scenes


## 🚀 How to Run

1. **Install the dependencies**

Make sure you have [`uv`](https://github.com/astral-sh/uv) installed.

### 2. Adjust config.json as required

The supplied config.json provides access to 4 OpenAI models:
- gpt-4o-mini
- gpt-4.1-nano
- gpt-4.1-mini
- gpt-4.1

You can add others including models running on openai compatible APIs - see the section below on Configuration


### 3. Copy and Configure Environment Variables

```bash
cp .env-example .env
```

If using OpenAI models, add your API key to `.env`:

```env
OPEN_AI_API_KEY=your-openai-key
TOOL_RESULT_FORMAT=result
```

### 4. Run the API

From the root of the project:

```bash
uvx casual-mcp serve
```

This will start the API server at `http://localhost:8000` and automatically register the `blender-mcp` tool server.


## 🧠 Making a Tool-Calling Request

Use the `/chat` endpoint to send LLM prompts:

```json
POST /chat
Content-Type: application/json

{
  "session_id": "my-session",
  "model": "gpt-4.1-nano",
  "user_prompt": "Create a large plane, place an area light above it"
}
```

The assistant will invoke Blender tool functions as needed.


## ⚙️ Configuration

This project uses a `config.json` file to declare models and servers.

### ✨ Example: `config.json`

```json
{
  "namespaced_tools": false,
  "models": {
    "gpt-4o-mini": {
      "provider": "openai",
      "model": "gpt-4o-mini",
      "template": "blender"
    }
  },
  "servers": {
    "blender": {
      "type": "uvx",
      "package": "blender-mcp"
    }
  }
}
```

### ➕ Add Your Own Models

To add your own OpenAI-compatible models:

```json
"my-local-model": {
  "provider": "openai",
  "endpoint": "http://localhost:1234/v1",
  "model": "my-llm-name",
  "template": "blender"
}
```

Make sure the `template` matches a file in `prompt-templates/` (e.g., `blender.j2`).


## 🧩 Prompt Template

The `prompt-templates/blender.j2` file is a Jinja2 template tailored for tool-calling with the Blender MCP toolset. It's rendered using the tool schema and sent as the system prompt.

This helps guide the LLM to use the correct tools with the right parameters.

Feel free to adjust this to see how it improves results


## 🛠 Additional Notes

- Models must support function/tool calling to work properly (OpenAI GPT-4.1+ or compatible)
- The server uses [Model Context Protocol (MCP)](https://modelcontextprotocol.io) to define and invoke tool calls
- All sessions are stored in memory


## 📄 License

MIT