# The Twelve Lunar Commandments — Code Expansions

This file expands each commandment into concrete, compilable patterns for the major OS and runtime surfaces an agent may touch:

- **OS**: Windows, Linux, macOS
- **Shell / multiplexer**: PowerShell, bash, zsh, tmux
- **Language / runtime**: Python, Java, Node.js, shell scripts

Each commandment keeps its sigil from `LUNAR-SIGILS.md`. When a model sees the sigil, it should recall the full rule and apply the matching code pattern below for the detected OS and runtime.

---

## ☉ Commandment I — No raw strings for Windows paths

**Rule:** Never embed Windows paths as raw strings. The shell and Python string parsers eat backslashes.

### Python
```python
# BAD — raw string with a Windows path collapses or mis-parses
path = r"C:\Users\Owner\Models\brain.gguf"

# GOOD — doubled backslashes in a normal string
path = "C:\\Users\\Owner\\Models\\brain.gguf"

# GOOD — pathlib (OS-agnostic)
from pathlib import Path
path = Path("C:/Users/Owner/Models/brain.gguf")

# GOOD — read from a config file instead of hardcoding
import json
cfg = json.loads(Path("config.json").read_text(encoding="utf-8"))
path = cfg["brain_path"]
```

### PowerShell
```powershell
# BAD — unquoted backslashes get eaten
$path = C:\Users\Owner\Models\brain.gguf

# GOOD — single-quoted literal
$path = 'C:\Users\Owner\Models\brain.gguf'

# GOOD — Join-Path (OS-aware)
$path = Join-Path $env:USERPROFILE 'Models\brain.gguf'
```

### bash / zsh / Linux / macOS
```bash
# GOOD — always quote; prefer $HOME over hardcoding
path="$HOME/Models/brain.gguf"

# GOOD — realpath resolves symlinks
path="$(realpath "$HOME/Models/brain.gguf")"
```

### tmux
```bash
# Run the command inside a pane; never let tmux interpolate backslashes
tmux send-keys -t dev "python train.py --model '$HOME/Models/brain.gguf'" Enter
```

### Java
```java
// GOOD — use Path, never raw backslash strings
Path p = Paths.get(System.getProperty("user.home"), "Models", "brain.gguf");
```

### Node.js
```js
// GOOD — path.join is OS-aware
const path = require('path');
const p = path.join(process.env.HOME || process.env.USERPROFILE, 'Models', 'brain.gguf');
```

---

## ╲╲ Commandment II — No heredocs / shell-passed scripts

**Rule:** Never write multi-line scripts through a shell heredoc. Quoting collapses and the file corrupts.

### Python
```python
# BAD — heredoc through bash mangles quotes
# os.system('''cat > file.py <<'EOF'
# ...
# EOF''')

# GOOD — write directly with the file API
from pathlib import Path
Path("file.py").write_text(content, encoding="utf-8")
```

### PowerShell
```powershell
# BAD — here-string can still mangle depending on quoting
@'
content
'@ | Set-Content file.py

# GOOD — .NET write, explicit encoding
[System.IO.File]::WriteAllText("$pwd\file.py", $content, [System.Text.UTF8Encoding]::new($false))
```

### bash / zsh
```bash
# BAD — heredoc
cat > file.py <<'EOF'
...
EOF

# GOOD — printf or a here-doc with a quoted delimiter is still risky;
# prefer a dedicated writer
python - <<'PY'
from pathlib import Path
Path("file.py").write_text(content, encoding="utf-8")
PY
```

### tmux
```bash
# Send keystrokes one line at a time; never paste a heredoc into a pane
tmux send-keys -t dev "python -c 'open(\"file.py\",\"w\").write(content)'" Enter
```

### Java
```java
// GOOD — Files.write, never shell redirection
Files.writeString(Path.of("file.py"), content, StandardOpenOption.CREATE);
```

### Node.js
```js
// GOOD — fs.writeFileSync
fs.writeFileSync('file.py', content, 'utf8');
```

