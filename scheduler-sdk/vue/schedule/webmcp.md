---
layout: post
title: WebMCP tool integration for Vue Scheduler | Syncfusion
description: Learn how to integrate WebMCP with the Syncfusion Vue Scheduler component to enable AI interaction, configure tools, and monitor execution.
control: Scheduler
platform: scheduler-sdk
documentation: ug
domainurl: https://help.syncfusion.com/scheduler-sdk
appliesto: UI Component Suite, Scheduler SDK
---

# WebMCP Tools Integration for Syncfusion® Vue Scheduler Component

WebMCP enables AI agents to discover and interact with Syncfusion® Vue components through a standardized tool interface. By enabling WebMCP, AI-powered applications can securely discover component capabilities, read data, perform actions, and automate workflows through a standardized interface.

This section demonstrates how to integrate WebMCP with the Syncfusion® Vue Scheduler component. The same approach can be used with other supported components such as Grid, File Manager, Chart, and more.

## WebMCP Overview

WebMCP allows AI assistants running in the browser to interact directly with Syncfusion® components through natural language prompts.

Instead of relying on DOM inspection or simulating user interactions, AI assistants can interact directly with component APIs through WebMCP tools. This approach provides more reliable and accurate results.

Once connected, an AI assistant can perform tasks such as:

- Create, update, and delete calendar events.
- Switch between Day, Week, Month, and Timeline views.
- Navigate the calendar to a specific date.
- Perform component-specific actions through natural language prompts.

> WebMCP is currently an experimental browser capability and requires a Chromium-based browser with WebMCP support enabled. Availability and required browser flags may change as the specification evolves.

## Prerequisites

