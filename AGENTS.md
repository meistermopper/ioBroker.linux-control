# ioBroker Development Rules for linux-control

This file defines style guidelines, constraints, and general instructions for AI agents working on the `iobroker.linux-control` codebase to ensure 100% ioBroker conformity, safety, and stability.

## 1. Asynchronous Error Handling (Crash Prevention)
- **Constraint:** All asynchronous API calls, network requests (e.g., Axios), SSH sessions (`node-ssh`), ping requests, and database operations must be wrapped in `try/catch` blocks or have `.catch()` handlers. Unhandled promise rejections must be avoided at all costs to prevent crash loops.
- **Example:**
  ```javascript
  try {
      await ssh.connect(config);
  } catch (error) {
      this.log.error(`SSH connection failed for ${target}: ${error.message}`);
  }
  ```
- **Event Handlers:** Ensure main entry points like `onReady`, `onStateChange`, and `onUnload` capture all internal errors and log them cleanly instead of crashing the process.

## 2. Object & State Management
- **Rule:** Never call `this.setState()` or `this.setStateAsync()` on states that do not exist in the ioBroker object database.
- **Static States:** If a state is static, it must be defined in [io-package.json](file:///c:/Users/thoma/dev/active/ioBroker.linux-control/io-package.json) under `instanceObjects` first.
- **Dynamic States:** If states are created dynamically (e.g., during polling, device discovery, or custom SSH command creation), you MUST call `this.setObjectNotExistsAsync()` before calling `this.setStateAsync()`.
- **Strict Metadata:** Every new object configuration must contain a valid `common` section specifying:
  - `type` (e.g., `'string'`, `'number'`, `'boolean'`)
  - `role` (must be a standard ioBroker role like `'value.temperature'`, `'switch.power'`, etc.)
  - `read` and `write` flags
  - `def` (default value corresponding to the data type)
- **Object ID Validation:** Object IDs must not contain special characters, spaces, or non-ASCII characters. They should ideally only contain `A-Za-z0-9-_` (and `.` as separator).
- **Explicit Hierarchy:** When creating an object tree dynamically (e.g., `device.channel.state`), you must explicitly create every parent object in the hierarchy (i.e., first the `device` object, then the `channel` object, and finally the `state` object).

## 3. The `ack` Flag Protocol
- **Sensor/Status Updates (`ack: true`):** When updating states with values received from remote Linux hosts or ping results, always set `ack: true` to indicate that the state represents the confirmed current value.
  - *Example:* `await this.setStateAsync("hosts.server1.online", true, true);`
- **User Commands (`ack: false`):** When reacting to state changes triggered by the user (where `state.ack === false` in `onStateChange`), perform the required SSH command / remote action. Upon success, update the state with `ack: true` to confirm the command execution.

## 4. Resource Lifecycle Management (Memory Cleanups)
- **Constraint:** All active intervals, timeouts, SSH sessions, and event listeners must be properly cleaned up in the `onUnload` method of the adapter.
- **Timers:** **NEVER** use Node.js global functions `setTimeout` or `setInterval`. You must always use the adapter-safe methods `this.setTimeout()` or `this.setInterval()`, or store references and clear them explicitly during unload.

## 5. Process Lifecycle Constraints
- **Constraint:** **NEVER** call `process.exit()` within the adapter code. If the adapter needs to be terminated or stopped due to a fatal error, you must call `this.terminate()` (or `this.terminate(reason, exitCode)`) instead.

## 6. Config UI & Internationalization (i18n)
- **Constraint:** Maintain JSONConfig or Admin UI compatibility.
- **Translation:** Never write direct/hardcoded translations in UI files. Always configure translation keys corresponding to files in the `admin/i18n` directory or `admin/words.js`.

## 7. Local Code Verification
- **Workflow:** Before finishing any code modification or pushing, run:
  ```bash
  npm test
  ```
  Make sure ESLint and unit/integration tests pass.

## 8. Node.js Built-in Module Imports
- **Constraint:** When requiring or importing Node.js built-in modules (e.g., `fs`, `path`, `os`, `child_process`), use clean module requires.
- **Examples:**
  ```javascript
  const fs = require('node:fs');
  const path = require('node:path');
  ```

## 9. Changelog & Release Guidelines
- **Constraint:** Whenever you make changes to the repository (source code, documentation, scripts), add a descriptive bullet point of your changes under the latest version section or changelog in [README.md](file:///c:/Users/thoma/dev/active/ioBroker.linux-control/README.md) and `io-package.json`.
- **Git Commits:** Always prepare Git commit messages in English using the **Conventional Commits** standard (e.g., `feat: add remote control status`, `fix: handle SSH timeout rejection`).
