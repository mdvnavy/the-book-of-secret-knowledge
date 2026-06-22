
## 2024-04-13 - Batched Process Execution
**Learning:** Shell scripts using standard tools (like `openssl`, `cat`, `tr`, `fold`, `head`) suffer significant performance degradation when called inside a tight loop due to process fork overhead. Specifically, `openssl` called N times takes O(N) process forks.
**Action:** When working with shell snippet performance optimizations, replace loops containing binary invocations with single-pipeline batched operations whenever possible. For example, batch random byte generation by requesting `N * bytes` and folding the output instead of executing the generator N times. Also avoid unnecessary uses of `cat <file> | ...` when `< <file>` or native shell operations are available.

## 2024-05-15 - Unnecessary Process Forks in Shell Snippets
**Learning:** Using `jq '.Answer[0].data' | tr -d "\""` and `cat < /dev/null > /dev/tcp/...` introduce unnecessary process forks (`tr` and `cat`). These can be optimized using `jq -r` and native bash file descriptor redirection (`</dev/tcp/...`).
**Action:** When optimizing shell scripts, always check if `jq` can handle string formatting natively (e.g., using `-r` for raw output) to avoid piping to `tr` or `sed`. Also, leverage native bash file descriptors to avoid `cat` when creating empty streams or writing to network sockets.

## 2024-04-18 - jq raw output vs piping to tr
**Learning:** Piping `jq` string output to `tr -d "\""` creates an unnecessary process fork penalty. Using `jq -r` provides the exact same unquoted string natively, saving milliseconds and simplifying the snippet.
**Action:** Always check if string processing utilities (`tr`, `sed`, `awk`) piped after `jq` can be replaced by native `jq` features like the `-r` flag to eliminate process forks.
## 2026-05-20 - Parameter Expansion Over Subshells
**Learning:** Replacing subshells and external binaries (`echo | cut`) with native bash parameter expansion (`${VAR%%:*}` and `${VAR##*:}`) eliminates process forks and drastically improves performance in shell scripts.
**Action:** Always prefer native bash parameter expansion over piping to external commands for simple string manipulation.

## 2024-05-26 - Eliminate Process Forks in find -exec
**Learning:** In bash script snippets processing files, using `find -exec ... \;` spawns a new subprocess for every matched file, leading to severe performance bottlenecks on large directories. The `rmdir` operation can be fully native.
**Action:** Replace `find -exec ... \;` with `find -exec ... +` to batch arguments into a single subprocess execution. Replace `-exec rmdir {} \;` with `-delete` (using `-mindepth 1` if necessary to protect the root dir) to utilize find's native C-level deletion, completely bypassing subshells.
## 2024-05-18 - Replacing inaccurate regex and xargs in shell scripts
**Learning:** Using `grep -Eo "[1-9][0-9]*"` to extract ports from `netstat` output is buggy because it matches IP segments (e.g., matching `127` and `1` in `127.0.0.1:80`). Also, `xargs -I {} sh -c "..."` causes O(N) process forks overhead.
**Action:** Use precise field parsing like `awk '/pattern/ {n=split($4, a, ":"); print a[n]}'` to isolate the port, and replace `xargs sh -c` with native bash loops like `| while read -r line; do ... done` to eliminate subshells and forks.
