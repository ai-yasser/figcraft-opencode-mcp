# The Story: From Invisible Grind to Real-Time AI Canvas Manipulation

At 25, I asked myself an uncomfortable question: why was I learning so much without having much to show for it?

I had spent time on coding, marketing, cybersecurity, design, and automation. I had built things and learned a lot, but much of that work stayed on my machine. No public project, no feedback loop, no proof someone else could use. I called it **the invisible grind**.

The change was deciding to learn, build, **publish**, and improve in public instead of waiting for everything to feel perfect. This repository is one of those attempts to ship something useful and let others make it better.

## The spark

While experimenting with OpenCode and reasoning models such as DeepSeek and GPT, I wanted natural-language instructions to result in editable visual work. A flattened AI-generated image is hard to revise: you cannot reliably change the headline, align the button, swap a brand color, or reorganize the layout.

I wanted the model to reason about the design, then manipulate real canvas objects—frames, text, vectors, and layout rules—that a person could inspect and continue editing.

## Finding the canvas

[Storytold's FigCraft](https://github.com/storytold/figcraft) offered the missing bridge: a native design app with a CLI and an MCP server backed by its command engine. With FigCraft running locally, OpenCode can use `figcraft-cli mcp --attach` to issue canvas operations and get inspection results back.

The flow became tangible:

1. Describe the desired layout, dimensions, visual hierarchy, and brand constraints to OpenCode.
2. Let a configured model plan the structure and use available tools to find local assets.
3. Send native edits through FigCraft's MCP tools to the live canvas.
4. Inspect the document and screenshot, revise what looks wrong, and save or export the result.

The agent is not the designer of record: a person still reviews the work, verifies assets and rights, and chooses what to publish. But watching a real, editable canvas change in response to a prompt makes the process feel less like image generation and more like collaborative production.

## Why publish it?

This repository shares the configuration pattern, architecture notes, and reusable prompts that made the workflow approachable. It does not repackage FigCraft or promise perfect designs. It is an invitation to try the pipeline, improve the prompts, and share what worked.

No more learning in the shadows. Build, ship, and iterate in public.
