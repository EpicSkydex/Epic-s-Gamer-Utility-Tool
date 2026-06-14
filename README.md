# Gamer Utility Tool

A lightweight C# .NET 8 WPF utility for Windows gaming. It provides an ETW-based FPS monitor, a 0.5ms system timer adjustment to help with input lag, and a basic background memory cleaner. Built as a standalone executable that relies on native Windows APIs instead of DLL injections or heavy background processes.

## Core Features

* **Passive FPS Monitoring:** Utilizes Event Tracing for Windows (ETW) to read DXGI "Present" events. This method is entirely passive and does not hook into game processes or inject DLLs.
* **Timer Resolution Adjustment:** Calls the undocumented `NtSetTimerResolution` API to force a 0.5ms system timer while a game is active, reverting to the OS default when closed.
* **Background Memory Management:** Interacts with native Windows APIs to periodically flush the working set and standby list.
* **Hardware Sensing:** Integrates `LibreHardwareMonitor` with a recursive SubHardware scanning approach to accurately retrieve CPU (including AMD Ryzen Tdie/CCD via Super I/O) and GPU temperatures.
* **Customizable Overlay:** Includes a WPF-based On-Screen Display (OSD) and a main dashboard with adjustable opacity and accent colors. State is persisted via a local JSON configuration.

## Known Issues

* **On-Screen Display (OSD):** The OSD overlay is currently a work in progress. It may not render correctly over certain exclusive fullscreen games or might require windowed-borderless mode to function reliably.

## Technical Stack

* **Framework:** .NET 8.0 (WPF)
* **Architecture:** x64 targeted WinExe
* **Key Libraries:** `LibreHardwareMonitorLib`, `Microsoft.Diagnostics.Tracing.TraceEvent`

## Build Instructions

The project is structured to be compiled into a single, highly optimized executable without loose DLL files. Run the following command in the project directory:

```bash
dotnet publish -c Release -r win-x64 -p:PublishSingleFile=true --self-contained false
