# The Twelve Lunar Commandments

1. NEVER use raw strings for Windows paths in Python patch specs or scripts.
   Use regular strings with doubled backslashes ("X:\\Models\\brain"),
   or write the path/replacement text to a config file first and read it in.

2. Write every spec or config file with the Write tool, never through a Bash
   heredoc: the shell collapses "\\" to "\", so "\b", "\e", "\s", "\U" turn into
   escapes (agent/brain → "agentrain") and the anchor silently misses.

3. File-writing scripts take required path arguments, never defaults that can
   drift. Check the argument count before any work starts and exit with a usage
   line showing the one canonical call; create the parent folder when it is
   gitignored.

4. Never commit binary assets (GIFs, MP4s, large media) to the main branch.
   Put them in a releases/ or assets/ folder covered by .gitignore, or use
   Git LFS. Re-renders bloat the repo and slow every clone.

5. Validate every path argument before any work starts. Resolve it, check the
   parent exists or can be created, and fail with a clear usage line on the
   first bad call — never let a script run halfway on a broken path.

6. Fail loud, never silent. A wrong default that writes to an untracked folder
   is worse than no default: the failure is invisible until someone looks.
   Prefer a required argument that errors immediately over a default that
   drifts.

7. One source of truth. Never paste the same function, block, or string into
   two files. Extract it to a shared module and import it — a second copy is
   a bug waiting to diverge.

8. Verify before you trust. After every write, run a compile or syntax check
   (py_compile, node --check) before moving on. Never assume a write landed
   clean.

9. Break the loop from outside. When the agent is stuck retrying the same
   failing command, stop it manually, fix the root cause, and only then let it
   continue. Endless retries burn tokens without progress.

10. Guardrails must guard themselves. Any lint, hook, or rule script must be
    written in the style it enforces — no raw strings, no heredocs, no silent
    defaults — or it becomes the thing it was built to catch.

11. Prefer a loud error over a quiet wrong. If a script cannot do its job
    correctly, it must refuse to run rather than produce a plausible-looking
    wrong result.

12. Every rule comes from a real failure, not a guess. Add a commandment only
    after it has burned you once — earned rules stick, invented ones get
    ignored.
