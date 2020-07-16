---
title: Debug ASP.NET Core Blazor WebAssembly
author: guardrex
description: Learn how to debug Blazor apps.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/15/2020
no-loc: [Blazor, "Blazor Server", "Blazor WebAssembly", "Identity", "Let's Encrypt", Razor, SignalR]
uid: blazor/debug
---
# Debug ASP.NET Core Blazor WebAssembly

[Daniel Roth](https://github.com/danroth27)

Blazor WebAssembly apps can be debugged using the browser dev tools in Chromium-based browsers (Edge/Chrome). Alternatively, you can debug your app using Visual Studio or Visual Studio Code.

Available scenarios include:

* Set and remove breakpoints.
* Run the app with debugging support in Visual Studio and Visual Studio Code (<kbd>F5</kbd> support).
* Single-step (<kbd>F10</kbd>) through the code.
* Resume code execution with <kbd>F8</kbd> in a browser or <kbd>F5</kbd> in Visual Studio or Visual Studio Code.
* In the *Locals* display, observe the values of local variables.
* See the call stack, including call chains that go from JavaScript into .NET and from .NET to JavaScript.

For now, you *can't*:

* Break on unhandled exceptions.
* Hit breakpoints during app startup.

We will continue to improve the debugging experience in upcoming releases.

## Prerequisites

Debugging requires either of the following browsers:

* Google Chrome (version 70 or later) (default)
* Microsoft Edge (version 80 or later)

## Enable debugging for Visual Studio and Visual Studio Code

To enable debugging for an existing Blazor WebAssembly app, update the `launchSettings.json` file in the startup project to include the following `inspectUri` property in each launch profile:

```json
"inspectUri": "{wsProtocol}://{url.hostname}:{url.port}/_framework/debug/ws-proxy?browser={browserInspectUri}"
```

Once updated, the `launchSettings.json` file should look similar to the following example:

[!code-json[](debug/launchSettings.json?highlight=14,22)]

The `inspectUri` property:

* Enables the IDE to detect that the app is a Blazor WebAssembly app.
* Instructs the script debugging infrastructure to connect to the browser through Blazor's debugging proxy.

The placeholder values for the WebSockets protocol (`wsProtocol`), host (`url.hostname`), port (`url.port`), and inspector URI on the launched browser (`browserInspectUri`) are provided by the framework.

## Visual Studio

To debug a Blazor WebAssembly app in Visual Studio:

1. Create a new ASP.NET Core hosted Blazor WebAssembly app.
1. Press <kbd>F5</kbd> to run the app in the debugger.
1. Set a breakpoint in `Pages/Counter.razor` in the `IncrementCount` method.
1. Browse to the **`Counter`** tab and select the button to hit the breakpoint:

   ![Debug Counter](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-counter.png)

1. Check out the value of the `currentCount` field in the locals window:

   ![View locals](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-locals.png)

1. Press <kbd>F5</kbd> to continue execution.

While debugging your Blazor WebAssembly app, you can also debug your server code:

1. Set a breakpoint in the `Pages/FetchData.razor` page in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>.
1. Set a breakpoint in the `WeatherForecastController` in the `Get` action method.
1. Browse to the **`Fetch Data`** tab to hit the first breakpoint in the `FetchData` component just before it issues an HTTP request to the server:

   ![Debug Fetch Data](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-fetch-data.png)

1. Press <kbd>F5</kbd> to continue execution and then hit the breakpoint on the server in the `WeatherForecastController`:

   ![Debug server](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-server.png)

1. Press <kbd>F5</kbd> again to let execution continue and see the weather forecast table rendered.

<a id="vscode"></a>

## Visual Studio Code

For information on installing Visual Studio Code for Blazor app development, see <xref:blazor/tooling>.

### Debug standalone Blazor WebAssembly

1. Open the standalone Blazor WebAssembly app in VS Code.

   If you receive the following notification that additional setup is required to enable debugging:
   
   * Confirm that you have the correct extensions installed.
   * Confirm that JavaScript preview debugging is enabled.
   * Reload the window.

   ![Additional setup required](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-additional-setup.png)

1. Start debugging using the <kbd>F5</kbd> keyboard shortcut or the menu item.

1. When prompted, select the **Blazor WebAssembly Debug** option to start debugging.

   ![List of available debug options](index/_static/blazor-vscode-debugtypes.png)

1. The standalone app is launched, and a debugging browser is opened.

1. Set a breakpoint in the `IncrementCount` method in the `Counter` component and then select the button to hit the breakpoint:

   ![Debug Counter in VS Code](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-debug-counter.png)

### Debug hosted Blazor WebAssembly

1. Open the hosted Blazor WebAssembly app's solution folder in VS Code.

1. If there's no launch configuration set for the project, the following notification appears. Select **Yes**.

   ![Add required assets](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-required-assets.png)

1. In the command palette at the top of the window, select the *Server* project within the hosted solution.

A `launch.json` file is generated with the launch configuration for launching the debugger.

### Attach to an existing debugging session

To attach to a running Blazor app, create a `launch.json` file with the following configuration:

```json
{
  "type": "blazorwasm",
  "request": "attach",
  "name": "Attach to Existing Blazor WebAssembly Application"
}
```

> [!NOTE]
> Attaching to a debugging session is only supported for standalone apps. To use full-stack debugging, you must launch the app from VS Code.

### Launch configuration options

The following launch configuration options are supported for the `blazorwasm` debug type (`.vscode/launch.json`).

| Option    | Description |
| --------- | ----------- |
| `request` | Use `launch` to launch and attach a debugging session to a Blazor WebAssembly app or `attach` to attach a debugging session to an already-running app. |
| `url`     | The URL to open in the browser when debugging. Defaults to `https://localhost:5001`. |
| `browser` | The browser to launch for the debugging session. Set to `edge` or `chrome`. Defaults to `chrome`. |
| `trace`   | Used to generate logs from the JS debugger. Set to `true` to generate logs. |
| `hosted`  | Must be set to `true` if launching and debugging a hosted Blazor WebAssembly app. |
| `webRoot` | Specifies the absolute path of the web server. Should be set if an app is served from a sub-route. |
| `timeout` | The number of milliseconds to wait for the debugging session to attach. Defaults to 30,000 milliseconds (30 seconds). |
| `program` | A reference to the executable to run the server of the hosted app. Must be set if `hosted` is `true`. |
| `cwd`     | The working directory to launch the app under. Must be set if `hosted` is `true`. |
| `env`     | The environment variables to provide to the launched process. Only applicable if `hosted` is set to `true`. |

### Example launch configurations

#### Launch and debug a standalone Blazor WebAssembly app

```json
{
  "type": "blazorwasm",
  "request": "launch",
  "name": "Launch and Debug"
}
```

#### Attach to a running app at a specified URL

```json
{
  "type": "blazorwasm",
  "request": "attach",
  "name": "Attach and Debug",
  "url": "http://localhost:5000"
}
```

#### Launch and debug a hosted Blazor WebAssembly app with Microsoft Edge

Browser configuration defaults to Google Chrome. When using Microsoft Edge for debugging, set `browser` to `edge`. To use Google Chrome, either don't set the `browser` option or set the option's value to `chrome`.

```json
{
  "name": "Launch and Debug Hosted Blazor WebAssembly App",
  "type": "blazorwasm",
  "request": "launch",
  "hosted": true,
  "program": "${workspaceFolder}/Server/bin/Debug/netcoreapp3.1/MyHostedApp.Server.dll",
  "cwd": "${workspaceFolder}/Server",
  "browser": "edge"
}
```

In the preceding example, `MyHostedApp.Server.dll` is the *Server* app's assembly. The `.vscode` folder is located in the solution's folder next to the `Client`, `Server`, and `Shared` folders.

## Debug in the browser

1. Run a Debug build of the app in the Development environment.

1. Launch a browser and navigate to the app's URL (for example, `https://localhost:5001`).

1. In the browser, attempt to commence remote debugging by pressing <kbd>Shift</kbd>+<kbd>Alt</kbd>+<kbd>D</kbd>.

   The browser must be running with remote debugging enabled, which isn't the default. If remote debugging is disabled, an **Unable to find debuggable browser tab** error page is rendered with instructions for launching the browser with the debugging port open. Follow the instructions for your browser, which opens a new browser window. Close the previous browser window.

1. Once the browser is running with remote debugging enabled, the debugging keyboard shortcut (<kbd>Shift</kbd>+<kbd>Alt</kbd>+<kbd>D</kbd>) opens a new debugger tab.

1. After a moment, the **Sources** tab shows a list of the app's .NET assemblies within the `file://` node.

1. In component code (`.razor` files) and C# code files (`.cs`), breakpoints that you set are hit when code executes. After a breakpoint is hit, single-step (<kbd>F10</kbd>) through the code or resume (<kbd>F8</kbd>) code execution normally.

Blazor provides a debugging proxy that implements the [Chrome DevTools Protocol](https://chromedevtools.github.io/devtools-protocol/) and augments the protocol with .NET-specific information. When debugging keyboard shortcut is pressed, Blazor points the Chrome DevTools at the proxy. The proxy connects to the browser window you're seeking to debug (hence the need to enable remote debugging).

## Browser source maps

Browser source maps allow the browser to map compiled files back to their original source files and are commonly used for client-side debugging. However, Blazor doesn't currently map C# directly to JavaScript/WASM. Instead, Blazor does IL interpretation within the browser, so source maps aren't relevant.

## Troubleshoot

If you're running into errors, the following tips may help:

* In the **Debugger** tab, open the developer tools in your browser. In the console, execute `localStorage.clear()` to remove any breakpoints.
* Confirm that you've installed and trusted the ASP.NET Core HTTPS development certificate. For more information, see <xref:security/enforcing-ssl#troubleshoot-certificate-problems>.
* Visual Studio requires the **Enable JavaScript debugging for ASP.NET (Chrome, Edge and IE)** option in **Tools** > **Options** > **Debugging** > **General**. This is the default setting for Visual Studio. If debugging isn't working, confirm that the option is selected.
