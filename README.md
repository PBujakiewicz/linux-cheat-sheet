# linux-cheat-sheet

<br /><br />

## Delete open files. When df and du show different results.
```bash
find /proc/*/fd -ls | grep '(deleted)' | awk '{print $11}' | xargs -I % sh -c 'echo /dev/null > %'
```

<br /><br />
