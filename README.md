# linux-cheat-sheet

<br /><br />

## Delete open files. When df and du show different results.
```bash
find /proc/*/fd -ls | grep '(deleted)' | awk '{print $11}' | xargs -I % sh -c 'echo /dev/null > %'
```

<br /><br />

## Du with excludes.
```bash
du --exclude=/d0 --exclude=/d1 -hd 1 /
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
find . -type d -name '*text.txt'
```

<br /><br />

## Find, Search by last modified.
```bash
find . -type f -printf '%TY-%Tm-%Td %TT %p\n' | sort -r
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


## My bash prompt (PS1).
```bash
#vim .bashrc

#########################################################################

timer_start() {
  timer=${timer:-$SECONDS}
}
timer_stop() {
  timer_show=$(($SECONDS - $timer))
  unset timer
}
trap 'timer_start' DEBUG
if [ "$PROMPT_COMMAND" == "" ]; then
  PROMPT_COMMAND="timer_stop"
else
  PROMPT_COMMAND="$PROMPT_COMMAND; timer_stop"
fi

# https://github.com/jonmosco/kube-ps1
source /home/pbujakiewicz/.kube-ps1/kube-ps1.sh
KUBE_PS1_SYMBOL_COLOR='cyan'
KUBE_PS1_CTX_COLOR='green'
KUBE_PS1_NS_ENABLE='false'
KUBE_PS1_PREFIX='['
KUBE_PS1_SUFFIX=']'

parse_git_branch() {
  git branch 2> /dev/null | sed -e '/^[^*]/d' -e 's/* \(.*\)/\1/'
}

if [ "$color_prompt" = yes ]; then
  PS1='\n[\[\033[01;32m\]\D{%H:%M:%S}\[\033[00m\]]\[\e[m\] [\[\033[01;36m\]\w\[\033[00m\]] [\[\033[01;32m\]$(parse_git_branch)\[\033[00m\]]\[\e[m\] [${timer_show}s]\n$(kube_ps1)\n\[\033[01;32m\]\$\[\033[00m\]\[\e[m\] '
else
  PS1='\n[D{%H:%M:%S}] [\w] [$(parse_git_branch)] [${timer_show}s]\n$(kube_ps1)\n\$ '
fi

#########################################################################
```

<br /><br />

## Git rebase master on branch.
```bash
git fetch
git rebase origin/master
```

<br /><br />

## LDAP search.
```bash
No TLS
ldapsearch -x -H "ldap://127.0.0.1:389" -D "CN=ldap auth,OU=SA,DC=company,DC=com" -w password -b "OU=Search here,DC=company,DC=com" "(&(objectClass=group)(|(cn=dev*)(cn=devops*)))"

TLS
env LDAPTLS_CACERT=domain.cer ldapsearch -x -H "ldaps://127.0.0.1:389" -D "CN=ldap auth,OU=SA,DC=company,DC=com" -w password -b "OU=Search here,DC=company,DC=com" "(&(objectClass=group)(|(cn=dev*)(cn=devops*)))"

or graphical solution
Apache Directory Studio - https://directory.apache.org/studio/
```

<br /><br />

## LVM Resize.
```bash
# scan pci for new disk
find /sys -iname 'scan'
echo "- - -" > /sys/devices/pci0000:00/0000:00:07.1/host0/scsi_host/host0/scan

# scan resize old disk
echo 1>/sys/class/block/sdX/device/rescan

# create PV and add to group
pvcreate /dev/sda
sudo vgextend centos /dev/sda

# scan when PV is whole disk and is added to group
pvresize /dev/sda

# add 10 GB
lvextend -L+10G /dev/mapper/vol

# add 100% free space
lvextend -l+100%FREE /dev/mapper/vol

# xfs file system
xfs_growfs /dev/centos/var

# ext3/4 file system
resize2fs /dev/centos/var
```

<br /><br />

## innodb_buffer_pool_size mysql
```bash
# go root
sudo su -

#checking current values
grep 'innodb_buffer_pool_size' /etc/my.cnf
echo "SHOW GLOBAL VARIABLES LIKE 'innodb_buffer_pool_size';" | mysql
echo "SELECT ROUND (@@innodb_buffer_pool_size / 1024 / 1024 / 1024, 2) 'innodb_buffer_pool_size (GB)';" | mysql

#recalculation
echo $((307 * 1024 * 1024 * 1024))
echo $((329638739968 / 1024 / 1024 / 1024))

#change
#mysql
SET GLOBAL innodb_buffer_pool_size= NEW_SIZE;
#file
vim /etc/my.cnf
```

<br /><br />