- Syncfusion® Vue components
- Chromium-based Browser (like Chrome, Edge, or Brave)
- [Node.js 24+](https://nodejs.org/en) (LTS recommended).
- WebMCP browser extension installed
- AI API key configured in the extension

## WebMCP setup

Before you can use WebMCP locally, enable the feature flag and install the browser extension:

1. Enable `WebMCP for testing` flag via `chrome://flags/`. Click **"Relaunch"** to restart Chrome.

2. Search for **"WebMCP - Model Context Tool Inspector"** in [Chrome Web Store](https://chromewebstore.google.com/detail/webmcp-model-context-tool/gbpdfapgefenggkahomfgkhfehlcenpd). Click **"Add to Chrome"**.

3. Visit [Google AI Studio](https://aistudio.google.com/app/apikey) to create or copy an API key. Open the WebMCP extension → paste your API key.

The WebMCP extension is now configured and ready to interact with WebMCP-enabled Syncfusion® components.

## Integrating WebMCP tools

Follow these steps to integrate WebMCP support into the Syncfusion® Vue Scheduler component.

### Step 1: Set up the Vite project

Create a Vue Vite application and integrate the Syncfusion® Vue Scheduler component by following the [Getting Started with Vue Scheduler](https://help.syncfusion.com/scheduler-sdk/vue/schedule/getting-started) guide. After completing the setup, follow the steps below to enable WebMCP integration.

### Step 2: Enable WebMCP support

Import the `WebMcpScheduler` module and inject it into the Scheduler to make WebMCP functionality available.

```html
<template>
  <ejs-schedule id="scheduler">
  </ejs-schedule>
</template>

<script setup>
import { provide } from 'vue';
import { ScheduleComponent as EjsSchedule, Day, Week, WorkWeek, Month, Agenda, WebMcpScheduler } from '@syncfusion/ej2-vue-schedule';

provide('schedule', [Day, Week, WorkWeek, Month, Agenda, WebMcpScheduler]);
</script>
```

### Step 3: Register component tools

Set the `enableWebMcp` property to `true` to register the component's available tools with the WebMCP runtime.

When `enableWebMcp` is enabled, supported component tools are automatically registered with the WebMCP runtime and become discoverable by authorized WebMCP clients.

For example, a Scheduler component may expose tools such as:

- createEvent
- updateEvent
- deleteEvent
- changeView
- navigateDate

An AI assistant can discover these tools and invoke them based on user prompts.

```html
<template>
  <ejs-schedule id="scheduler" :enableWebMcp="true">
  </ejs-schedule>
</template>

<script setup>
import { provide } from 'vue';
import { ScheduleComponent as EjsSchedule, Day, Week, WorkWeek, Month, Agenda, WebMcpScheduler } from '@syncfusion/ej2-vue-schedule';

provide('schedule', [Day, Week, WorkWeek, Month, Agenda, WebMcpScheduler]);
</script>
```

### Step 4: Customize tool registration

Use the `webMcpSettings` property to customize how WebMCP tools are exposed to AI agents. The available settings are listed below.

| Property | Description |
|-----------|-------------|
| `name` | Prefix applied to registered tool names. If name: `planner` is specified, a tool such as `createEvent` may be registered as `planner_createEvent`, allowing multiple component instances to coexist without tool name conflicts. |
| `tools` | List of tool names to expose. Any supported tools not included in this list will not be registered. |
| `exposedTo` | List of trusted domains that are allowed to interact with the component through WebMCP. |

```html
<template>
  <ejs-schedule id="scheduler" :enableWebMcp="true"
    :webMcpSettings="webMcpSettings">
  </ejs-schedule>
</template>

<script setup>
import { provide } from 'vue';
import { ScheduleComponent as EjsSchedule, Day, Week, WorkWeek, Month, Agenda, WebMcpScheduler } from '@syncfusion/ej2-vue-schedule';

provide('schedule', [Day, Week, WorkWeek, Month, Agenda, WebMcpScheduler]);

const webMcpSettings = {
  name: 'planner',
  tools: ['createEvent', 'updateEvent', 'deleteEvent'],
  exposedTo: ['https://www.syncfusion.com/']
};
</script>
```

> Use the `exposedTo` property to restrict WebMCP access to trusted domains. This helps prevent unauthorized WebMCP clients from interacting with your component tools.

### Step 5: Monitor and control tool execution

The `beforeWebMcpToolExecute` event is triggered before a tool is executed. This event allows you to:

- Audit tool usage.
- Validate incoming requests.
- Cancel tool execution when necessary.
- Apply custom business rules and security policies.

> AI-generated requests should always be validated before executing write operations. Restrict access using `exposedTo` and perform additional authorization checks within `beforeWebMcpToolExecute` when modifying application data.

The `beforeWebMcpToolExecute` event exposes multiple properties that let applications control how each tool is invoked:

- `args.showConfirmationDialog = true` — Pops a confirmation dialog before the tool runs. Useful for risky write operations such as `deleteEvent` so the user can choose **Yes** or **No** before any changes are applied.
- `args.cancel = true` — Cancels the tool invocation entirely. Use this to block tools you do not want to expose (for example `updateEvent`) based on application-specific rules.
- `args.cancellationResponse` — Sets a custom message that the AI agent receives when the tool is cancelled. Use this to explain to the agent why the call was rejected so it can recover gracefully.

```html
<template>
  <ejs-schedule id="scheduler" :enableWebMcp="true"
    :webMcpSettings="webMcpSettings" :beforeWebMcpToolExecute="beforeWebMcpToolExecute">
  </ejs-schedule>
</template>

<script setup>
import { provide } from 'vue';
import { ScheduleComponent as EjsSchedule, Day, Week, WorkWeek, Month, Agenda, WebMcpScheduler } from '@syncfusion/ej2-vue-schedule';

provide('schedule', [Day, Week, WorkWeek, Month, Agenda, WebMcpScheduler]);

const webMcpSettings = {
  name: 'planner',
  tools: ['createEvent', 'updateEvent', 'deleteEvent'],
  exposedTo: ['https://www.syncfusion.com/']
};

const beforeWebMcpToolExecute = (args) => {
  // Ask the user to confirm before deleting an event
  if (args.toolName.includes('deleteEvent')) {
    args.showConfirmationDialog = true;
  }

  // Reject updates outright and tell the agent why
  if (args.toolName.includes('updateEvent')) {
    args.cancel = true;
    args.cancellationResponse =
      'Modification of events is not allowed through WebMCP tools.';
  }
};
</script>
```

> **Note:** `showConfirmationDialog` is supported only for write tools (`createEvent`, `updateEvent`, and `deleteEvent`) and is not applicable to navigation tools such as `changeView` and `navigateDate`.

## Scheduler WebMCP tools

The Syncfusion® Scheduler component exposes 5 tools that enable AI agents to manage calendar events, switch between views, and navigate dates through natural language prompts. Explore the Syncfusion® Vue Scheduler component with WebMCP integration through this [demo](https://demos.syncfusion.com/scheduler-sdk/webmcp-integration/index.html).

| Tool Name | Description |
|-----------|-------------|
| createEvent | Creates a new Scheduler event with subject, start time, end time, location, description, and all-day flag |
| updateEvent | Updates an existing Scheduler event identified by subject (and optionally start/end time) with new values |
| deleteEvent | Deletes an existing Scheduler event identified by subject (and optionally start/end time) |
| changeView | Changes the current Scheduler view to Day, Week, WorkWeek, Month, Agenda, MonthAgenda, TimelineDay, TimelineWeek, TimelineWorkWeek, TimelineMonth, Year, or TimelineYear |
| navigateDate | Navigates the Scheduler to a specific date using an ISO 8601 string |

## Best practices

- Expose only the tools required by your application.
- Use the `name` property when multiple Scheduler instances exist on the same page.
- Configure `exposedTo` to limit access to trusted domains.
- Monitor tool activity through the `beforeWebMcpToolExecute` event.
- Apply application-specific validation before executing tool operations.

## Explore the demo

Explore the Syncfusion® Vue Scheduler component with WebMCP integration through this [demo](https://demos.syncfusion.com/scheduler-sdk/webmcp-integration/index.html).

## Next steps

After successfully enabling WebMCP, you can:

- Integrate WebMCP with other Syncfusion® Vue components.
- Build AI-powered workflows that interact directly with application data and UI elements.
- Create secure and controlled tool execution pipelines using custom validation logic.

## See also

- [WebMCP Tools for Scheduler SDK](../../ai-tools/webmcp-tools)
- [WebMCP Supported Syncfusion Vue Components](https://ej2.syncfusion.com/vue/documentation/webmcp/components)