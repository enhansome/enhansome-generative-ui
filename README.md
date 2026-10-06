# Awesome Generative UI with stars

> A curated list of resources for AI-generated user interfaces — systems where LLMs dynamically create, compose, and render UI components.

Generative UI represents a paradigm shift from "AI assists coding" to "AI generates interfaces directly." Instead of writing components, you describe what you need and the AI produces a working UI.

## Contents

* [What is Generative UI](#what-is-generative-ui)
* [Key Concepts](#key-concepts)
* [Research](#research)
* [Benchmarks & Datasets](#benchmarks--datasets)
* [Specifications & Protocols](#specifications--protocols)
* [Frameworks & SDKs](#frameworks--sdks)
* [Tools & Platforms](#tools--platforms)
* [Security & Sandboxing](#security--sandboxing)
* [Evaluation & Testing](#evaluation--testing)
* [Schemas & Patterns](#schemas--patterns)
* [Component Libraries](#component-libraries)
* [Examples & Demos](#examples--demos)
* [Articles & Talks](#articles--talks)
* [Related Concepts](#related-concepts)

***

## What is Generative UI

**Generative UI** is the practice of using AI models (typically LLMs) to dynamically generate user interface components at runtime, rather than having developers hand-code every interface.

### The Spectrum

| Dimension             | Traditional UI                  | AI-Assisted UI                        | Generative UI                             |
| --------------------- | ------------------------------- | ------------------------------------- | ----------------------------------------- |
| Primary workflow      | Developers hand-code components | Copilot helps developers write code   | Model generates UI components from intent |
| Developer involvement | High (author everything)        | High (developer in the loop)          | Lower per UI (developer sets constraints) |
| Runtime behavior      | Static UI                       | Static UI (generation happens in dev) | Dynamic / runtime-generated UI            |
| Typical output        | Source code                     | Source code                           | Components, schemas, or rendered UI       |
| Best for              | Predictability, control         | Faster delivery with human oversight  | Personalization and rapid UI composition  |

### Why It Matters

Generative UI enables **personalization at scale**, **rapid prototyping**, and **natural language interfaces** (e.g., "Show me sales by region" produces an actual chart), while reducing development time by shifting effort from implementation details to intent.

### Challenges

Challenges include **Consistency** (generated UIs may look different each time), **Performance** (generation takes time; streaming helps), **Safety** (arbitrary code generation has security implications), and **Hallucination** (AI might generate components that don't exist).

### How the Field Evolved

**2023–2024: framework-bound streaming.** Vercel's AI SDK 3.0 popularized the term with `streamUI()`: a model calls a tool, the server streams back a React Server Component. Powerful, but tied to one framework and one vendor's runtime.

**2024–2025: prompt-to-app products.** v0, Bolt.new, Lovable, and Google Stitch made "describe it, get a UI" a product category, while Google's research argued that models can generate full HTML/CSS/JS at runtime without UI-specific training.

**Late 2025–mid 2026: standardization into layers.** The stack split into interoperable specs: AG-UI for streaming agent events, A2UI and OpenUI Lang for describing UI, and MCP Apps (plus OpenAI's Apps SDK) as the shared surface for rendering UI inside chat hosts. Production systems mostly settled on constrained, allow-listed components.

**Late 2026: consolidation.** SDKs are moving from React-first to first-class Vue, Svelte, and Angular support, specs are heading toward stable 1.0 releases, and models are starting to be trained specifically to emit UI description languages. Meanwhile unconstrained generation has shipped to consumers in Gemini and Google Search, so both ends of the spectrum are now in production.

***

## Key Concepts

### Component Registry

A predefined set of UI components the AI can use. Instead of generating arbitrary HTML/CSS, the AI selects from known, tested components.

```
Registry: [BarChart, LineChart, DataTable, KPICard]
Prompt: "Show monthly revenue"
Output: { component: "BarChart", props: { data: [...] } }
```

### Query Registry

A predefined set of data sources the AI can query. Prevents hallucinated API calls.

```
Registry: [getUsers, getRevenue, getOrders]
Prompt: "Show top customers"
Output: { query: "getUsers", params: { sort: "revenue", limit: 10 } }
```

### Streaming UI

Rendering UI components as they're generated, rather than waiting for complete output.

### Server Components

React Server Components enable streaming UI from server to client, making generative UI more practical.

### Structured Output

Using JSON schemas or function calling to ensure AI outputs valid, parseable UI descriptions.

### Constrained vs Unconstrained Generation

A fundamental design decision in generative UI systems:

| Approach          | Description                           | Examples                                                               |
| ----------------- | ------------------------------------- | ---------------------------------------------------------------------- |
| **Constrained**   | AI selects from registered components | A2UI, OpenUI, assistant-ui, Tambo, CopilotKit, Vercel AI SDK UI        |
| **Unconstrained** | AI generates raw HTML/CSS/JS directly | Google GenUI research, Gemini dynamic view, Claude Artifacts, MCP Apps |

**Constrained systems** are safer and more consistent — the AI can only use pre-approved components with known behavior. Trade-off: less flexible, requires component development upfront.

**Unconstrained systems** are more powerful — the AI can create anything. Trade-off: needs sandboxing (iframe, E2B), potential for inconsistency, and security considerations.

Google's research (see below) suggests unconstrained generation may become the dominant approach as models improve. They found users preferred AI-generated HTML/CSS over markdown 83% of the time, calling it an "emergent capability" — models produce good UIs without UI-specific training.

In practice, the 2025–2026 wave of developer tooling has mostly leaned the other way. Google's own A2UI spec and SDKs like OpenUI, assistant-ui, and CopilotKit standardize on *constrained / declarative* output — agents emit allow-listed components rather than raw code — for safety and consistency, while MCP Apps contains unconstrained HTML in sandboxed iframes. Both ends of the spectrum are advancing in parallel rather than one cleanly winning (see [How the Field Evolved](#how-the-field-evolved)).

***

## Research

### Papers

* [Generative UI: LLMs are Effective UI Generators](https://generativeui.github.io/static/pdfs/paper.pdf) - Google (2025). Foundational paper making the case for unconstrained UI generation. Key findings: (1) Users preferred AI-generated HTML/CSS/JS over markdown 83% of the time, (2) UI generation is an "emergent capability" requiring no UI-specific training, (3) Vision of "infinite ephemeral interfaces" - every query gets a custom UI. ([Project page](https://generativeui.github.io/)).
* [Pix2Struct: Screenshot Parsing as Pretraining for Visual Language Understanding](https://arxiv.org/abs/2210.03347) - Google (2023). Vision-language model pretrained on web screenshots. Foundation for screenshot-to-code approaches.

*Note: Generative UI is an emerging field. Much knowledge lives in blog posts, SDKs, and industry practice rather than academic papers.*

***

## Benchmarks & Datasets

*Benchmarks for evaluating UI generation and datasets that enable screenshot-to-code, layout understanding, and web-agent interaction.*

* [WebArena](https://github.com/web-arena-x/webarena) ⭐ 1,620 | 🐛 104 | 🌐 Python | 📅 2025-11-26 - Realistic web environment and benchmark for agents interacting with live websites.
* [Mind2Web](https://github.com/OSU-NLP-Group/Mind2Web) ⭐ 1,032 | 🐛 14 | 🌐 Jupyter Notebook | 📅 2025-11-05 - Dataset and benchmark for generalist web agents grounded in real webpages.
* [VisualWebArena](https://github.com/web-arena-x/visualwebarena) ⭐ 488 | 🐛 28 | 🌐 Python | 📅 2024-11-09 - Vision-grounded WebArena variant for UI understanding and interaction.
* [MiniWoB++](https://github.com/Farama-Foundation/miniwob-plusplus) ⭐ 403 | 🐛 3 | 🌐 HTML | 📅 2026-08-13 - Standard suite of web UI interaction tasks used for agent evaluation.
* [Design2Code](https://arxiv.org/abs/2403.03163) - Benchmark for converting designs/screenshots into front-end code (Stanford/Google, 2024).
* [StructEval](https://arxiv.org/abs/2505.20139) - Benchmark for LLM generation and conversion across 18 structured formats, including rendered HTML, React, and SVG (TIGER-AI-Lab, TMLR 2025).
* [ArtifactsBench](https://arxiv.org/abs/2507.04952) - Benchmark of 1,825 tasks for generating visual, interactive artifacts (web apps, SVG, games), scored by an MLLM judge from rendered screenshots (Tencent Hunyuan, 2025).
* [Interaction2Code](https://arxiv.org/abs/2411.03292) - Benchmark for generating interactive webpages from interactive prototypes rather than static designs (ASE 2025).
* [WebDev Arena](https://arena.ai/leaderboard/webdev) - LMArena. Crowd-voted leaderboard where models build web apps side by side.
* [RICO](https://www.interactionmining.org/archive/rico) - Large-scale dataset of mobile app UIs (screens + view hierarchies).
* [WebSight](https://huggingface.co/datasets/HuggingFaceM4/WebSight) - Synthetic dataset of 2M websites paired with screenshots for screenshot-to-code training (Hugging Face, 2024; [paper](https://arxiv.org/abs/2403.09029)).
* [pix2code](https://arxiv.org/abs/1705.07962) - Early reference paper on UI screenshot-to-code generation (Tony Beltramelli, 2017).

***

## Specifications & Protocols

### Open Specifications

* [Model Context Protocol (MCP) Servers](https://github.com/modelcontextprotocol/servers) ⭐ 91,035 | 🐛 491 | 🌐 TypeScript | 📅 2026-10-05 - Reference implementations of MCP servers and tooling.
* [mcp-ui](https://github.com/MCP-UI-Org/mcp-ui) ⭐ 5,196 | 🐛 64 | 🌐 TypeScript | 📅 2026-09-16 - Ido Salomon and Liad Yosef. Community SDK (TypeScript, Ruby, Python) for serving interactive UI over MCP; pioneered the pattern that fed the official MCP Apps spec.
* [MCP Apps (UI Extension)](https://github.com/modelcontextprotocol/ext-apps) ⭐ 2,904 | 🐛 220 | 🌐 TypeScript | 📅 2026-09-25 - The official MCP extension for interactive UI: tools return UI resources that render in an iframe inside the host client. Builds on mcp-ui and the OpenAI Apps SDK ([announcement](https://blog.modelcontextprotocol.io/posts/2026-01-26-mcp-apps/)).
* [Model Context Protocol (MCP)](https://modelcontextprotocol.io/) - Anthropic. Protocol for connecting AI models to external data sources and tools.
* [OpenAI Apps SDK / ChatGPT Plugins](https://developers.openai.com/plugins) - OpenAI. Build plugins (called ChatGPT apps until July 2026) that render interactive UI inline in ChatGPT; extends MCP with a UI layer via the MCP Apps bridge.
* [Adaptive Cards](https://adaptivecards.microsoft.com/) - Microsoft. Platform-agnostic JSON card format rendered natively by host apps such as Teams and Outlook; a long-standing precursor to declarative agent UI specs.

### Agent-to-UI Protocols

*Protocols that connect agent backends to front ends and describe the UI that agents emit.*

* [A2UI](https://github.com/a2ui-project/a2ui) ⭐ 16,599 | 🐛 425 | 🌐 TypeScript | 📅 2026-10-06 - Google. Declarative, streaming (JSONL) generative-UI spec: agents request allow-listed components that the client renders natively (Flutter, Angular, Lit). A data format, not executable code ([site](https://a2ui.org/)).
* [AG-UI (Agent-User Interaction Protocol)](https://github.com/ag-ui-protocol/ag-ui) ⭐ 16,345 | 🐛 471 | 🌐 TypeScript | 📅 2026-10-06 - CopilotKit. Open, event-based protocol that streams agent activity and shared state to front ends over SSE. Adopted by Google, LangChain, AWS, Microsoft, Mastra, and PydanticAI.

### How the Layers Fit Together

These specs are largely complementary rather than competing — they standardize different layers of the same stack:

| Layer                       | What it standardizes                          | Examples                                                 |
| --------------------------- | --------------------------------------------- | -------------------------------------------------------- |
| Transport / events          | How agent activity and state stream to the UI | AG-UI                                                    |
| UI description format       | How the UI itself is described in the payload | A2UI (declarative components), MCP Apps (HTML resources) |
| In-client rendering surface | Where generated UI renders in a host chat app | MCP Apps, OpenAI Apps SDK (plugins), mcp-ui              |

***

## Frameworks & SDKs

### Generative UI Frameworks

Purpose-built for streaming AI-generated interfaces:

* [CopilotKit](https://github.com/CopilotKit/CopilotKit) ⭐ 37,773 | 🐛 301 | 🌐 TypeScript | 📅 2026-10-06 - Full-stack framework for in-app agents and generative UI across React, Angular, mobile, and Slack; makers of the AG-UI protocol.
* [json-render](https://github.com/vercel-labs/json-render) ⭐ 18,515 | 🐛 140 | 🌐 TypeScript | 📅 2026-10-02 - Vercel Labs. Apache-2.0 framework where models emit JSON constrained to a catalog of predefined components and actions, with renderers for React, React Native, Vue, Svelte, and Solid plus PDF, email, and video targets.
* [assistant-ui](https://github.com/assistant-ui/assistant-ui) ⭐ 12,418 | 🐛 329 | 🌐 TypeScript | 📅 2026-10-06 - TypeScript/React primitives for AI chat with a first-class generative-UI primitive that renders agent-described components from a consumer-provided allowlist.
* [Tambo](https://github.com/tambo-ai/tambo) ⭐ 11,184 | 🐛 49 | 🌐 TypeScript | 📅 2026-10-06 - Generative UI SDK for React, purpose-built for streaming AI-generated components.
* [OpenUI (Thesys)](https://github.com/thesysdev/openui) ⭐ 9,988 | 🐛 166 | 🌐 TypeScript | 📅 2026-10-06 - Thesys. MIT-licensed generative UI framework built around OpenUI Lang, a compact streaming DSL for model-generated component trees, with runtimes for React, Vue, Svelte, and Angular. Also powers the commercial [C1](https://www.thesys.dev/) API.
* [GenUI SDK for Flutter](https://github.com/flutter/genui) ⭐ 1,780 | 🐛 20 | 🌐 Dart | 📅 2026-10-02 - Flutter. Composes UIs from your existing widget catalog and feeds UI state back to the agent; supports A2UI. Experimental.
* [Hashbrown](https://github.com/liveloveapp/hashbrown) ⭐ 726 | 🐛 11 | 🌐 TypeScript | 📅 2026-10-03 - Framework for building generative user interfaces in Angular and React.
* [mdocUI](https://github.com/mdocui/mdocui) ⭐ 40 | 🐛 1 | 🌐 TypeScript | 📅 2026-09-26 - Streaming generative UI using Markdoc `{% %}` tag syntax. Framework-agnostic core with React renderer, 24 theme-neutral components, and Zod schema validation.
* [Vercel AI SDK](https://ai-sdk.dev/) - Vercel. Multi-provider TypeScript toolkit for React, Vue, Svelte, and Angular; generative UI is built by rendering typed tool results on the client ([guide](https://ai-sdk.dev/docs/ai-sdk-ui/generative-user-interfaces)). The original RSC-based `streamUI()` is experimental.
* [Cuttlekit](https://cuttlekit.com) - Fully generative UI framework, framework agnostic, optimised for performance and real-time UI generation.

### Supporting Libraries

Building blocks for reliable generation:

* [Instructor](https://github.com/567-labs/instructor) ⭐ 13,980 | 🐛 146 | 🌐 Python | 📅 2026-10-05 - Structured output extraction; useful with UI schemas for reliable generation.
* [DeepSeek Harness GenUI](https://github.com/pengyue-polaron/deepseek-harness-genui) ⭐ 115 | 🐛 1 | 🌐 TypeScript | 📅 2026-09-24 - PengYue. Community DeepSeek Harness plugin where the agent writes task-specific React UIs whose user inputs carry into later agent turns.

### Models

Models trained specifically to generate UI:

* [OUI-1](https://huggingface.co/thesysdev/OUI-1) - Thesys. Apache-2.0 diffusion model (DiffusionGemma finetune, 4B active parameters) that writes UI screens in OpenUI Lang from a component library and a plain-language brief, in about a second per screen.

***

## Tools & Platforms

### Component Generators

*"Describe a component, get code."*

* [OpenUI (Weights & Biases)](https://github.com/wandb/openui) ⭐ 22,566 | 🐛 88 | 🌐 TypeScript | 📅 2026-09-25 - Describe UI in natural language and see it rendered live.
* [v0](https://v0.app/) - Vercel (commercial). Generate React/Tailwind components from prompts; known for shadcn/ui integration.

### App Builders

*"Describe an app, get a deployable project."*

* [Bolt.new](https://bolt.new/) - StackBlitz (commercial). Generate full-stack applications in-browser with live preview.
* [Lovable](https://lovable.dev/) - Generate and deploy web apps from descriptions (commercial).

### Design-to-Code

*"Convert visuals to working code."*

* [Screenshot-to-Code](https://github.com/abi/screenshot-to-code) ⭐ 80,020 | 🐛 148 | 🌐 Python | 📅 2026-09-29 - Convert screenshots or designs to HTML/React/Vue code.
* [tldraw make-real](https://makereal.tldraw.com/) - Turn a wireframe into a working React component.
* [Google Stitch](https://stitch.withgoogle.com/) - Google Labs. Generates web and mobile UI designs plus front-end code from prompts, screenshots, or sketches.
* [Figma Make](https://www.figma.com/make/) - Figma. Prompt-to-UI inside Figma that builds working interfaces using your existing components and design system.
* [Magic Patterns](https://www.magicpatterns.com/) - Generate and iterate on React UI from prompts; can learn and apply an existing design system.
* [Subframe](https://www.subframe.com/) - AI design tool built for code: compose production-ready React/Tailwind components on a canvas and export clean code.

### MCP Tools for UI

*"Model Context Protocol servers that improve AI UI generation."*

* [Playwright MCP](https://github.com/microsoft/playwright-mcp) ⭐ 37,856 | 🐛 1 | 🌐 TypeScript | 📅 2026-09-28 - Microsoft. MCP server for browser automation and UI regression testing workflows.
* [Figma-Context-MCP](https://github.com/GLips/Figma-Context-MCP) ⭐ 15,957 | 🐛 26 | 🌐 TypeScript | 📅 2026-10-03 - GLips. Provides Figma layout information to AI coding agents.
* [Cursor Talk to Figma MCP](https://github.com/grab/cursor-talk-to-figma-mcp) ⭐ 7,048 | 🐛 88 | 🌐 JavaScript | 📅 2026-07-26 - Grab. MCP server + Figma plugin for reading and modifying Figma designs.
* [21st.dev Magic MCP](https://github.com/21st-dev/magic-mcp) ⭐ 5,974 | 🐛 6 | 🌐 TypeScript | 📅 2026-09-09 - 21st.dev. Generate UI components from prompts with design-system awareness.
* [shadcn-ui-mcp-server](https://github.com/Jpisnice/shadcn-ui-mcp-server) ⭐ 3,025 | 🐛 2 | 🌐 TypeScript | 📅 2026-10-03 - Jpisnice. Helps LLMs understand shadcn/ui component structure.
* [shadcn-vue-mcp](https://github.com/HelloGGX/shadcn-vue-mcp) ⭐ 108 | 🐛 2 | 🌐 TypeScript | 📅 2025-11-16 - HelloGGX. MCP server for shadcn-vue component knowledge.
* [MUI MCP](https://mui.com/x/introduction/mcp/) - MUI. MCP server for MUI docs and code examples, published as `@mui/mcp` ([npm](https://www.npmjs.com/package/@mui/mcp)).
* [Storybook MCP](https://storybook.js.org/docs/ai/mcp/overview) - Storybook. MCP server and addon that exposes component information and workflows from your local Storybook; part of Storybook core since v10.6 ([npm](https://www.npmjs.com/package/@storybook/mcp)).

### AI Products with UI Generation

*"Chat interfaces that can generate UIs."*

* [Claude](https://claude.ai/) - Anthropic (commercial). Artifacts generates interactive React/HTML UIs in chat.
* [ChatGPT](https://chatgpt.com/) - OpenAI (commercial). Canvas supports UI generation and editing.
* [Gemini](https://gemini.google/overview/) - Google (commercial). Dynamic view and visual layout generate a custom interactive interface per prompt; also available in Google Search AI Mode.

***

## Security & Sandboxing

*When UIs (or UI code) are model-generated, treat outputs as untrusted. These resources cover browser sandboxing, sanitization, and LLM-app security pitfalls.*

* [DOMPurify](https://github.com/cure53/DOMPurify) ⭐ 17,441 | 🐛 2 | 🌐 JavaScript | 📅 2026-10-05 - Widely used HTML sanitizer for untrusted/generated markup.
* [E2B](https://github.com/e2b-dev/e2b) ⭐ 14,194 | 🐛 86 | 🌐 Python | 📅 2026-10-05 - Sandboxed code execution environments for running untrusted generated code.
* [Sandpack](https://github.com/codesandbox/sandpack) ⭐ 6,245 | 🐛 163 | 🌐 TypeScript | 📅 2025-04-24 - CodeSandbox. In-browser code sandboxing patterns for safe previews.
* [OWASP Top 10 for LLM Applications](https://owasp.org/projects/top-10-for-large-language-model-applications) - OWASP. Threat model checklist for LLM apps (prompt injection, insecure tool use, data leakage).
* [OWASP Prompt Injection Prevention Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/LLM_Prompt_Injection_Prevention_Cheat_Sheet.html) - OWASP. Practical mitigations for prompt/tool injection in production systems.
* [Content Security Policy (CSP)](https://developer.mozilla.org/en-US/docs/Web/HTTP/Guides/CSP) - MDN. Core browser control for limiting script execution and exfiltration.
* [iframe sandbox](https://developer.mozilla.org/en-US/docs/Web/HTML/Reference/Elements/iframe) - MDN. Reference for sandboxed iframes to contain untrusted HTML/JS UI previews.
* [Trusted Types](https://web.dev/articles/trusted-types) - Mitigation against DOM XSS when inserting generated HTML into the DOM.

***

## Evaluation & Testing

*Tools and practices for regression testing generated UIs, validating structured outputs, and red-teaming LLM apps.*

* [promptfoo](https://github.com/promptfoo/promptfoo) ⭐ 25,739 | 🐛 712 | 🌐 TypeScript | 📅 2026-10-06 - Prompt and tool-call regression testing across models.
* [garak](https://github.com/NVIDIA/garak) ⭐ 9,450 | 🐛 487 | 🌐 Python | 📅 2026-10-02 - NVIDIA. LLM vulnerability scanner for automated probing.
* [PyRIT](https://github.com/microsoft/PyRIT) ⭐ 4,582 | 🐛 145 | 🌐 Python | 📅 2026-10-06 - Microsoft. Red teaming toolkit for LLM apps (jailbreaks, prompt injection).
* [Playwright](https://playwright.dev/) - Microsoft. E2E testing and screenshot diffs for UI regression testing.
* [Storybook Test Runner](https://storybook.js.org/docs/writing-tests/integrations/test-runner) - Storybook. Automates component-level interaction testing.

***

## Schemas & Patterns

*Make model outputs reliable: constrain generation, validate payloads, and standardize tool/component contracts.*

* [Zod](https://github.com/colinhacks/zod) ⭐ 44,065 | 🐛 86 | 🌐 TypeScript | 📅 2026-10-02 - Colin Hacks. TypeScript schema validation for enforcing output shapes at runtime.
* [Guidance](https://github.com/guidance-ai/guidance) ⭐ 21,787 | 🐛 343 | 🌐 Jupyter Notebook | 📅 2026-05-21 - Grammar- and constraint-oriented prompting for structured outputs.
* [Outlines](https://github.com/dottxt-ai/outlines) ⭐ 15,899 | 🐛 188 | 🌐 Python | 📅 2026-09-21 - Structured generation with JSON/grammar constraints.
* [JSON Schema](https://json-schema.org/) - Standard for validating structured model outputs.
* [OpenAPI Specification](https://spec.openapis.org/oas/latest.html) - OpenAPI Initiative. Standard contract format for APIs and tool catalogs.
* [shadcn/ui Registry](https://ui.shadcn.com/docs/registry) - Component registry pattern commonly targeted by UI generators.

***

## Component Libraries

### Generation Targets

*Libraries that AI generators commonly output to:*

* [shadcn/ui](https://ui.shadcn.com/) - Copy-paste components that many generators target.
* [Radix UI](https://www.radix-ui.com/) - Radix. Unstyled primitives that shadcn/ui builds on.
* [Tremor](https://www.tremor.so/) - Dashboard components often used for generated data UIs.
* [React Native Paper](https://reactnativepaper.com/) - Material Design components for React Native.

### For Building AI/Chat Interfaces

*Components designed for LLM-powered apps:*

* [Markstream](https://github.com/Simon-He95/markstream-vue) ⭐ 3,026 | 🐛 1 | 🌐 Vue | 📅 2026-10-05 - Multi-framework streaming Markdown components for AI chat, with Mermaid, KaTeX, code highlighting, safe HTML, and SSR support.
* [ChatKit](https://github.com/openai/chatkit-js) ⭐ 1,960 | 🐛 61 | 🌐 TypeScript | 📅 2026-07-31 - OpenAI. Embeddable chat UI framework with streaming, tool visualization, and agent-rendered interactive widgets.
* [GPT-Vis](https://github.com/antvis/GPT-Vis) ⭐ 787 | 🐛 4 | 🌐 TypeScript | 📅 2026-09-17 - AntV. Visualization components designed for LLM-generated outputs.
* [AI Elements](https://vercel.com/changelog/introducing-ai-elements) - Vercel. 20+ shadcn/ui-based React components for AI interfaces (message threads, reasoning panels, tool output), integrated with the AI SDK.

### Visualization

*For AI-generated charts and data displays:*

* [Recharts](https://recharts.org/) - Composable React charts commonly used in dashboards.
* [Vega-Lite](https://vega.github.io/vega-lite/) - Vega. Declarative JSON grammar for charts and visualizations.
* [Observable Plot](https://observablehq.com/plot/) - Observable. Grammar of graphics for JS with concise specs.
* [D3.js](https://d3js.org/) - D3. Low-level but powerful library for custom visualizations.

***

## Examples & Demos

### Open Source

* [Chatbot](https://github.com/vercel/chatbot) ⭐ 20,991 | 🐛 28 | 🌐 TypeScript | 📅 2026-07-08 - Vercel. Full-featured chatbot with generative UI using the AI SDK.
* [morphic](https://github.com/miurla/morphic) ⭐ 9,152 | 🐛 55 | 🌐 TypeScript | 📅 2026-10-04 - AI-powered search engine with generative answer UI.

***

## Articles & Talks

* [Vercel: Introducing AI SDK 3.0 with Generative UI](https://vercel.com/blog/ai-sdk-3-generative-ui) - Vercel blog post introducing Generative UI features in the AI SDK.
* [Generative UI: A rich, custom, visual interactive user experience for any prompt](https://research.google/blog/generative-ui-a-rich-custom-visual-interactive-user-experience-for-any-prompt/) - Google Research post on shipping unconstrained generative UI in the Gemini app and Google Search (2025).
* [Enabling teachers to create learning interactives with generative UI](https://research.google/blog/the-future-of-practice-enabling-teachers-to-create-learning-interactives-with-generative-ui/) - Google Research on generative UI with learning-design guardrails for classroom simulations (2026).
* [Building effective agents](https://www.anthropic.com/engineering/building-effective-agents) - Anthropic engineering post on agent design patterns relevant to tool-using UI systems.
* [Introducing A2UI: An open project for agent-driven interfaces](https://developers.googleblog.com/introducing-a2ui-an-open-project-for-agent-driven-interfaces/) - Google Developers blog introducing the A2UI declarative generative-UI spec.
* [AG-UI Protocol: Bridging Agents to Any Front End](https://www.copilotkit.ai/blog/ag-ui-protocol-bridging-agents-to-any-front-end) - CopilotKit post explaining the agent-to-frontend interaction protocol.
* [The Developer's Guide to Generative UI in 2026](https://www.copilotkit.ai/blog/the-developer-s-guide-to-generative-ui-in-2026) - CopilotKit overview of static, declarative, and open-ended generative UI and how the protocols fit together.

***

## Related Concepts

### Adjacent Fields

Design-to-code converts designs (e.g., Figma) to code. Code generation is the broader field of AI-generated code. Conversational UI covers chat interfaces (related but distinct). Low-code/no-code includes visual builders that generative UI can automate.

* [LangChain.js](https://docs.langchain.com/oss/javascript/langchain/overview) - LangChain. LLM framework often used to build tool-using agents that can drive generative UI.
* [Mastra](https://mastra.ai/) - TypeScript framework for AI applications with patterns adaptable to generative UI.

### Evaluation tooling (adjacent)

* [OpenAI Evals](https://github.com/openai/evals) ⭐ 19,560 | 🐛 345 | 🌐 Python | 📅 2026-04-14 - OpenAI. Framework for evaluating model outputs with custom tasks and graders.
* [TruLens](https://github.com/truera/trulens) ⭐ 3,590 | 🐛 91 | 🌐 Python | 📅 2026-10-06 - TruEra. Evaluation and feedback tooling for LLM applications.
* [LangSmith Evaluation](https://docs.langchain.com/langsmith/evaluation-concepts) - LangChain. Evaluation patterns and workflows for LLM apps.

### Standards & Formats

JSON Schema defines valid UI structures. OpenAPI describes APIs for query registries. Web Components are a framework-agnostic component standard.

***

## Contributing

Contributions welcome! Please read the [contribution guidelines](CONTRIBUTING.md) first.

### Criteria for Inclusion

Submissions should be relevant to generative UI (not general AI/LLM), high quality (well-documented and maintained or historically significant), and accessible (open source, free tier, or detailed documentation).

### What We're Looking For

New frameworks and tools, research papers and articles, interesting examples and demos, and corrections/updates.

***

License: CC BY 4.0 (see [LICENSE](LICENSE)).

***

> _Enhansomed by [enhansome](https://github.com/enhansome) on 2026-10-06._
