# linux-cheat-sheet

<br /><br />

## Delete open files. When df and du show different results.
```bash
find /proc/*/fd -ls | grep '(deleted)' | awk '{print $11}' | xargs -I % sh -c 'echo /dev/null > %'
```

<br /><br />

## Cron syntax.
```bash
*     *     *   *    *       user   command_to_execute
|     |     |   |    |
|     |     |   |    |
|     |     |   |    +----- day of the week (0 - 7) [both 0 and 7 represent Sunday]
|     |     |   +------- month (1 - 12)
|     |     +--------- day of the month (1 - 31)
|     +----------- hour (0 - 23)
+------------- minute (0 - 59)

Every 5 min
*/5 * * * * user command_to_execute

Every hour, from 9:00 to 18:00
00 09-18 * * * user command_to_execute

Every day, at 9:00 and 18:00
00 09,18 * * * user command_to_execute

Every day, at 17:30
30 17 * * * user command_to_execute

Every weekday, at 17:30
30 17 * * Mon,Tue,Wed,Thu,Fri user command_to_execute
```

<br /><br />

## Find, Search by directory name.
```bash
find . -type d -name 'text'
```

<br /><br />

## Grep, Regular expression to search for text in files.
```bash
grep -rni -E '^text' .
```

<br /><br />

## Checking the ssl certificate.
```bash
openssl x509 -in /dir/ca.crt -text -noout
```

<br /><br />
