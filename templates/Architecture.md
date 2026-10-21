# System Architecture Specification: OpenCode × FigCraft MCP

This document describes a **local, attached** visual-design pipeline. `${PORT}` stands for the same user-selected control port in both shell commands; `7979` is an example, not a required value.

## Components and boundaries

| Component | Responsibility |
| --- | --- |
| OpenCode + selected DeepSeek/GPT-compatible model | Interpret the design brief, plan changes, call tools, and review results. Model choice and credentials are configured separately in OpenCode. |
| Workspace assets | `./branding_assets/` holds user-provided images and logos; `./downloads/` is an optional staging area for licensed downloaded assets. |
| FigCraft CLI MCP server | `figcraft-cli mcp --attach ${PORT}` serves MCP over stdio. OpenCode launches it and exchanges JSON-RPC tool requests and results. |
| FigCraft desktop app | `figcraft --control ${PORT}` accepts commands on its local loopback control channel and shows live canvas updates. |

```text
User brief / local assets
          │
          ▼
OpenCode + model ── MCP JSON-RPC 2.0 / stdio ──► figcraft-cli mcp --attach ${PORT}
          ▲                                            │
          └──── MCP results (tree / pixels / errors) ◄─┼──► localhost control channel
                                                       │       FigCraft desktop canvas
```

**Transport split:** OpenCode communicates with `figcraft-cli` using MCP over stdin/stdout; `${PORT}` belongs to the **CLI-to-app control channel**, not to the MCP transport. The FigCraft server accepts MCP initialize, tool discovery, and tool calls. Its documented stdio protocol is newline-delimited JSON-RPC 2.0; do not write arbitrary text to its stdout. The attached server forwards commands to the GUI, which returns state, renderings, and errors through the same path.

**Lifecycle:** Start the GUI with `--control ${PORT}` before connecting OpenCode. OpenCode starts and manages the CLI process from the workspace MCP configuration. Running `figcraft-cli mcp` without `--attach` creates a separate headless document instead of controlling the visible app.

**Typical feedback loop:** Query available commands/tools, create and modify frames and text, inspect the document or selected nodes, take a screenshot, revise, then explicitly save or export. MCP tool names and argument schemas are supplied by the installed FigCraft version; see [upstream MCP documentation](https://github.com/storytold/figcraft/blob/main/docs/mcp.md).

## Configuration and safety

OpenCode V2 configures local servers in `opencode.json` under `mcp.servers.<name>` with `type: "local"` and a `command` array. [`.mcp.json.template`](../.mcp.json.template) uses `{env:FIGCRAFT_PORT}` for OpenCode's environment substitution; in shell examples set `PORT=7979` and export `FIGCRAFT_PORT="${PORT}"` **before starting OpenCode**. An unset environment variable substitutes an empty string and prevents attachment.

The app's control channel is loopback-only; do not expose it on a public network. MCP servers and models can access data you authorize: review commands, limit asset directories, keep credentials out of the repository, and check rights for downloaded assets. For stronger file-access confinement in **attached** mode, supply FigCraft's `--automation-read-root` and `--automation-write-root` flags to the **app**, not to `figcraft-cli mcp --attach` (which rejects them). See [FigCraft's upstream security guidance](https://github.com/storytold/figcraft/blob/main/docs/mcp.md#confining-file-access).

This repository contains templates and documentation only. FigCraft's 400+ command registry lives in the upstream application; a filesystem MCP server or a web image downloader is **optional**, not an implicit part of this pipeline.
