# LUNAR Rules

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
