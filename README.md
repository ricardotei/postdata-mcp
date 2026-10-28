# PostData MCP server

Remote MCP server for [PostData](https://postdata.cloud), the free sensor-data store for IoT devices and the successor to dweet.io. There is no signup and no API key, for the agent or for the device.

With it, an agent can get an unused device name, write and flash the firmware, wait for the board's first reading, and give you a live chart link, without stopping to ask anyone to create an account.

- **Endpoint:** `https://api.postdata.cloud/mcp`
- **Transport:** Streamable HTTP
- **Authentication:** none
- **Registry name:** `cloud.postdata/postdata`

This repository holds the listing and setup instructions. The server itself is hosted by PostData; there is nothing to install or run.

## Tools

| Tool | What it does |
|---|---|
| `new_device_name` | An unused device name, with its ingest, read, live-stream and chart URLs |
| `publish_reading` | Store a reading, exactly like `POST /add/{device}` (one message per device every 30 s) |
| `get_latest` | The latest reading, when it arrived, and the chart URL |
| `get_history` | Per-metric min/max/mean/first/last and a downsampled series for the last N hours |
| `wait_for_reading` | Block (up to 50 s) until a device sends a new reading. Use it after flashing firmware |
| `list_recent_devices` | The 20 most recently active public devices and their payloads |

`publish_reading` is the only tool that writes. The others are read-only.

## Setup

### VS Code (GitHub Copilot agent mode)

Add this to `.vscode/mcp.json`:

```json
{ "servers": { "postdata": { "type": "http", "url": "https://api.postdata.cloud/mcp" } } }
```

Or from the command line:

```bash
code --add-mcp '{"name":"postdata","type":"http","url":"https://api.postdata.cloud/mcp"}'
```

### Claude Code

```bash
claude mcp add --transport http postdata https://api.postdata.cloud/mcp
```

### Claude (claude.ai, Desktop, mobile)

Go to **Customize → Connectors → Add custom connector**, paste `https://api.postdata.cloud/mcp` and choose **No sign-in**.

### Cursor

Add this to `~/.cursor/mcp.json` or `.cursor/mcp.json`:

```json
{ "mcpServers": { "postdata": { "url": "https://api.postdata.cloud/mcp" } } }
```

### OpenAI Codex

```bash
codex mcp add postdata --url https://api.postdata.cloud/mcp
```

### Gemini CLI

```bash
gemini mcp add --transport http postdata https://api.postdata.cloud/mcp
```

### Anything else

Add a remote (Streamable HTTP) MCP server with the URL `https://api.postdata.cloud/mcp` and no credentials. Per-client details for Cline, Zed, Devin and others are at https://postdata.cloud/docs#mcp.

## Usage

Ask your agent things like:

- Get my ESP32's DHT22 temperature and humidity onto a live chart.
- I just flashed the board. Is it posting yet?
- What did amber-otter-4821 read over the last 24 hours? Min, max and average.
- My old dweet.io sketch stopped working. Move it to PostData.
- Show me what real devices are sending, so I can copy the payload format.

A typical run: the agent calls `new_device_name`, writes firmware that posts JSON to the returned ingest URL, you flash the board, the agent calls `wait_for_reading` until the first reading lands, and then it hands you the chart URL.

Devices post over plain HTTP, HTTPS, WebSocket or MQTT-over-WebSocket. Agents that can't speak MCP can use the HTTP API described at https://postdata.cloud/llms.txt.

## Things to know

- **Everything is public.** Anyone can read any device, and anyone who knows a device's name can write to it. Don't send credentials, personal data or precise locations.
- Each device can send one message every 30 seconds.
- Numeric history is kept for about 92 days.

## Links

- Website: https://postdata.cloud
- MCP docs: https://postdata.cloud/docs#mcp
- Live devices: https://postdata.cloud/explore
- Privacy: https://postdata.cloud/privacy · Terms: https://postdata.cloud/terms
- Contact: hello@obiio.com

PostData is run by [obiio](https://obiio.com). The service is free.