---

## 🎁 Commandment III — Require the path; no silent defaults

**Rule:** File-writing scripts take the path as a required argument. No default that can drift.

### Python
```python
import argparse, sys
from pathlib import Path

p = argparse.ArgumentParser()
p.add_argument("out", help="output path (required)")
args = p.parse_args()
out = Path(args.out)
if not args.out:
    p.error("usage: script.py <out-path>")
out.parent.mkdir(parents=True, exist_ok=True)
```

### PowerShell
```powershell
param(
  [Parameter(Mandatory=$true)]
  [string]$OutPath
)
if (-not $OutPath) { Write-Error "usage: .\script.ps1 -OutPath <path>"; exit 1 }
New-Item -ItemType Directory -Force -Path (Split-Path $OutPath) | Out-Null
```

### bash / zsh
```bash
#!/usr/bin/env bash
set -euo pipefail
out="${1:-}"
if [[ -z "$out" ]]; then
  echo "usage: $0 <out-path>" >&2
  exit 1
fi
mkdir -p "$(dirname "$out")"
```

### tmux
```bash
# Pass the arg through the pane; never rely on a default inside the session
tmux send-keys -t dev "./script.sh /abs/path/out.txt" Enter
```

### Java
```java
public static void main(String[] args) {
    if (args.length < 1) {
        System.err.println("usage: Script <out-path>");
        System.exit(1);
    }
    Path out = Paths.get(args[0]);
    Files.createDirectories(out.getParent());
}
```

### Node.js
```js
const out = process.argv[2];
if (!out) { console.error('usage: node script.js <out-path>'); process.exit(1); }
fs.mkdirSync(path.dirname(out), { recursive: true });
```

---

## ⧉ Commandment IV — One source of truth; no duplicates

**Rule:** Never paste a second copy of a function or block. Import or reference it.

### Python
```python
# BAD — loadSocketIO defined twice in the same file
# GOOD — single module, imported everywhere
# common.py
def load_socket_io(): ...
# server.py
from common import load_socket_io
```

### bash / zsh
```bash
# BAD — copy-pasted function body in two scripts
# GOOD — source a shared lib
# lib.sh
load_socket_io() { ...; }
# script.sh
source "$(dirname "$0")/lib.sh"
load_socket_io
```

### PowerShell
```powershell
# GOOD — dot-source a module
. "$PSScriptRoot\lib.ps1"
Load-SocketIo
```

### Java
```java
// GOOD — one class, imported
import com.example.SocketIo;
```

### Node.js
```js
// GOOD — one module.exports, required everywhere
// lib.js
module.exports = { loadSocketIo };
// server.js
const { loadSocketIo } = require('./lib');
```

### tmux
```bash
# Keep shared libs outside panes; panes only call them
tmux send-keys -t dev "source lib.sh && run" Enter
```

---

## ✓ Commandment V — Verify before you trust

**Rule:** Run a syntax or compile check after every write. Never assume it worked.

### Python
```bash
python -m py_compile file.py
# or
python -c "import ast; ast.parse(open('file.py').read())"
```

### PowerShell
```powershell
$errors = $null
$null = [System.Management.Automation.Language.Parser]::ParseFile(
  'file.ps1', [ref]$null, [ref]$errors)
if ($errors) { $errors | ForEach-Object { $_.ToString() }; exit 1 }
```

### bash / zsh
```bash
bash -n script.sh
# or
shellcheck script.sh
```

### Java
```bash
javac File.java
```

### Node.js
```bash
node --check file.js
```

### tmux
```bash
tmux send-keys -t dev "python -m py_compile file.py && echo OK" Enter
```

---

## 🔓 Commandment VI — Break the loop from outside

**Rule:** When the agent is stuck retrying, stop it and fix the root cause manually.

### General
```text
1. Kill the running command (Ctrl+C in the pane).
2. Identify the missing binary or bad path.
3. Run the command once by hand with the absolute path.
4. Only then let the agent resume.
```

