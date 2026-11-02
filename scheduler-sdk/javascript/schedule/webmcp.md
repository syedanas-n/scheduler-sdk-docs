---
layout: post
title: WebMCP tool integration for JavaScript (ES5) Scheduler | Syncfusion
description: Learn how to integrate WebMCP with the Syncfusion JavaScript (ES5) Scheduler control to enable AI interaction, configure tools, and monitor execution.
control: Scheduler
platform: scheduler-sdk
documentation: ug
domainurl: https://help.syncfusion.com/scheduler-sdk
appliesto: UI Component Suite, Scheduler SDK
---

# WebMCP Tools Integration for Syncfusion® JavaScript (ES5) Scheduler

WebMCP enables AI agents to discover and interact with Syncfusion® JavaScript (ES5) controls through a standardized tool interface. By enabling WebMCP, AI-powered applications can securely discover control capabilities, read data, perform actions, and automate workflows through a standardized interface.

This section demonstrates how to integrate WebMCP with the Syncfusion® JavaScript (ES5) Scheduler control. The same approach can be used with other supported controls such as Grid, File Manager, Chart, and more.

## WebMCP Overview

WebMCP allows AI assistants running in the browser to interact directly with Syncfusion® controls through natural language prompts.

Instead of relying on DOM inspection or simulating user interactions, AI assistants can interact directly with control APIs through WebMCP tools. This approach provides more reliable and accurate results.

Once connected, an AI assistant can perform tasks such as:

- Create, update, and delete calendar events.
- Switch between Day, Week, Month, and Timeline views.
- Navigate the calendar to a specific date.
- Perform control-specific actions through natural language prompts.

> WebMCP is currently an experimental browser capability and requires a Chromium-based browser with WebMCP support enabled. Availability and required browser flags may change as the specification evolves.

## Prerequisites

