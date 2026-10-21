# Setup & Execution Guide: FigCraft + OpenCode

This guide targets **OpenCode V2** and the attached, live FigCraft canvas. Commands assume this repository and the FigCraft source are sibling folders.

## 1. Build the CLI and app

Install [OpenCode](https://opencode.ai/v2/docs/) and a working Rust/Cargo toolchain. Configure a model in OpenCode separately; DeepSeek or GPT availability and any API charges depend on your provider.

```sh
# Run from the parent directory of this repository.
git clone https://github.com/storytold/figcraft.git
cd figcraft
cargo build --release -p figcraft-cli -p figcraft
```

The binaries are `target/release/figcraft-cli` and `target/release/figcraft` (or `.exe` on Windows). If you use an installed FigCraft release instead, change the command in your local config to the executable you installed. Refer to [FigCraft's CLI docs](https://github.com/storytold/figcraft/blob/main/docs/cli.md) if its build commands change.

## 2. Prepare the workspace

Run the following from **this repository's root**:

```sh
mkdir -p branding_assets downloads
cp .mcp.json.template opencode.json
```

```text
figcraft-opencode-mcp/
├── opencode.json          # Local copy of the published template; gitignored
├── branding_assets/       # Your own logo.svg, images, etc.; gitignored
├── downloads/             # Optional fetched assets; gitignored
└── templates/             # Reusable prompt guidance
```

The template's `../figcraft/target/release/figcraft-cli` path is relative to the workspace. Adjust it to your installed CLI's location if different. OpenCode loads **`opencode.json(c)`**, not `.mcp.json`: `.mcp.json.template` is just the name of this distributable example. Do not put private machine paths, credentials, brand files, or downloaded images in a committed config.

## 3. Connect to the live canvas

In a terminal in the workspace root:

```sh
PORT=7979
export FIGCRAFT_PORT="${PORT}"
../figcraft/target/release/figcraft --control "${PORT}"
```

Keep that terminal/app running. In **another** terminal in the same workspace, set the matching value and check OpenCode:

```sh
export FIGCRAFT_PORT=7979
opencode mcp list
opencode
```

The `figcraft-app` server should show as connected. OpenCode starts the CLI MCP subprocess; do not start `figcraft-cli mcp --attach` as a separate terminal service for this setup. If it cannot connect, verify the app is running, both ports match, the CLI path exists, and the `FIGCRAFT_PORT` variable is set in the terminal that starts OpenCode. To run without a GUI instead, change the CLI command to end at `"mcp"` (omit `"--attach"` and its port); this creates a separate headless document.

## 4. Steering prompt

Paste the following into a session before your design request, or store it in your preferred OpenCode instructions file and configure it through OpenCode's [instructions setting](https://opencode.ai/v2/docs/config#instructions). The text below is a **prompt template**, not an automatically loaded `.opencode/prompts/` file.

> You are an AI visual designer working in the **attached FigCraft canvas**. Use the connected FigCraft MCP tools to make editable native elements, not a flattened image. First inspect the current document and find available commands/tools. Check `./branding_assets/` before selecting a logo; do not invent a file or fetch web assets unless asked and a suitable tool is available. Use frames, typography, and auto layout with sensible padding, gaps, and sizing (`fixed`, `hug`, `fill` where supported). Keep labels readable and colors consistent with the brief. After each major step, inspect the layer tree and screenshot, fix visible issues, then save or export only to an approved path. Ask when brand rules or asset rights are unclear.

## 5. Prompt examples

- **Carousel:** “Create three 1080 × 1080 dark-mode slides for a product announcement. Use `./branding_assets/logo.svg` if it exists. Establish a consistent typography scale and auto-layout spacing; inspect each slide and correct overflow.”
- **Menu:** “Build a two-column A4-proportion coffee-shop menu with background `#F9F6F0`, primary text `#1A1A1A`, and accent `#C5A059`. Use editable text and align prices consistently.”
- **Landing page:** “Design a 1440 × 900 desktop hero with a navigation row, concise headline, subtext, and two CTA buttons. Use a clear grid, check contrast, and show a screenshot before saving.”

Frame sizes are design targets (for example 1080 × 1080 social, 1440 × 900 desktop, or 1240 × 1754 A4-proportion); verify export resolution and print requirements before production use.
