# Nio Public User Manual

Nio is an AI operator for developers. It helps you understand projects, remember context, resume unfinished work, and safely assist with coding tasks.

Nio's workflow is:

```text
analyze -> recall -> plan -> execute -> remember
```

Nio can work through the hosted gateway at `nioai.run`, or locally with your own provider keys if your plan supports BYOK.

## Install Nio

Download the latest release:

```text
https://github.com/luisMan/Nio-AI/releases
```

Windows Installation:
https://github.com/luisMan/Nio/tree/main/Windows

macOS:
https://github.com/luisMan/Nio/tree/main/MacOs

Linux:
https://github.com/luisMan/Nio/tree/main/Linux

Verify installation:

```powershell
nio status
```

## First-Time Setup

Run:

```powershell
nio setup
```

This prepares your local Nio configuration, runtime folder, permissions, and default settings.

Recommended Windows runtime location:

```text
%APPDATA%\Nio
```

## Account Login

If you are using hosted Nio, log in with the credentials or token provided by Nio:

```powershell
nio auth-login <user_id> <token>
```

If registration is available from your build:

```powershell
nio auth-register <email> <password> <display_name>
```

## Hosted Mode

Hosted mode is the easiest way to use Nio.

Use hosted mode if you are on:

```text
Free
Pro
Team
```

In hosted mode:

- Nio authenticates through `nioai.run`.
- AI requests go through the hosted gateway.
- You do not need local OpenAI or Anthropic keys.
- Usage limits are handled by your plan.

## Client Profile Configuration

Packaged Nio clients can use a client profile similar to `config/profiles/client.env`.

For hosted users, the most important values are:

```env
LOG_LEVEL=info

# Nio account key - obtain from https://nioai.run/account
# Free, Pro, Team, and SSO users set this to route through the Nio gateway.
# Leave empty only if using BYOK with local OPENAI_API_KEY or ANTHROPIC_API_KEY.
NIO_API_KEY=

NIO_GATEWAY_BASE_URL=https://nioai.run/v1
NIO_DEFAULT_PERMISSION_MODE=permission_request
NIO_SYNC_SESSION_ON_EXIT=true

NIO_MEMORY_SHORT_TERM_BACKEND=in_memory
NIO_MEMORY_MID_TERM_BACKEND=in_memory
NIO_MEMORY_LONG_TERM_BACKEND=in_memory
```
## Nio directory should look like:
```
config/
nio
```

Full public client profile example:

```env
LOG_LEVEL=info

# Nio account key - obtain from https://nioai.run/account
# Free, Pro, Team, and SSO users set this to route through the Nio gateway.
# Leave empty only if using BYOK with local OPENAI_API_KEY or ANTHROPIC_API_KEY.
NIO_API_KEY=

NIO_ROUTING_CONFIG_PATH=data/runtime/model-routing.json
NIO_ROUTING_STATE_BACKEND=file
NIO_ROUTING_AUTO_RELOAD=false
NIO_ROUTING_SIGNAL_REDIS_PUBSUB=false

NIO_AUDIT_LOG_PATH=data/audit/audit-log.jsonl
NIO_AUDIT_BACKEND=file

NIO_DEFAULT_PERMISSION_MODE=permission_request
NIO_SYNC_SESSION_ON_EXIT=true

NIO_MEMORY_SHORT_TERM_BACKEND=in_memory
NIO_MEMORY_MID_TERM_BACKEND=in_memory
NIO_MEMORY_LONG_TERM_BACKEND=in_memory

NIO_BUDGET_USER_REQUESTS_PER_MONTH=5000
NIO_BUDGET_USER_TOKENS_PER_MONTH=2000000
NIO_BUDGET_WORKSPACE_REQUESTS_PER_MONTH=5000
NIO_BUDGET_WORKSPACE_TOKENS_PER_MONTH=2000000

NIO_LOCAL_GPU_BASE_URL=
NIO_LOCAL_GPU_MODEL=qwen2.5-32b-instruct

NIO_GATEWAY_BASE_URL=https://nioai.run/v1
NIO_ALLOW_CLIENT_USAGE_RESET_ON_SYNC=true
```

Most hosted users should only need `NIO_API_KEY` and `NIO_GATEWAY_BASE_URL`. The other defaults are included so advanced users can inspect or customize their local runtime behavior.

## BYOK Mode

BYOK means "bring your own key."

Use BYOK if your plan allows you to keep provider keys on your own machine.

Nio reads local keys from your Nio runtime folder:

```text
%NIO_HOME%\.env
```

OpenAI example:

```env
OPENAI_API_KEY=sk-...
```

Anthropic example:

