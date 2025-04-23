content = """
[![UiPath Studio](https://img.shields.io/badge/UiPath%20Studio-%E2%89%A52023.4-blue)](https://docs.uipath.com/)
[![License](https://img.shields.io/github/license/your-org/RPAChallenge_REFramework)](LICENSE)

# RPA Challenge — UiPath REFramework (Dispatcher & Performer)

End-to-end automation for the classic **[RPA Challenge – Fill Forms](https://www.rpachallenge.com/)**, built on UiPath’s **Robotic Enterprise Framework (REFramework)**.

The robot automatically downloads the Excel input file, normalises its headers, and completes every form submission. Because the challenge randomises field order and relies on dynamic selectors, the project uses attribute-based locators and data-driven mapping to remain stable across sessions.

The solution is split into two independent projects, following the UiPath best-practice “Dispatcher/Performer” pattern (REFramework):

| Role | Purpose |
|------|---------|
| **Dispatcher** | Downloads the challenge spreadsheet, validates the data and pushes each row to an Orchestrator **Queue**. |
| **Performer**  | Retrieves queue items and submits the web form, recording success or failure back to Orchestrator. |

```text
┌──────────┐     Queue: “Queue”       ┌────────────┐
│Dispatcher│ ───────────────────────► │ Performer  │
└──────────┘   (enqueue all rows)     └────────────┘
```

This queue-centric design decouples data collection from processing, so you can spin up multiple Performer robots in parallel and scale throughput on demand.

---

## Table of Contents
- [Features](#features)
- [Prerequisites](#prerequisites)
- [Quick Start](#quick-start)
- [Configuration](#configuration)
- [Orchestrator Set-up](#orchestrator-set-up)
- [Project Structure](#project-structure)
- [Contributing](#contributing)
- [License](#license)

---

## Features
- ⚙️ **REFramework best practices** – state machine, robust logging, retries and recovery.
- 🧹 **Dispatcher ↔ Performer separation** – queue-driven, idempotent and scalable.
- 🔒 **Assets** – data stored in Assets of orchestrator for use in both dispatcher and performer. No credential was needed in this project, but it's a interesting feature able to be used.
- 🖼️ **Automatic screenshots** on exceptions, saved to `Exceptions_Screenshots` in case of system exceptions.
- 📴 **Environment cleanup** – configurable `AppsToKill` list for tidy bot exits.
- 🧪 **Unit-test ready** – sample tests in each project’s `Tests/` folder.

---

## Prerequisites
| Tool | Minimum version | Notes |
|------|-----------------|-------|
| UiPath Studio / Robot | **2023.4** | Community or Enterprise |
| Chrome               | Latest      | Install UiPath browser extension |
| Orchestrator         | Modern Folders recommended | Create Queue & Assets below |

---

## Quick Start
1. **Clone** the repository and open the *Dispatcher* or *Performer* folder in UiPath Studio.  
2. **Adjust** the Excel config (`Data/Config_Dispatcher.xlsx` and `Data/Config_Performer.xlsx`) to match your environment.  
3. **Create** he asset in your project’s Orchestrator folder.
4. **Publish** each process to Orchestrator and start a job:  
   * Run **Dispatcher** once to populate the queue.  
   * Launch **Performer** (unattended/trigger) to work through the items.

---

## Configuration

| Sheet | Key | Example value | Description |
|-------|-----|---------------|-------------|
| **Settings** | `OrchestratorQueueName` | `Queue` | Queue where items are added / consumed |
|              | `AppsToKill`            | `chrome\|excel` | Pipe-delimited list of processes to kill on cleanup |
| **Constants**| `MaxRetryNumber`        | `0` | Leave **0** when using queues |
| **Assets**   | `Url_challenge`         | `URL_RPA_Challenge_FillForms` | Text asset with the challenge URL |

*Dispatcher* and *Performer* maintain separate config files to allow independent promotion.

---

## Orchestrator Set-up
1. **Queue**  
   * **Name:** `Queue` (or the value above)  
   * **Folder:** [NAME_OF_YOUR_FOLDER] (Modern Folder)  
2. **Assets**  

   | Name | Type | Value |
   |------|------|-------|
   | `URL_RPA_Challenge_FillForms` | Text | `https://www.rpachallenge.com/` |

3. **Robot permissions** – *Queue Items* (View, Edit, Create) and *Assets* (View).

---

## Project Structure

```
└── Dispatcher/
    ├── Data/Config_Dispatcher.xlsx
    ├── Documentation/
    ├── Exceptions_Screenshots/
    ├── Framework/          # InitAllSettings.xaml, Process.xaml, etc.
    ├── Process/            # Dispatcher-specific workflows
    └── Tests/
└── Performer/
    ├── Data/Config_Performer.xlsx
    ├── Documentation/
    ├── Exceptions_Screenshots/
    ├── Framework/          # Includes its own Process.xaml
    └── Tests/
```
> **Note:** The *Performer* project intentionally has no top-level `Process/` folder; its business logic resides in `Framework/Process.xaml`.

---

## Contributing
1. **Fork** the repo  
2. Create a branch `git checkout -b feat/my-change`  
3. Commit with meaningful messages  
4. Open a **Pull Request**.  
Run **Workflow Analyzer** with *High* strictness before submitting.

---

## License
Distributed under the **MIT License** – see [`LICENSE`](LICENSE) for details.

---
