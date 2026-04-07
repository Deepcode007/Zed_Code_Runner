# Zed CP Helper

A fully automated, native background tool for managing competitive programming workflows seamlessly in the [Zed](https://zed.dev/) editor on macOS.

This tool runs entirely locally as a Python script. It intercepts problem data from the [Competitive Companion](https://github.com/jmerle/competitive-companion) browser extension, auto-creates your source files with problem-specific template and test cases, allows you to instantly compile and run tests in Zed, and even fully automates submissions to Codeforces and AtCoder using Safari.

## Features

- 📥 **Auto-Parses Problems**: Captures problem requirements and sample test cases via Competitive Companion.
- 🏗️ **Native C++ Template Injection**: Injects test cases directly into block comments at the bottom of the `.cpp` file (no multiple `.in`/`.out` files).
- 🚀 **Auto-Run Test Cases**: Compiles and runs all embedded test cases locally with precise execution time per case.
- 🤖 **Zero-Click Submissions**: Submits directly to Codeforces and AtCoder in the background by automating Safari.
- 📡 **Live Verdict Polling**: Shows live Codeforces status (In queue, Judging, AC, WA) directly in the terminal without ever looking at the browser.
- 🧱 **No WASM/Sandboxing Limits**: Standard Zed CP extensions have network/sandbox limits. This native approach has zero limits.

## Prerequisites

- **macOS** (Relies on AppleScript to interact with Safari)
- **Python 3.8+**
- **Safari** with **"Allow JavaScript from Apple Events"** enabled.
  - *To enable:* Safari -> Preferences -> Advanced -> Check "Show Develop menu". Then Develop -> Allow JavaScript from Apple Events.
- **[Zed Code Editor](https://zed.dev/)**
- **C++ Compiler** (e.g., `g++` installed via Homebrew)
- **[Competitive Companion](https://github.com/jmerle/competitive-companion)** browser extension for Chrome/Firefox/Safari.

## Installation

### 1. Place the Script
Place the main `main.py` script somewhere on your machine, for example into `~/.vc-zed-cp-helper/`.

```bash
mkdir -p ~/.vc-zed-cp-helper
cp main.py ~/.vc-zed-cp-helper/main.py
```

### 2. Add Custom Code Template (Optional)
Put your default `C++` (or Python/Java) template at `~/.vc-zed-cp-helper/boilerplate.cpp`. If this file doesn't exist, it will just leave your new files empty before injecting tests.

### 3. Setup Zed Tasks
Open your Zed tasks file (`~/.config/zed/tasks.json`) and add the following 4 tasks to integrate smoothly with Zed's task runner (`cmd+shift+R`):

```json
[
  {
    "label": "CP: Start Listener (Current Folder)",
    "command": "python3 ~/.vc-zed-cp-helper/main.py listen \"${ZED_DIRNAME}\"",
    "use_new_terminal": true,
    "allow_concurrent_runs": false,
    "hide": "never"
  },
  {
    "label": "CP: Run Tests",
    "command": "python3 ~/.vc-zed-cp-helper/main.py run \"${ZED_FILE}\"",
    "use_new_terminal": false,
    "allow_concurrent_runs": false
  },
  {
    "label": "CP: Submit to Codeforces / AtCoder",
    "command": "python3 ~/.vc-zed-cp-helper/main.py submit \"${ZED_FILE}\"",
    "use_new_terminal": false,
    "allow_concurrent_runs": false
  },
  {
    "label": "CP: Set Language [cpp20]",
    "command": "python3 ~/.vc-zed-cp-helper/main.py set_lang cpp20",
    "use_new_terminal": false,
    "allow_concurrent_runs": false
  }
]
```

### 4. Basic Keymap
```json
[
  {
    "context": "Workspace",
    "bindings": {
      "cmd-'": ["task::Spawn", { "task_name": "CP: Run Tests" }],
      "cmd-enter": ["task::Spawn", { "task_name": "CP: Submit to Codeforces" }],
      "cmd-r": ["task::Spawn", { "task_name": "CP: Start Listener (Current Folder)" }],
    }
  },
]
```
---

## Usage

### 1. Starting the Listener
At the start of your programming session, open Zed and launch the **CP: Start Listener** task.
*Alternatively, run `python3 ~/.vc-zed-cp-helper/main.py listen` in any terminal.*

Click the green `+` on the Competitive Companion extension in your browser when viewing a Codeforces or AtCoder problem. Zed will automatically open the generated source file.

### 2. Set Language 
You only have to do this once. Run the **CP: Set Language [cpp20]** task. 
*(You can press `TAB` before hitting enter to modify it to `cpp23`, `python`, `java`, etc.)*
This saves the active language inside `~/.vc-zed-cp-helper/config.json`. Every "Run" or "Submit" task will use this language.

### 3. Testing 
Solve your problem and save the file. Open the Zed Task Menu (`cmd+shift+R`) and run **CP: Run Tests**. The script compiles the code dynamically and tests every embedded sample case.

### 4. Direct Submissions
Run the **CP: Submit to Codeforces / AtCoder** task. 
- It strips out the embedded test case blocks from the bottom.
- Opens Safari invisibly, finds the Codeforces/AtCoder judge, sets the code, sets your selected language, and safely submits.
- **For Codeforces:** It tracks the submission live and prints `WJ`, `Running on test 5`, until eventually showing `✅ ACCEPTED` or `❌ WRONG ANSWER` directly in Zed's terminal.
- **For AtCoder:** Validates the submission and handles Captchas via Safari forwarding if required.

## Dealing with CAPTCHAs
Platforms like AtCoder, and occasionally Codeforces (via Cloudflare), heavily use invisible CAPTCHAs.
If the automation hits a CAPTCHA wall:
1. The terminal output will alert you: `🔒 CAPTCHA: Please solve the CAPTCHA in Safari`.
2. Safari will be brought to the foreground automatically on the submit page.
3. Once you manually click the CAPTCHA (and/or submit), the script detects the form change, and automatically resumes live verdict polling in your Zed terminal!

## Supporting New Languages
To modify compiler flags or add custom languages (e.g. Rust), just edit the `LANGUAGES` mapping inside `main.py`. You'll need the `cf_id` (Codeforces Language ID) or `ac_id` (AtCoder Language ID) depending on the platform.
