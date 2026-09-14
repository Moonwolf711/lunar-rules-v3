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
