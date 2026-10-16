# Use Nio from ChatGPT, Claude and AI agents (remote MCP)

Since v0.1.238, Nio answers MCP at **`https://nioai.run/mcp`**. Any client that can add a remote MCP server can use it. On your own computer, Claude Code, Codex CLI, Cursor and VS Code can keep using the local server: `nio mcp install --client <claude|codex|cursor|vscode>`.

| What the client can do | Tools | Needs |
| --- | --- | --- |
| Use your Nio memory | `memory_search`, `memory_save`, `task_list`, `project_context`, `auth_whoami` | A Nio API key or an OAuth sign-in |
| Hand Nio project tasks | `nio_ask`, `nio_task_status`, `nio_task_cancel`, `project_status`, `project_decisions`, `project_record`, `project_resume` | A Nio API key, and `nio connect` running on your computer |

Your code never runs on nioai.run. Project tasks travel to your own Nio, which runs them with your permission settings. Nio changes files in a project only after you allow it for that project. Allow it on your computer with `nio cli allow-project <your account> <project folder>`. In v0.1.238 a connected client can also call `project_allow_writes`; keep tool approval on in your client and connect only clients you trust. The next release removes that tool from remote clients.

## Connect a client

You need a Nio API key for your account, or OAuth sign-in where the client supports it.

**OpenAI Responses API**

```json
{ "type": "mcp", "server_label": "nio", "server_url": "https://nioai.run/mcp", "authorization": "<NIO_API_KEY>" }
```

Send `authorization` on every request; OpenAI does not store it.

**Claude API (MCP connector)**

```json
"mcp_servers": [{ "type": "url", "url": "https://nioai.run/mcp", "name": "nio", "authorization_token": "<NIO_API_KEY>" }],
"tools": [{ "type": "mcp_toolset", "mcp_server_name": "nio" }]
```

**Claude.ai**: Customize > Connectors > Add custom connector, URL `https://nioai.run/mcp`. You can choose either kind of sign-in:

- OAuth: sign in to Nio when you connect. This gives memory tools only.
- "No sign in" with a request header `Authorization: Bearer <NIO_API_KEY>`. This also gives project tools.

**ChatGPT**: add a custom connector (developer mode) with URL `https://nioai.run/mcp` and authentication **OAuth**. Sign in with your nioai.run email and password (plus your authenticator code if MFA is on) and allow the `memory` scope. ChatGPT cannot send API keys, so it gets the memory tools. Which ChatGPT plans can add custom connectors is up to OpenAI.

Your account lists the apps you signed in to and can disconnect them (`GET` and `DELETE /auth/oauth/grants`).

## `nio connect`: project tasks on your computer

```bash
nio connect
```

Run it in a project folder, or name the folders: `nio connect --project D:\work\shop --project blog=D:\work\site`. Each project is offered under its folder name, or the name before `=`. The gateway learns names only, never paths.

- **Account:** `NIO_API_KEY` when set, otherwise your `nio cli auth-login`.
- **Computer name:** `--name <name>` (default: the host name). A second `nio connect` with the same name takes over from the first. An account can connect up to 5 computers.
- **Connection:** a WebSocket, with long polling where WebSockets are blocked (`--websocket` or `--polling` forces one). It pings every 30 s and reconnects by itself, backing off up to 60 s.

Then, from the client, `project_context` lists your connected projects. `nio_ask` with `{"project": "shop", "prompt": "..."}` returns a `task_id` at once, and `nio_task_status` returns the result. If no computer is connected, project tools answer "Start Nio on your computer with `nio connect`, then try again."

## Privacy

- The account always comes from your key or sign-in, never from what a model writes in a tool call.
- Each account sees only its own memory, work items and projects.
- Secrets are removed from saved notes and results.
- Every call is recorded in your audit log: the tool and the outcome, without arguments or results.
