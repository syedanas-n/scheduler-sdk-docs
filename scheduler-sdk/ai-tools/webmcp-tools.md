---
layout: post
title: WebMCP Tools for Scheduler - Syncfusion
description: Learn here about the WebMCP tools that enable AI agents to browse, manage, and automate scheduling operations in the Syncfusion Scheduler.
platform: scheduler-sdk
control: Scheduler
documentation: ug
---

# WebMCP Tools for Scheduler SDK

**WebMCP** (Model Context Protocol) brings AI automation capabilities directly into the Syncfusion® Scheduler. Instead of AI agents attempting to read the DOM or simulate user clicks, web pages explicitly declare their capabilities through structured tool definitions — giving agents reliable, deterministic ways to interact with applications.

## What is WebMCP tools?

WebMCP is a proposed web standard that enables web applications to expose functionality as structured tools that AI agents can invoke directly. Each tool includes:

- **Natural Language Description** — Explains what the tool does in human-readable terms
- **Input/Output Schemas** — JSON Schema definitions describing parameters and return values
- **Execution Context** — Direct function invocation without DOM manipulation or UI simulation

### Key concepts

- **Tool**: A JavaScript function with schema definitions that agents can invoke
- **ModelContext**: The browser API (`document.modelContext`) that manages tool registration and invocation
- **Schema**: JSON Schema definitions describing tool inputs, outputs, and constraints
- **Agent**: An autonomous assistant (LLM-powered) that can understand goals and invoke registered tools to accomplish tasks

### Browser support

WebMCP is currently available:

- Behind a feature flag in Chromium-based browsers (Chrome, Edge, Brave, etc.)
- Experimental status; API and browser behavior may change as the standard evolves
- Requires secure context (HTTPS or localhost) for development and cross-origin scenarios

For local development, enable **WebMCP for testing** via `chrome://flags/`.

> Note: WebMCP is an experimental browser standard. The API and behavior may change as the standard evolves. The Syncfusion Scheduler WebMCP integration is a preview feature.

## Why use WebMCP for Scheduler?

Syncfusion Scheduler integration with WebMCP provides significant benefits for building AI-powered calendar applications:

### Key benefits

- **Universal Tool Access** — AI agents, LLMs, and any MCP-compatible client can discover and invoke scheduling operations
- **Zero Custom Integration** — Set `enableWebMcp: true` and all tool registration, schema binding, and life cycle management is automatic. The Scheduler's WebMcp module loads itself when the property is enabled
- **Schema-Validated I/O** — Every tool includes JSON Schema for inputs and outputs, eliminating hallucination and enabling AI clients to validate data reliably
- **Controlled Execution** — Write operations can trigger user confirmation dialogs when `args.showConfirmationDialog` is set to `true` in the `beforeWebMcpToolExecute` event. Applications can use this event to audit, restrict, or cancel any operation
- **Multi-Instance Friendly** — Unique prefixes per Scheduler prevent tool-name collisions when multiple instances share a page
- **Native Integration** — Write tools participate in the Scheduler's built-in event pipelines (create, update, delete) and clean up automatically on component destruction
- **Real-Time Feedback** — Tools return immediate structured responses so agents can confirm or chain the next action

## How WebMCP works in Scheduler

Understanding how WebMCP processes your request helps you write better prompts and get more reliable results.

### The six-stage pipeline

When you invoke a WebMCP tool, the following happens behind the scenes:

1. **Tool Discovery** — Retrieve available tool schemas via `getWebMcpTools()`
2. **Tool Registration** — Register tools on `document.modelContext` with unique prefixes
3. **Tool Invocation** — AI Agent calls tool via `document.modelContext`
4. **Adapter Routing** — `WebMcpScheduler` strips prefix and fires `beforeWebMcpToolExecute` event
5. **Confirmation & Execution** — Write tools (`createEvent`, `updateEvent`, `deleteEvent`) can be confirmed through `beforeWebMcpToolExecute`, while read tools execute immediately
6. **Response Formatting** — Returns structured response with success or error

## Integration

WebMCP integrates seamlessly into Syncfusion Scheduler applications across all major platforms. Choose your framework below to get started with platform-specific setup instructions, API references, code examples, and sample prompts.

### Supported platforms

- [WebMCP Integration for React Scheduler](../react/schedule/webmcp) — React-based Scheduler
- [WebMCP Integration for Angular Scheduler](../angular/schedule/webmcp) — Angular-based Scheduler
- [WebMCP Integration for Vue Scheduler](../vue/schedule/webmcp) — Vue.js-based Scheduler
- [WebMCP Integration for TypeScript Scheduler](../typescript/schedule/webmcp) — TypeScript/ES6 Scheduler
- [WebMCP Integration for JavaScript Scheduler](../javascript/schedule/webmcp) — Vanilla JavaScript/ES5 Scheduler

## Common use cases

### AI-Assisted Schedule Browsing

Agents read the current view, list events on a given day, and summarize what is on the calendar via natural language prompts.

Example: "Navigate to April 8, 2026 and switch the Scheduler to Month view."

### Automated Event Management

Create, update, and delete calendar entries through conversational commands.

Example: "Create a new event called Design Review tomorrow from 10 AM to 11 AM."

### Calendar Navigation and View Switching

Move between dates and switch between Day, Week, Month, and Timeline views.

Example: "Delete the Design Review event."

## Supported Features

WebMCP tools for the Scheduler are organized into the following categories:

| Category | Tools | Example |
|----------|-------|---------|
| Event Operations | createEvent, updateEvent, deleteEvent | Add, modify, and remove calendar entries |
| View Operations | changeView | Switch between Day, Week, Month, and Timeline views |
| Navigation | navigateDate | Jump to a specific date on the calendar |

For the complete tool reference, see the [WebMCP Tools](../react/schedule/webmcp#scheduler-webmcp-tools) section in the platform integration guides.

## Writing Effective Prompts

AI responses are only as good as the prompt you provide. Vague requests like "fix this" give the AI very little context. More specific prompts produce reliable, accurate results.

Good Prompt Example:

"Create a new Scheduler event titled 'Quarterly Review' on April 8, 2026 from 2 PM to 3 PM, then switch the Scheduler to Day view."

Vague Prompt Example:

"add an event"

### Tips for Effective Prompts

- **Be Specific** — Include exact event titles, start and end times, and view names
- **Provide Context** — Mention the current view, target date, or business objective
- **One Goal at a Time** — Complex prompts may confuse the AI; break them into steps
- **Reference Items Clearly** — Use specific event titles or subjects instead of vague references

## Limitations

- **Public API operations only** — WebMCP tools can only perform the operations exposed through the Scheduler's public API methods.
- **Event identification by Subject** — `updateEvent` and `deleteEvent` identify events by `Subject` and optionally `StartTime`/`EndTime`. Custom event-field mappings are honored by the adapter.
- **Prompt clarity affects result quality** — The AI interprets your request literally, so output quality depends directly on how clearly the prompt is written.
- **Experimental status** — WebMCP is a preview standard. APIs and browser support may change as the specification evolves.

## See Also

- [Official WebMCP Specification](https://github.com/webmachinelearning/webmcp)
- [Chrome Developer Documentation](https://developer.chrome.com/docs/ai/webmcp/)