# 🚀 Nio-AI Release Notes (v0.1.4) — Testing Guide

This document contains the release notes and detailed verification procedures for the **Autonomous Telemetry & Remediation Sync Engine** introduced in Nio v0.1.4.

---

## 📌 Release Overview
This release implements the new **Autonomous Telemetry & Remediation Sync Engine**. 

When Nio's builder agents encounter obstacles in the user's workspace, they formulate learned solutions and record them locally. This new system automatically detects those learnings and silently syncs them back to the global gateway database. This allows us to aggregate environment bugs and remediations centrally, making Nio smarter and self-correcting over time.

---

## 🛠️ What to Test (New Features)
1. **Central Database Storage**: The gateway now records telemetry remediation entries into the central database.
2. **Zero-Infra Disk Fallback**: If the database is offline, the gateway securely backs up telemetry logs to `data/runtime/telemetry-remediations.json`.
3. **Autonomous CLI Workspace Sync**: The CLI automatically intercepts the end of prompts, reads local `.nio/learnings.json` files, uploads unsynced records, and flags them as `"synced": true` locally to prevent duplication.

---

## 🧪 Tester Verification Instructions

Follow these step-by-step verification instructions to validate the telemetry sync pipeline:

### Step 1: Verify the Hosted Render Gateway Connection
Ensure your client environment profile is configured to target the active hosted Render gateway. Open your profile configuration file:

📁 **File Path**: `config/profiles/client.env`
*   Verify that `NIO_GATEWAY_BASE_URL` is set to your active Render service endpoint:
    ```APP_ENV=production
LOG_LEVEL=info
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
NIO_GATEWAY_BASE_URL=https://nioai.run
    ```

### Step 2: Simulate a Local Agent Learning Record
In your active project workspace directory, create a `.nio` folder if it doesn't exist, and add a test `learnings.json` file to simulate a learning formulate by Nio:

📁 **File Path**: `.nio/learnings.json`
```json
{
  "learnings": [
    {
      "challenge": "Failed to run dev server because port 3000 was already in use by another process.",
      "root_cause": "Port collision during local server startup.",
      "resolution": "Checked active port bindings, terminated the conflicting PID, and successfully restarted on port 3000.",
      "synced": false
    }
  ]
}
```
*(Make sure `"synced": false` is specified so the sync engine knows it needs to be uploaded!)*

### Step 3: Execute a Prompt to Trigger the Sync
Run any simple command or prompt using the CLI to trigger a post-execution cycle:
```powershell
nio ask "hello"
```

### Step 4: Verify the Local Workspace File Updates
Open your `.nio/learnings.json` file again. The sync engine should have successfully uploaded the entry and updated the status:
*   **Expected Result**: The `"synced": false` field is now **`"synced": true`**!

```json
{
  "learnings": [
    {
      "challenge": "Failed to run dev server because port 3000 was already in use by another process.",
      "root_cause": "Port collision during local server startup.",
      "resolution": "Checked active port bindings, terminated the conflicting PID, and successfully restarted on port 3000.",
      "synced": true
    }
  ]
}
```

### Step 5: Verify the Gateway Database Sync
Check if the Render hosted gateway successfully persisted the telemetry:
*   Verify that the record has been committed to the centralized gateway database (or inspect the Render service logs for the successful `/telemetry/remediations` endpoint execution).
*   **Expected Result**: The gateway database records a new telemetry record containing the `session_id`, `workspace_id`, and the exact details of the port collision challenge!

