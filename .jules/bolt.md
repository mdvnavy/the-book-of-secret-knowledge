
## 2024-04-13 - Batched Process Execution
**Learning:** Shell scripts using standard tools (like `openssl`, `cat`, `tr`, `fold`, `head`) suffer significant performance degradation when called inside a tight loop due to process fork overhead. Specifically, `openssl` called N times takes O(N) process forks.
**Action:** When working with shell snippet performance optimizations, replace loops containing binary invocations with single-pipeline batched operations whenever possible. For example, batch random byte generation by requesting `N * bytes` and folding the output instead of executing the generator N times. Also avoid unnecessary uses of `cat <file> | ...` when `< <file>` or native shell operations are available.

## 2024-05-15 - Unnecessary Process Forks in Shell Snippets
**Learning:** Using `jq '.Answer[0].data' | tr -d "\""` and `cat < /dev/null > /dev/tcp/...` introduce unnecessary process forks (`tr` and `cat`). These can be optimized using `jq -r` and native bash file descriptor redirection (`</dev/tcp/...`).
**Action:** When optimizing shell scripts, always check if `jq` can handle string formatting natively (e.g., using `-r` for raw output) to avoid piping to `tr` or `sed`. Also, leverage native bash file descriptors to avoid `cat` when creating empty streams or writing to network sockets.

## 2024-04-18 - jq raw output vs piping to tr
**Learning:** Piping `jq` string output to `tr -d "\""` creates an unnecessary process fork penalty. Using `jq -r` provides the exact same unquoted string natively, saving milliseconds and simplifying the snippet.
**Action:** Always check if string processing utilities (`tr`, `sed`, `awk`) piped after `jq` can be replaced by native `jq` features like the `-r` flag to eliminate process forks.

## 2024-05-18 - Parameter Expansion over Subshells
**Learning:** Using subshells to execute external binaries like `cut` (e.g., `echo "$VAR" | cut -d ":" -f1`) introduces significant process fork overhead, drastically reducing performance, especially in loops or frequently executed scripts.
**Action:** When extracting substrings in Bash, always prioritize native parameter expansion (e.g., `${VAR%%:*}` and `${VAR##*:}`) over spawning subshells with `echo` and piping to string processing utilities like `cut` or `awk`.