### tmux
```bash
tmux send-keys -t dev C-c
tmux send-keys -t dev "/abs/path/to/binary --flag" Enter
```

### PowerShell
```powershell
Stop-Process -Name <proc> -Force -ErrorAction SilentlyContinue
& 'C:\abs\path\to\binary.exe' --flag
```

---

## 🛡️ Commandment VII — The guard guards itself

**Rule:** The lint/hook script must follow its own rules — no raw strings, no heredocs inside it.

### Python (the hook)
```python
# lint_dupes_pre_write.py — itself must parse with doubled backslashes only
import re
DRIVE = re.compile(r"[A-Za-z]:\\")   # doubled, not raw-with-path
```

### bash
```bash
# The hook runner must not call itself through a heredoc
python lint_dupes_pre_write.py < payload.json
```

---

### 📢 Commandment VIII — Fail loud, never quiet

**Rule:** A loud error is mercy; a quiet wrong is a curse. Exit non-zero and print usage.

### Python
```python
import sys
sys.stderr.write("error: missing --out; usage: script.py --out <path>\n")
sys.exit(2)
```

### PowerShell
```powershell
Write-Error "missing -OutPath; usage: .\script.ps1 -OutPath <path>"
exit 2
```

### bash
```bash
echo "error: missing out path; usage: $0 <path>" >&2
exit 2
```

### Java
```java
System.err.println("error: missing out path");
System.exit(2);
```

### Node.js
```js
console.error('error: missing --out; usage: node script.js --out <path>');
process.exit(2);
```

---

## 🗄️ Commandment IX — No binaries in the text house

**Rule:** Keep binary assets (GIFs, MP4s, models) out of the main git tree.

### bash / git
```bash
# .gitignore
releases/marketing/*.gif
releases/marketing/*.mp4
*.gguf

# or use Git LFS for large tracked binaries
git lfs track "*.gguf"
git add .gitattributes
```

### PowerShell
```powershell
# Add to .gitignore via here is banned — write directly
"releases/marketing/*.gif`nreleases/marketing/*.mp4`n*.gguf" |
  Set-Content -Encoding utf8 .gitignore
```

---

## 🔢 Commandment X — Count arguments before work

**Rule:** Validate argc/argv before doing anything.

### Python
```python
import sys
if len(sys.argv) < 2:
    print("usage: script.py <arg>", file=sys.stderr); sys.exit(2)
```

### bash
```bash
if [[ $# -lt 1 ]]; then echo "usage: $0 <arg>" >&2; exit 2; fi
```

### PowerShell
```powershell
if ($args.Count -lt 1) { Write-Error "usage: .\script.ps1 <arg>"; exit 2 }
```

### Java
```java
if (args.length < 1) { System.err.println("usage: Script <arg>"); System.exit(2); }
```

### Node.js
```js
if (process.argv.length < 3) { console.error('usage: node script.js <arg>'); process.exit(2); }
```

---

## 🔥 Commandment XI — Laws born of wounds, not fears

**Rule:** Only add a commandment after a real failure. No speculative rules.

### Process
```text
1. A command fails in a new way.
2. Diagnose the root cause.
3. Write one rule that prevents exactly that failure.
4. Add it to LUNAR.md and LUNAR-SIGILS.md together.
5. Never add a rule for a failure you only imagine.
```

---

## 🌙 Commandment XII — The moon changes; laws must too

**Rule:** Revisit the commandments when the stack or workflow changes.

### Process
```text
- New OS, runtime, or shell → re-check every code block above.
- New failure mode → Commandment XI fires → add or revise a rule.
- Stale rule that no longer matches reality → delete it.
```

---

## How the model should use this file

1. Detect the OS and runtime from the environment or the file extension.
2. When a sigil (☉, ╲╲, 🎁, …) appears, recall the commandment.
3. Emit or apply the matching code block for that OS/runtime.
4. Never mix patterns from different OSes in one script.
5. After writing, run the verify step from Commandment V before trusting the result.
