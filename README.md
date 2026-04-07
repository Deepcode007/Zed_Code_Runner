# Zed CP Helper 🚀

All legacy Sublime files have been removed! This is now a pure Zed Competitive Programming environment powered entirely by a single, ultra-fast Python CLI (`zed_cp.py`).

## Global Installation

Because Zed specifically prevents public Extensions from registering arbitrary keyboard shortcuts or running unauthorized bash/python scripts (like invoking `g++` or `Safari AppleScript`) for security reasons, **you do not "install" this as a traditional extension**. 

Instead, you register it in your **Global Zed Config** so it automatically works on any C++ file you ever open, no matter which folder you are in! Zed will know exactly where your code runs because you supply the absolute path to this folder.

### Step 1: Add Global Tasks
1. Open Zed.
2. Open the Command Palette (`Cmd + Shift + P`), type **`zed: open tasks`**, and hit Enter.
3. Paste the following configuration, which tells Zed exactly where your script lives:

```json
[
  {
    "label": "CP: Start Companion Listener",
    "command": "python3 /Users/hello/Desktop/PCode/Projects/zed-cp-helper/zed_cp.py listen .",
    "use_new_terminal": false,
    "allow_concurrent_runs": false
  },
  {
    "label": "CP: Run Tests",
    "command": "python3 /Users/hello/Desktop/PCode/Projects/zed-cp-helper/zed_cp.py run ${ZED_FILE}",
    "use_new_terminal": false,
    "allow_concurrent_runs": false
  },
  {
    "label": "CP: Submit to Codeforces",
    "command": "python3 /Users/hello/Desktop/PCode/Projects/zed-cp-helper/zed_cp.py submit ${ZED_FILE}",
    "use_new_terminal": false,
    "allow_concurrent_runs": false
  }
]
```

### Step 2: Add Global Keymaps
1. Open the Command Palette again, type **`zed: open keymap`** and hit Enter.
2. Merge the following bindings inside the `"context": "Workspace"` block. (You can change these shortcuts to whatever you used in Sublime!) 

```json
[
  {
    "context": "Workspace",
    "bindings": {
      "ctrl-shift-b": ["task::Spawn", { "task_name": "CP: Run Tests" }],
      "ctrl-shift-s": ["task::Spawn", { "task_name": "CP: Submit to Codeforces" }],
      "ctrl-shift-l": ["task::Spawn", { "task_name": "CP: Start Companion Listener" }]
    }
  }
]
```

---

## Workflow 🖥️

1. Launch **`CP: Start Companion Listener`** via your `Ctrl + Shift + L` shortcut! It will boot up your listener in the current directory.
2. Click the green `(+)` in the **Competitive Companion** browser extension. `zed_cp.py` receives the problem and injects tests natively into the bottom of a new `.cpp` file.
3. Once you've written your solution, press **`Ctrl + Shift + B`** to compile and run against the stored test-cases inside your file!
4. Once tests pass, hit **`Ctrl + Shift + S`** to fire off the Codeforces submission silently in the background.

Happy coding!
