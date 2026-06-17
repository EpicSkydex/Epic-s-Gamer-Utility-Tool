# Gamer Utility Tool

A lightweight C# .NET 8 WPF utility for Windows gaming. It provides an ETW-based FPS monitor, a 0.5ms system timer adjustment to help with input lag, smart background process pausing, and a RAM cleaner. Built as a standalone executable that relies on native Windows APIs instead of sketchy DLL injections or heavy background processes.

## ⚠️ Why does Windows Defender flag this as Malware?

If you download the `.exe` or compile it yourself, Windows Defender (or other Antivirus software) will likely freak out and flag it as a Trojan or generic malware. **This is a false positive.** Here is exactly why it happens:

1. **Undocumented APIs:** To squeeze out performance, this tool hooks into low-level Windows Kernel APIs (like `NtSetTimerResolution` for the 0.5ms timer and `NtSuspendProcess` to freeze background apps). Antivirus software generally flags apps that do this.
2. **ETW Tracing & Hardware Sensors:** Reading raw DXGI frames via ETW and querying CPU/GPU temperatures directly requires admin privileges and looks suspiciously like spyware to automated AV scanners.
3. **Single-File Publish:** The tool is packed into a single `.exe` file without loose DLLs. Malware often uses this exact packing method.
4. **No Code Signing Certificate:** I'm just a dude writing a tool for me and my friends, not a corporation. I don't have a $500 EV code signing certificate to tell Microsoft this app is "official".

**The code is 100% open-source.** Don't trust the `.exe`? I don't blame you. Read the source code here on GitHub and compile it yourself using the build instructions below.

---

## Core Features

* **Passive FPS Monitoring:** Uses Event Tracing for Windows (ETW) with a custom debounce filter to read DXGI "Present" events. This method is entirely passive—no game hooks, no DLL injections, no anti-cheat bans.
* **Timer Resolution & Power Plan:** Calls the `NtSetTimerResolution` API to force a 0.5ms system timer while a game is active to reduce input lag. It also automatically switches to a high-performance power plan.
* **Smart Process Pauser:** Select specific background apps (like Discord, Chrome, or Launchers) in the dashboard. The tool will freeze them (0% CPU usage) when a game starts and instantly thaw them when you close the game.
* **System Tweaks:** Temporarily forces TCP No-Delay (Nagle's algorithm killer for better ping), pauses Windows Updates, and disables Sticky Keys during gameplay. Reverts cleanly on exit.
* **Background Memory Management:** Interacts with native Windows APIs to flush the working set and standby list, complete with a clean, unobtrusive floating UI overlay showing exactly what was freed.
* **Hardware Sensing:** Integrates `LibreHardwareMonitor` with a recursive scanning approach to accurately retrieve CPU/GPU temperatures and active load percentages.
* **Customizable OSD & Theming:** Includes a highly customizable WPF On-Screen Display (OSD) and a main dashboard with adjustable opacity, layouts, and deep color theming.

## Known Issues

* **On-Screen Display (OSD):** Depending on the game engine, the OSD overlay might require you to run the game in "Windowed Borderless" mode to render correctly over the game window. Exclusive Fullscreen can sometimes hide it.

## Technical Stack

* **Framework:** .NET 8.0 (WPF)
* **Architecture:** x64 targeted WinExe
* **Key Libraries:** `LibreHardwareMonitorLib`, `Microsoft.Diagnostics.Tracing.TraceEvent`, `Microsoft.Xaml.Behaviors.Wpf`

## Build Instructions

The project is structured to be compiled into a single, highly optimized executable without loose DLL files. Run the following commands in the project directory:

```bash
dotnet clean
dotnet publish GamerUtilityTool.csproj -c Release -r win-x64 -p:PublishSingleFile=true --self-contained false