```env
ANTHROPIC_API_KEY=sk-ant-...
```

Local model server example:

```env
NIO_LOCAL_GPU_BASE_URL=http://127.0.0.1:11434
NIO_LOCAL_GPU_MODEL=qwen2.5-32b-instruct
```

After adding keys, verify providers:

```powershell
nio smoke-providers
```

## Start Nio

Interactive CLI:

```powershell
nio
```

Terminal cockpit:

```powershell
nio tui
```

Run Nio from inside a project folder when you want project-aware help:

```powershell
cd path\to\your\project
nio
```

## Good First Prompts

Try:

```text
Read this project and explain how to run it locally.
```

```text
Summarize the architecture of this codebase.
```

```text
Find likely bugs or missing setup steps.
```

```text
Create a task plan before editing any files.
```

```text
Help me implement this change, but ask before modifying files.
```

## Common Commands

Check runtime status:

```powershell
nio status
```

Run diagnostics:

```powershell
nio doctor
```

Show current task state:

```powershell
nio task
```

Show recommended next steps:

```powershell
nio next-steps
```

Show permissions, routing, provider, and trust status:

```powershell
nio trust
```

Open the full terminal interface:

```powershell
nio tui
```

Start the MCP server:

```powershell
nio mcp
```

## Working With Projects

When you run Nio inside a project folder, it can help with:

- Explaining files
- Reviewing code
- Creating task plans
- Editing project files
- Running commands
- Starting development servers
- Debugging errors
- Resuming unfinished work

Example:

```text
Inspect this project and tell me the safest next step.
```

Example:

```text
Implement the first item in task.md and ask before changing files.
```

## Permissions

Nio is designed to keep users in control.

Recommended public default:

```text
permission_request
```

This means Nio can reason, plan, and explain freely, but should ask before changing files or running sensitive actions.

If Nio asks for permission, review the action before approving it.

## Resume And Recovery

Nio tracks active work so you can recover from interruptions.

Show current activity:

```powershell
nio task
```

Inside interactive mode, use:

```text
/continue
```

to continue interrupted work.

Use:

```text
/revise <new instruction>
```

to redirect the current task.

Use:

```text
/stop
```

to cancel the current task.

Use:

```text
/task
```

to view current task status.

## Background Processes

If Nio starts a server or background command, list active processes:

```powershell
nio processes
```

View logs:

```powershell
nio process logs <id|pid>
```

Stop a process:

```powershell
nio process stop <id|pid>
```

## Hosted Gateway Settings

Hosted users normally do not need to edit these manually.

If instructed by support, the hosted gateway URL is:

```env
NIO_GATEWAY_BASE_URL=https://nioai.run/v1
```

If token auth is required:

```env
NIO_AUTH_TOKEN=<your-token>
```

## Local Files Nio Uses

Nio uses `NIO_HOME` as its runtime home.

Important local files may include:

```text
%NIO_HOME%\.env
%NIO_HOME%\data\runtime\providers.json
%NIO_HOME%\data\runtime\billing.json
%NIO_HOME%\data\runtime\auth-sessions.json
```

For most hosted users, you should not need to edit these manually.

## Security Notes

For public MVP users:

- Keep `NIO_HOME` inside your user profile.
- Do not commit `.env` files to git.
- Do not share provider keys.
- Do not store Nio config in a shared folder.
- Use `permission_request` unless you fully trust the workspace.
- Review file changes before approving them.
- Hosted users do not need local provider keys.

BYOK provider keys are stored locally in plain text today, so protect your machine account.

## Troubleshooting

If Nio is not working, run:

```powershell
nio status
nio doctor
```

If Nio cannot reach a provider in BYOK mode:

```powershell
nio smoke-providers
```

If Nio seems stuck:

```powershell
nio task
```

Then use one of:

```text
/continue
/revise <new instruction>
/stop
```

If hosted login or gateway access fails, check:

```text
Your login token
Your internet connection
NIO_GATEWAY_BASE_URL
```

## Recommended First Demo

1. Install Nio.
2. Run:

   ```powershell
   nio setup
   nio status
   nio doctor
   ```

3. Open a project:

   ```powershell
   cd path\to\project
   nio
   ```

4. Ask:

   ```text
   Read this project and explain how to run it locally.
   ```

5. Ask:

   ```text
   Create a safe task plan for improving this project.
   ```

6. Ask:

   ```text
   Implement the first task, but ask before changing files.
   ```

7. Check task state:

   ```powershell
   nio task
   ```

8. Check trust state:

   ```powershell
   nio trust
   ```

This demonstrates Nio's core value: project awareness, memory, permissioned execution, and resumable work.
