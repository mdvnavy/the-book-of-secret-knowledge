## 2024-04-13 - Batched Process Execution
**Learning:** Shell scripts using standard tools (like `openssl`, `cat`, `tr`, `fold`, `head`) suffer significant performance degradation when called inside a tight loop due to process fork overhead. Specifically, `openssl` called N times takes O(N) process forks.
**Action:** When working with shell snippet performance optimizations, replace loops containing binary invocations with single-pipeline batched operations whenever possible. For example, batch random byte generation by requesting `N * bytes` and folding the output instead of executing the generator N times. Also avoid unnecessary uses of `cat <file> | ...` when `< <file>` or native shell operations are available.

## 2024-04-14 - Bash Native Socket Optimizations
**Learning:** Shell pipelines using `cat < /dev/null > /dev/tcp/...` spawn unnecessary process forks for `bash` invocations and standard command evaluations.
**Action:** Use native bash redirection such as `</dev/tcp/...` when attempting to test open ports instead of `cat < /dev/null > /dev/tcp/...` to significantly improve execution time.
