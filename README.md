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

| Approach          | Description                           | Examples                                                              |
| ----------------- | ------------------------------------- | --------------------------------------------------------------------- |
| **Constrained**   | AI selects from registered components | Vercel AI SDK `streamUI()`, A2UI, Crayon, assistant-ui, Tambo, v0.dev |
| **Unconstrained** | AI generates raw HTML/CSS/JS directly | Google GenUI research, Claude Artifacts, MCP Apps, OpenAI Canvas      |

**Constrained systems** are safer and more consistent — the AI can only use pre-approved components with known behavior. Trade-off: less flexible, requires component development upfront.

**Unconstrained systems** are more powerful — the AI can create anything. Trade-off: needs sandboxing (iframe, E2B), potential for inconsistency, and security considerations.

Google's research (see below) suggests unconstrained generation may become the dominant approach as models improve. They found users preferred AI-generated HTML/CSS over markdown 83% of the time, calling it an "emergent capability" — models produce good UIs without UI-specific training.

In practice, the 2025–2026 wave of production systems has leaned the other way. AG-UI, Google's own A2UI spec, the MCP Apps extension, and SDKs like Crayon and assistant-ui all standardize on *constrained / declarative* output — agents emit allow-listed components rather than raw code — for safety and consistency. Both ends of the spectrum are advancing in parallel rather than one cleanly winning.

***

## Research

### Papers

