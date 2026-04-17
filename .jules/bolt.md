## 2024-04-13 - Batched Process Execution
**Learning:** Shell scripts using standard tools (like `openssl`, `cat`, `tr`, `fold`, `head`) suffer significant performance degradation when called inside a tight loop due to process fork overhead. Specifically, `openssl` called N times takes O(N) process forks.
**Action:** When working with shell snippet performance optimizations, replace loops containing binary invocations with single-pipeline batched operations whenever possible. For example, batch random byte generation by requesting `N * bytes` and folding the output instead of executing the generator N times. Also avoid unnecessary uses of `cat <file> | ...` when `< <file>` or native shell operations are available.
## 2024-04-14 - Bash Native Port Checking
**Learning:** Checking a port via bash using `cat < /dev/null > /dev/tcp/host/port` involves executing the `cat` binary.
**Action:** Instead, use native bash file descriptor redirection directly: `</dev/tcp/host/port`. This eliminates a process fork and makes the snippet more performant, particularly when invoked in a tight loop.

## 2024-04-14 - jq Raw Output
**Learning:** Stripping quotes from `jq` output using `| tr -d "\""` creates an additional process fork in a pipeline.
**Action:** Use `jq -r` instead to output raw strings. This removes the need for `tr` entirely.
