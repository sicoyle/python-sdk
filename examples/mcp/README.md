# MCP Examples

Examples demonstrating how to use the `DaprMCPClient` from the Dapr Python SDK
to discover and invoke MCP tools via Dapr's built-in workflow orchestrations.

## Prerequisites

- **Dapr CLI** installed with `dapr init` completed (provides Redis on `localhost:6379`)
- **Python 3.11+**
- Install deps: `pip install -r requirements.txt`

## Files

| File | Purpose |
|------|---------|
| `mcp_tool_discovery.py` | The example: discovers tools and runs one in a workflow. |
| `weather_mcp_server.py` | Self-contained MCP server with `get_weather` / `get_forecast` tools (streamable-HTTP on `:8081/mcp`). |
| `resources/weather.yaml` | Dapr `MCPServer` resource pointing the sidecar at the weather server. |
| `resources/statestore.yaml` | Redis state store with `actorStateStore: true` (required by workflows). |

## Run

In one terminal, start the bundled MCP server:

```bash
python weather_mcp_server.py
```

In another terminal, run the example with Dapr:

```bash
dapr run \
  --app-id mcp-demo \
  --resources-path ./resources \
  -- python mcp_tool_discovery.py
```

The example will:

1. Connect to the `weather` MCPServer resource via the sidecar.
2. Print each discovered tool's name, description, and workflow name.
3. Schedule a `CallTool` child workflow for the first tool with `{"location": "Seattle"}`.
4. Print the result.

## Using a different MCP server

Edit `resources/weather.yaml` to point at any MCP-compatible endpoint. Supported
transports:

```yaml
spec:
  endpoint:
    streamableHTTP:
      url: http://host:port/mcp
```

```yaml
spec:
  endpoint:
    sse:
      url: http://host:port/sse
```

```yaml
spec:
  endpoint:
    stdio:
      command: python
      args: ["path/to/server.py"]
```