- Syncfusion® JavaScript (ES5) controls
- Chromium-based Browser (like Chrome, Edge, or Brave)
- [Node.js 24+](https://nodejs.org/en) (LTS recommended).
- WebMCP browser extension installed
- AI API key configured in the extension

## WebMCP setup

Before you can use WebMCP locally, enable the feature flag and install the browser extension:

1. Enable `WebMCP for testing` flag via `chrome://flags/`. Click **"Relaunch"** to restart Chrome.

2. Search for **"WebMCP - Model Context Tool Inspector"** in [Chrome Web Store](https://chromewebstore.google.com/detail/webmcp-model-context-tool/gbpdfapgefenggkahomfgkhfehlcenpd). Click **"Add to Chrome"**.

3. Visit [Google AI Studio](https://aistudio.google.com/app/apikey) to create or copy an API key. Open the WebMCP extension → paste your API key.

The WebMCP extension is now configured and ready to interact with WebMCP-enabled Syncfusion® controls.

## Integrating WebMCP tools

Follow these steps to integrate WebMCP support into the Syncfusion® JavaScript (ES5) Scheduler control.

### Step 1: Create an application

Create an application and integrate the Syncfusion® JavaScript (ES5) Scheduler control by following the [Getting Started with JavaScript (ES5) Scheduler](https://help.syncfusion.com/scheduler-sdk/javascript/schedule/getting-started) guide. After completing the setup, follow the steps below to enable WebMCP integration.

### Step 2: Enable WebMCP support

The Scheduler's WebMCP integration is loaded automatically when `enableWebMcp` is `true`, so no extra module injection is required. Inject the Scheduler view modules as usual.

```html
<!DOCTYPE html>
<html lang="en">
    <head>
        <title>Essential JS 2</title>
        <meta charset="utf-8" />
        <meta name="viewport" content="width=device-width, initial-scale=1.0, user-scalable=no" />
        <link href="https://cdn.syncfusion.com/ej2/34.2.2/bootstrap5.3.css" rel="stylesheet" type="text/css" />
        <!-- Essential JS 2 all script -->
        <script src="https://cdn.syncfusion.com/ej2/34.2.2/dist/ej2.min.js" type="text/javascript"></script>
    </head>
    <body>
        <div>
            <div id="scheduler"></div>
        </div>
        <script>
            ej.schedule.Schedule.Inject(ej.schedule.Day, ej.schedule.Week, ej.schedule.WorkWeek, ej.schedule.Month, ej.schedule.Agenda);
            var scheduleInstance = new ej.schedule.Schedule({});
            scheduleInstance.appendTo('#scheduler');
        </script>
    </body>
</html>
```

### Step 3: Register control tools

Set the `enableWebMcp` property to `true` to register the control's available tools with the WebMCP runtime.

When `enableWebMcp` is enabled, supported control tools are automatically registered with the WebMCP runtime and become discoverable by authorized WebMCP clients.

For example, a Scheduler control may expose tools such as:

- createEvent
- updateEvent
- deleteEvent
- changeView
- navigateDate

An AI assistant can discover these tools and invoke them based on user prompts.

```html
<!DOCTYPE html>
<html lang="en">
    <head>
        <title>Essential JS 2</title>
        <meta charset="utf-8" />
        <meta name="viewport" content="width=device-width, initial-scale=1.0, user-scalable=no" />
        <link href="https://cdn.syncfusion.com/ej2/34.2.2/bootstrap5.3.css" rel="stylesheet" type="text/css" />
        <!-- Essential JS 2 all script -->
        <script src="https://cdn.syncfusion.com/ej2/34.2.2/dist/ej2.min.js" type="text/javascript"></script>
    </head>
    <body>
        <div>
            <div id="scheduler"></div>
        </div>
        <script>
            ej.schedule.Schedule.Inject(ej.schedule.Day, ej.schedule.Week, ej.schedule.WorkWeek, ej.schedule.Month, ej.schedule.Agenda);
            var scheduleInstance = new ej.schedule.Schedule({
                enableWebMcp: true
            });
            scheduleInstance.appendTo('#scheduler');
        </script>
    </body>
</html>
```

### Step 4: Customize tool registration

Use the `webMcpSettings` property to customize how WebMCP tools are exposed to AI agents. The available settings are listed below.

| Property | Description |
|-----------|-------------|
| `name` | Prefix applied to registered tool names. If name: `planner` is specified, a tool such as `createEvent` may be registered as `planner_createEvent`, allowing multiple control instances to coexist without tool name conflicts. |
| `tools` | List of tool names to expose. Any supported tools not included in this list will not be registered. |
| `exposedTo` | List of trusted domains that are allowed to interact with the control through WebMCP. |

```html
<!DOCTYPE html>
<html lang="en">
    <head>
        <title>Essential JS 2</title>
        <meta charset="utf-8" />
        <meta name="viewport" content="width=device-width, initial-scale=1.0, user-scalable=no" />
        <link href="https://cdn.syncfusion.com/ej2/34.2.2/bootstrap5.3.css" rel="stylesheet" type="text/css" />
        <!-- Essential JS 2 all script -->
        <script src="https://cdn.syncfusion.com/ej2/34.2.2/dist/ej2.min.js" type="text/javascript"></script>
    </head>
    <body>
        <div>
            <div id="scheduler"></div>
        </div>
        <script>
            ej.schedule.Schedule.Inject(ej.schedule.Day, ej.schedule.Week, ej.schedule.WorkWeek, ej.schedule.Month, ej.schedule.Agenda);
            var scheduleInstance = new ej.schedule.Schedule({
                enableWebMcp: true,
                webMcpSettings: {
                    name: 'planner',
                    tools: ['createEvent', 'updateEvent', 'deleteEvent', 'changeView', 'navigateDate'],
                    exposedTo: ['https://www.syncfusion.com/']
                }
            });
            scheduleInstance.appendTo('#scheduler');
        </script>
    </body>
</html>
```

> Use the `exposedTo` property to restrict WebMCP access to trusted domains. This helps prevent unauthorized WebMCP clients from interacting with your control tools.

### Step 5: Monitor and control tool execution

The `beforeWebMcpToolExecute` event is triggered before a tool is executed. This event allows you to:

- Audit tool usage.
- Validate incoming requests.
- Cancel tool execution when necessary.
- Apply custom business rules and security policies.
- Set `args.showConfirmationDialog = true` for mutating tools (`createEvent`, `updateEvent`, `deleteEvent`) to prompt the user before the Scheduler writes to its event pipeline.

> AI-generated requests should always be validated before executing write operations. Restrict access using `exposedTo` and perform additional authorization checks within `beforeWebMcpToolExecute` when modifying application data.

```html
<!DOCTYPE html>
<html lang="en">
    <head>
        <title>Essential JS 2</title>
        <meta charset="utf-8" />
        <meta name="viewport" content="width=device-width, initial-scale=1.0, user-scalable=no" />
        <link href="https://cdn.syncfusion.com/ej2/34.2.2/bootstrap5.3.css" rel="stylesheet" type="text/css" />
        <!-- Essential JS 2 all script -->
        <script src="https://cdn.syncfusion.com/ej2/34.2.2/dist/ej2.min.js" type="text/javascript"></script>
    </head>
    <body>
        <div>
            <div id="scheduler"></div>
        </div>
        <script>
            ej.schedule.Schedule.Inject(ej.schedule.Day, ej.schedule.Week, ej.schedule.WorkWeek, ej.schedule.Month, ej.schedule.Agenda);
            var scheduleInstance = new ej.schedule.Schedule({
                enableWebMcp: true,
                webMcpSettings: {
                    name: 'planner',
                    tools: ['createEvent', 'updateEvent', 'deleteEvent', 'changeView', 'navigateDate'],
                    exposedTo: ['https://www.syncfusion.com/']
                },
                beforeWebMcpToolExecute: function (args) {
                    // Example business rule: block writes outside business hours
                    if (args.toolName.indexOf('createEvent') !== -1 || args.toolName.indexOf('updateEvent') !== -1 || args.toolName.indexOf('deleteEvent') !== -1) {
                        var hour = new Date().getHours();
                        if (hour < 9 || hour > 17) {
                            args.cancel = true;
                            console.log(
                                'Schedule changes are allowed only between 9:00 AM and 5:00 PM.'
                            );
                        }
                    }
                }
            });
            scheduleInstance.appendTo('#scheduler');
        </script>
    </body>
</html>
```

## Scheduler WebMCP tools

The Syncfusion® Scheduler control exposes 5 tools that enable AI agents to manage calendar events, switch between views, and navigate dates through natural language prompts.

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
- Pass dates in ISO 8601 format (`2026-04-08T09:00:00`) so AI agents can address them unambiguously.
- Confirm mutating operations by setting `args.showConfirmationDialog = true` in `beforeWebMcpToolExecute` for `createEvent`, `updateEvent`, and `deleteEvent`.

## Next steps

After successfully enabling WebMCP, you can:

- Integrate WebMCP with other Syncfusion® JavaScript (ES5) controls.
- Build AI-powered workflows that interact directly with application data and UI elements.
- Create secure and controlled tool execution pipelines using custom validation logic.

## See also

- [WebMCP Tools for Scheduler SDK](../../ai-tools/webmcp-tools)
- [WebMCP Supported Syncfusion JavaScript Controls](https://ej2.syncfusion.com/javascript/documentation/webmcp/controls)