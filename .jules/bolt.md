## 2024-04-04 - Shell Script Performance Optimizations
**Learning:** Shell scripts frequently contain unnecessary process forks, like piping `cat /dev/urandom` into another command instead of using standard input redirection (`< /dev/urandom`), or using `seq` inside a for-loop instead of native bash loops (`for ((i=1; i<=N; i++))`). These unnecessary forks can dramatically slow down script execution time when called frequently.
**Action:** Always favor native bash constructs and standard input redirection over spawning additional processes like `cat` and `seq`.
