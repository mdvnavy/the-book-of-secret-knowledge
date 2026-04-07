## 2024-05-24 - Bash Native Iteration Optimization
**Learning:** Using `for i in $(seq 1 "$_count")` spawns an expensive subshell and process (`seq`) for every invocation. This is incredibly inefficient for tight loops in bash.
**Action:** Replace `for i in $(seq ...)` with bash native C-style loops `for ((i=1; i<=$_count; i++)); do`. It avoids subshell and process forks entirely.

## 2024-05-24 - Avoiding `cat` pipes
**Learning:** Piping `cat file` to another command (e.g. `cat file | grep ...` or `cat /dev/urandom | tr ...`) causes unnecessary forking and context switching.
**Action:** Always prefer input redirection `< file` directly to the target command instead of using `cat |`.