* [Generative UI: LLMs are Effective UI Generators](https://generativeui.github.io/static/pdfs/paper.pdf) - Google (2025). Foundational paper making the case for unconstrained UI generation. Key findings: (1) Users preferred AI-generated HTML/CSS/JS over markdown 83% of the time, (2) UI generation is an "emergent capability" requiring no UI-specific training, (3) Vision of "infinite ephemeral interfaces" - every query gets a custom UI. ([Project page](https://generativeui.github.io/)).
* [WebArena: A Realistic Web Environment for Building Autonomous Agents](https://arxiv.org/abs/2307.13854) - CMU (2023). Framework for evaluating web agents, relevant to UI generation and interaction.
* [Pix2Struct: Screenshot Parsing as Pretraining for Visual Language Understanding](https://arxiv.org/abs/2210.03347) - Google (2023). Vision-language model pretrained on web screenshots. Foundation for screenshot-to-code approaches.

*Note: Generative UI is an emerging field. Much knowledge lives in blog posts, SDKs, and industry practice rather than academic papers.*

***

## Benchmarks & Datasets

*Benchmarks for evaluating UI generation and datasets that enable screenshot-to-code, layout understanding, and web-agent interaction.*

* [WebArena](https://github.com/web-arena-x/webarena) ⭐ 1,587 | 🐛 101 | 🌐 Python | 📅 2025-11-26 - Realistic web environment and benchmark for agents interacting with live websites.
* [Mind2Web](https://github.com/OSU-NLP-Group/Mind2Web) ⭐ 1,021 | 🐛 12 | 🌐 Jupyter Notebook | 📅 2025-11-05 - Dataset and benchmark for generalist web agents grounded in real webpages.
* [VisualWebArena](https://github.com/web-arena-x/visualwebarena) ⭐ 485 | 🐛 27 | 🌐 Python | 📅 2024-11-09 - Vision-grounded WebArena variant for UI understanding and interaction.
* [MiniWoB++](https://github.com/Farama-Foundation/miniwob-plusplus) ⭐ 397 | 🐛 3 | 🌐 HTML | 📅 2026-08-13 - Standard suite of web UI interaction tasks used for agent evaluation.
* [Design2Code](https://arxiv.org/abs/2403.03163) - Benchmark for converting designs/screenshots into front-end code (Stanford/Google, 2024).
* [StructEval](https://arxiv.org/abs/2505.20139) - Benchmark for LLM generation and conversion across 18 structured formats, including rendered HTML, React, and SVG (TIGER-AI-Lab, TMLR 2025).
* [RICO](https://www.interactionmining.org/archive/rico) - Large-scale dataset of mobile app UIs (screens + view hierarchies).
* [pix2code](https://arxiv.org/abs/1705.07962) - Early reference paper on UI screenshot-to-code generation (Tony Beltramelli, 2017).

***

## Specifications & Protocols

### Open Specifications

* [Model Context Protocol (MCP) Servers](https://github.com/modelcontextprotocol/servers) ⭐ 89,854 | 🐛 543 | 🌐 TypeScript | 📅 2026-08-20 - Reference implementations of MCP servers and tooling.
* [mcp-ui](https://github.com/idosal/mcp-ui) ⭐ 5,101 | 🐛 60 | 🌐 TypeScript | 📅 2026-07-08 - Ido Salomon and Liad Yosef. Community SDK (TypeScript, Ruby, Python) for serving interactive UI over MCP; pioneered the pattern that fed the official MCP Apps spec.
* [MCP Apps (UI Extension)](https://github.com/modelcontextprotocol/ext-apps) ⭐ 2,761 | 🐛 205 | 🌐 TypeScript | 📅 2026-08-12 - The official MCP extension for interactive UI: tools return UI resources that render in an iframe inside the host client. Builds on mcp-ui and the OpenAI Apps SDK ([announcement](https://blog.modelcontextprotocol.io/posts/2026-01-26-mcp-apps/)).
* [Model Context Protocol (MCP)](https://modelcontextprotocol.io/) - Anthropic. Protocol for connecting AI models to external data sources and tools.
* [OpenAI Apps SDK](https://developers.openai.com/apps-sdk) - OpenAI. Build apps that render interactive React UI inline in ChatGPT; extends MCP with a UI layer via the MCP Apps bridge.

### Agent-to-UI Protocols

*Protocols that connect agent backends to front ends and describe the UI that agents emit.*

* [A2UI](https://github.com/google/a2ui) ⭐ 16,206 | 🐛 327 | 🌐 TypeScript | 📅 2026-08-25 - Google. Declarative, streaming (JSONL) generative-UI spec: agents request allow-listed components that the client renders natively (Flutter, Angular, Lit). A data format, not executable code ([site](https://a2ui.org/)).
* [AG-UI (Agent-User Interaction Protocol)](https://github.com/ag-ui-protocol/ag-ui) ⭐ 15,542 | 🐛 352 | 🌐 Python | 📅 2026-08-25 - CopilotKit. Open, event-based protocol that streams agent activity and shared state to front ends over SSE. Adopted by Google, LangChain, AWS, Microsoft, Mastra, and PydanticAI.

### How the Layers Fit Together

These specs are largely complementary rather than competing — they standardize different layers of the same stack:

| Layer                       | What it standardizes                          | Examples                                                 |
| --------------------------- | --------------------------------------------- | -------------------------------------------------------- |
| Transport / events          | How agent activity and state stream to the UI | AG-UI                                                    |
| UI description format       | How the UI itself is described in the payload | A2UI (declarative components), MCP Apps (HTML resources) |
| In-client rendering surface | Where generated UI renders in a host chat app | MCP Apps, OpenAI Apps SDK, mcp-ui                        |

***

## Frameworks & SDKs

### Generative UI Frameworks

Purpose-built for streaming AI-generated interfaces:

* [CopilotKit](https://github.com/CopilotKit/CopilotKit) ⭐ 37,034 | 🐛 351 | 🌐 TypeScript | 📅 2026-08-25 - Full-stack framework for in-app agents and generative UI across React, Angular, mobile, and Slack; makers of the AG-UI protocol.
* [assistant-ui](https://github.com/assistant-ui/assistant-ui) ⭐ 11,840 | 🐛 49 | 🌐 TypeScript | 📅 2026-08-25 - TypeScript/React primitives for AI chat with a first-class generative-UI primitive that renders agent-described components from a consumer-provided allowlist.
* [Tambo](https://github.com/tambo-ai/tambo) ⭐ 11,167 | 🐛 38 | 🌐 TypeScript | 📅 2026-08-25 - Generative UI SDK for React, purpose-built for streaming AI-generated components.
* [OpenUI Lang (Thesys)](https://github.com/thesysdev/openui) ⭐ 8,455 | 🐛 142 | 🌐 TypeScript | 📅 2026-08-25 - MIT-licensed, streaming-first DSL and React runtime for model-generated UI; a compact, token-efficient alternative to emitting component JSON.
* [Hashbrown](https://github.com/liveloveapp/hashbrown) ⭐ 719 | 🐛 8 | 🌐 TypeScript | 📅 2026-08-19 - Framework for building generative user interfaces in Angular and React.
* [mdocUI](https://github.com/mdocui/mdocui) ⭐ 39 | 🐛 0 | 🌐 TypeScript | 📅 2026-04-11 - Streaming generative UI using Markdoc `{% %}` tag syntax. Framework-agnostic core with React renderer, 24 theme-neutral components, and Zod schema validation.
* [Vercel AI SDK](https://sdk.vercel.ai/) - Vercel. TypeScript toolkit with `streamUI()` for server-streamed components and multi-provider support; v5 adds React/Vue/Svelte parity and SSE-based streaming.
* [Cuttlekit](https://cuttlekit.com) - Fully generative UI framework, framework agnostic, optimised for performance and real-time UI generation.
* [Thesys C1](https://www.thesys.dev/) - Thesys. OpenAI-compatible API that returns rendered UI instead of text, paired with the MIT-licensed Crayon React toolkit (built on Radix and shadcn/ui patterns).

### Supporting Libraries

Building blocks for reliable generation:

* [Mitosis](https://github.com/BuilderIO/mitosis) ⭐ 13,902 | 🐛 176 | 🌐 TypeScript | 📅 2026-07-21 - Builder.io. Write components once and compile to React/Vue/Svelte/etc.
* [Instructor](https://github.com/jxnl/instructor) ⭐ 13,779 | 🐛 50 | 🌐 Python | 📅 2026-08-09 - Structured output extraction; useful with UI schemas for reliable generation.

***

## Tools & Platforms

### Component Generators

*"Describe a component, get code."*

* [OpenUI (Weights & Biases)](https://github.com/wandb/openui) ⭐ 22,515 | 🐛 88 | 🌐 TypeScript | 📅 2026-08-22 - Describe UI in natural language and see it rendered live.
* [openv0](https://github.com/raidendotai/openv0) ⭐ 3,951 | 🐛 35 | 🌐 TypeScript | 📅 2024-09-19 - Open source v0 clone that generates React/Tailwind components from prompts.
* [v0.dev](https://v0.dev/) - Vercel (commercial). Generate React/Tailwind components from prompts; known for shadcn/ui integration.

### App Builders

*"Describe an app, get a deployable project."*

* [Cofounder](https://github.com/raidendotai/cofounder) ⭐ 6,657 | 🐛 68 | 🌐 TypeScript | 📅 2024-11-08 - Open source full-stack app generator with generative UI, databases, and APIs.
* [Bolt.new](https://bolt.new/) - StackBlitz (commercial). Generate full-stack applications in-browser with live preview.
* [Lovable](https://lovable.dev/) - Generate and deploy web apps from descriptions (commercial).

### Design-to-Code

*"Convert visuals to working code."*

* [Screenshot-to-Code](https://github.com/abi/screenshot-to-code) ⭐ 74,458 | 🐛 129 | 🌐 Python | 📅 2026-08-14 - Convert screenshots or designs to HTML/React/Vue code.
* [tldraw make-real](https://makereal.tldraw.com/) - Turn a wireframe into a working React component.
* [Galileo AI](https://www.usegalileo.ai/) - Generate editable UI designs from text descriptions (commercial, Figma-compatible).
* [Google Stitch](https://stitch.withgoogle.com/) - Google Labs. Generates web and mobile UI designs plus front-end code from prompts, screenshots, or sketches.
* [Figma Make](https://www.figma.com/make/) - Figma. Prompt-to-UI inside Figma that builds working interfaces using your existing components and design system.
* [Magic Patterns](https://www.magicpatterns.com/) - Generate and iterate on React UI from prompts; can learn and apply an existing design system.
* [Subframe](https://www.subframe.com/) - AI design tool built for code: compose production-ready React/Tailwind components on a canvas and export clean code.

### MCP Tools for UI

*"Model Context Protocol servers that improve AI UI generation."*

* [Playwright MCP](https://github.com/microsoft/playwright-mcp) ⭐ 36,451 | 🐛 6 | 🌐 TypeScript | 📅 2026-08-21 - Microsoft. MCP server for browser automation and UI regression testing workflows.
* [Figma-Context-MCP](https://github.com/GLips/Figma-Context-MCP) ⭐ 15,710 | 🐛 25 | 🌐 TypeScript | 📅 2026-08-07 - GLips. Provides Figma layout information to AI coding agents.
* [Cursor Talk to Figma MCP](https://github.com/grab/cursor-talk-to-figma-mcp) ⭐ 6,987 | 🐛 87 | 🌐 JavaScript | 📅 2026-07-26 - Grab. MCP server + Figma plugin for reading and modifying Figma designs.
* [21st.dev Magic MCP](https://github.com/21st-dev/magic-mcp) ⭐ 5,729 | 🐛 1 | 🌐 JavaScript | 📅 2026-07-31 - 21st.dev. Generate UI components from prompts with design-system awareness.
* [shadcn-ui-mcp-server](https://github.com/Jpisnice/shadcn-ui-mcp-server) ⭐ 2,958 | 🐛 3 | 🌐 TypeScript | 📅 2026-05-16 - Jpisnice. Helps LLMs understand shadcn/ui component structure.
* [Storybook MCP](https://github.com/storybookjs/mcp) ⭐ 268 | 🐛 48 | 🌐 TypeScript | 📅 2026-08-21 - Storybook. MCP server and addon that exposes component information and workflows from your local Storybook ([npm](https://www.npmjs.com/package/@storybook/mcp)).
* [shadcn-vue-mcp](https://github.com/HelloGGX/shadcn-vue-mcp) ⭐ 109 | 🐛 2 | 🌐 TypeScript | 📅 2025-11-16 - HelloGGX. MCP server for shadcn-vue component knowledge.
* [MUI MCP](https://mui.com/x/introduction/mcp/) - MUI. MCP server for MUI docs and code examples, published as `@mui/mcp` ([npm](https://www.npmjs.com/package/@mui/mcp)).

### AI Products with UI Generation

*"Chat interfaces that can generate UIs."*

* [Claude](https://claude.ai/) - Anthropic (commercial). Artifacts generates interactive React/HTML UIs in chat.
* [ChatGPT](https://chatgpt.com/) - OpenAI (commercial). Canvas supports UI generation and editing.

***

## Security & Sandboxing

*When UIs (or UI code) are model-generated, treat outputs as untrusted. These resources cover browser sandboxing, sanitization, and LLM-app security pitfalls.*

* [DOMPurify](https://github.com/cure53/DOMPurify) ⭐ 17,334 | 🐛 0 | 🌐 JavaScript | 📅 2026-08-24 - Widely used HTML sanitizer for untrusted/generated markup.
* [E2B](https://github.com/e2b-dev/e2b) ⭐ 13,554 | 🐛 45 | 🌐 Python | 📅 2026-08-25 - Sandboxed code execution environments for running untrusted generated code.
* [Sandpack](https://github.com/codesandbox/sandpack) ⭐ 6,221 | 🐛 159 | 🌐 TypeScript | 📅 2025-04-24 - CodeSandbox. In-browser code sandboxing patterns for safe previews.
* [OWASP Top 10 for LLM Applications](https://owasp.org/www-project-top-10-for-large-language-model-applications/) - OWASP. Threat model checklist for LLM apps (prompt injection, insecure tool use, data leakage).
* [OWASP Prompt Injection Prevention Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/LLM_Prompt_Injection_Prevention_Cheat_Sheet.html) - OWASP. Practical mitigations for prompt/tool injection in production systems.
* [Content Security Policy (CSP)](https://developer.mozilla.org/en-US/docs/Web/HTTP/Guides/CSP) - MDN. Core browser control for limiting script execution and exfiltration.
* [iframe sandbox](https://developer.mozilla.org/en-US/docs/Web/HTML/Reference/Elements/iframe) - MDN. Reference for sandboxed iframes to contain untrusted HTML/JS UI previews.
* [Trusted Types](https://web.dev/articles/trusted-types) - Mitigation against DOM XSS when inserting generated HTML into the DOM.

***

## Evaluation & Testing

*Tools and practices for regression testing generated UIs, validating structured outputs, and red-teaming LLM apps.*

* [promptfoo](https://github.com/promptfoo/promptfoo) ⭐ 24,567 | 🐛 513 | 🌐 TypeScript | 📅 2026-08-25 - Prompt and tool-call regression testing across models.
* [garak](https://github.com/NVIDIA/garak) ⭐ 9,029 | 🐛 407 | 🌐 Python | 📅 2026-08-21 - NVIDIA. LLM vulnerability scanner for automated probing.
* [PyRIT](https://github.com/Azure/PyRIT) ⚠️ Archived - Microsoft. Red teaming toolkit for LLM apps (jailbreaks, prompt injection).
* [Playwright](https://playwright.dev/) - Microsoft. E2E testing and screenshot diffs for UI regression testing.
* [Storybook Test Runner](https://storybook.js.org/docs/writing-tests/integrations/test-runner) - Storybook. Automates component-level interaction testing.

***

## Schemas & Patterns

*Make model outputs reliable: constrain generation, validate payloads, and standardize tool/component contracts.*

* [Zod](https://github.com/colinhacks/zod) ⭐ 43,527 | 🐛 87 | 🌐 TypeScript | 📅 2026-08-25 - Colin Hacks. TypeScript schema validation for enforcing output shapes at runtime.
* [Guidance](https://github.com/guidance-ai/guidance) ⭐ 21,714 | 🐛 319 | 🌐 Jupyter Notebook | 📅 2026-05-21 - Grammar- and constraint-oriented prompting for structured outputs.
* [Outlines](https://github.com/dottxt-ai/outlines) ⭐ 15,692 | 🐛 165 | 🌐 Python | 📅 2026-08-25 - Structured generation with JSON/grammar constraints.
* [JSON Schema](https://json-schema.org/) - Standard for validating structured model outputs.
* [OpenAPI Specification](https://spec.openapis.org/oas/latest.html) - OpenAPI Initiative. Standard contract format for APIs and tool catalogs.
* [Vercel AI SDK](https://sdk.vercel.ai/docs) - Vercel. Streaming UI and tool-calling patterns for generative UI applications.
* [shadcn/ui Registry](https://ui.shadcn.com/docs/registry) - Component registry pattern commonly targeted by UI generators.

***

## Component Libraries

### Generation Targets

*Libraries that AI generators commonly output to:*

* [shadcn/ui](https://ui.shadcn.com/) - Copy-paste components that many generators target.
* [Radix UI](https://www.radix-ui.com/) - Radix. Unstyled primitives that shadcn/ui builds on.
* [Tremor](https://tremor.so/) - Dashboard components often used for generated data UIs.
* [React Native Paper](https://reactnativepaper.com/) - Material Design components for React Native.

### For Building AI/Chat Interfaces

*Components designed for LLM-powered apps:*

* [LangUI](https://github.com/LangbaseInc/langui) ⭐ 3,144 | 🐛 6 | 🌐 HTML | 📅 2024-07-10 - LangbaseInc. Tailwind components for chat, AI assistants, and LLM projects.
* [Markstream](https://github.com/Simon-He95/markstream-vue) ⭐ 2,965 | 🐛 3 | 🌐 Vue | 📅 2026-08-25 - Multi-framework streaming Markdown components for AI chat, with Mermaid, KaTeX, code highlighting, safe HTML, and SSR support.
* [GPT-Vis](https://github.com/antvis/GPT-Vis) ⭐ 772 | 🐛 4 | 🌐 TypeScript | 📅 2026-06-26 - AntV. Visualization components designed for LLM-generated outputs.
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

* [ai-chatbot](https://github.com/vercel/ai-chatbot) ⭐ 20,871 | 🐛 27 | 🌐 TypeScript | 📅 2026-07-08 - Vercel. Full-featured chatbot with generative UI using the AI SDK.
* [morphic](https://github.com/miurla/morphic) ⭐ 9,061 | 🐛 57 | 🌐 TypeScript | 📅 2026-08-25 - AI-powered search engine with generative answer UI.
* [termite](https://github.com/shobrook/termite) ⭐ 419 | 🐛 7 | 🌐 Python | 📅 2025-01-06 - Terminal-based generative UI.
* [langgraphjs-gen-ui-examples](https://github.com/langchain-ai/langgraphjs-gen-ui-examples) ⚠️ Archived - LangChain. Collection of generative UI agents built with LangGraph.js.
* [gen-ui](https://github.com/bracesproul/gen-ui) ⭐ 293 | 🐛 3 | 🌐 TypeScript | 📅 2024-08-01 - Reference implementation for generative UI with LangChain.js, AI SDK, and Next.js.

***

## Articles & Talks

* [Vercel: Introducing AI SDK 3.0 with Generative UI](https://vercel.com/blog/ai-sdk-3-generative-ui) - Vercel blog post introducing Generative UI features in the AI SDK.
* [Building effective agents](https://www.anthropic.com/engineering/building-effective-agents) - Anthropic engineering post on agent design patterns relevant to tool-using UI systems.
* [Introducing A2UI: An open project for agent-driven interfaces](https://developers.googleblog.com/introducing-a2ui-an-open-project-for-agent-driven-interfaces/) - Google Developers blog introducing the A2UI declarative generative-UI spec.
* [AG-UI Protocol: Bridging Agents to Any Front End](https://www.copilotkit.ai/blog/ag-ui-protocol-bridging-agents-to-any-front-end) - CopilotKit post explaining the agent-to-frontend interaction protocol.

***

## Related Concepts

### Adjacent Fields

Design-to-code converts designs (e.g., Figma) to code. Code generation is the broader field of AI-generated code. Conversational UI covers chat interfaces (related but distinct). Low-code/no-code includes visual builders that generative UI can automate.

* [LangChain.js](https://js.langchain.com/) - LangChain. LLM framework often used to build tool-using agents that can drive generative UI.
* [Mastra](https://mastra.ai/) - TypeScript framework for AI applications with patterns adaptable to generative UI.

### Evaluation tooling (adjacent)

* [OpenAI Evals](https://github.com/openai/evals) ⭐ 19,253 | 🐛 323 | 🌐 Python | 📅 2026-04-14 - OpenAI. Framework for evaluating model outputs with custom tasks and graders.
* [TruLens](https://github.com/truera/trulens) ⭐ 3,522 | 🐛 64 | 🌐 Python | 📅 2026-08-25 - TruEra. Evaluation and feedback tooling for LLM applications.
* [LangSmith Evaluation](https://docs.smith.langchain.com/evaluation) - LangChain. Evaluation patterns and workflows for LLM apps.

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

> _Enhansomed by [enhansome](https://github.com/enhansome) on 2026-08-25._
