## 2024-05-18 - Bash Script Optimization: Avoid unnecessary process forks
**Learning:** Performance optimization in bash scripts involves reducing the number of external processes spawned. Using pipes like `cat <file> | command` creates an unnecessary `cat` process. Similarly, `$(seq ...)` spawns a subshell and executes the `seq` external binary.
**Action:** Replace `cat` pipes with input redirection (`command < file`). Replace `seq` with native bash loops (e.g., `for ((i=1; i<=count; i++)); do`) to avoid subshells and extra forks.
