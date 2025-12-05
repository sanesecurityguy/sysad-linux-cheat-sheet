Check the **CPU** usage of the 5 most CPU-intensive processes:<br>
`ps -eo %cpu,comm,pid,user,etime --sort=-%cpu | head -6`<br>
Sometimes you'll want to use `top` instead of `ps`. Sometimes `ps` is inaccurate while `top` is accurate.<br>
`top -d 10 -o +%CPU`<br>
Check the **memory** usage of the 5 most memory-hungry processes:<br>
`ps -eo %mem,comm,pid,user,etime --sort=-%mem | head -6`<br>
See a process's full process tree along with its arguments:<br>
`pstree -pals [PID]`

List all partitions:<br>
`df -h -x tmpfs -x devtmpfs`<br>
List only local partitions:<br>
`df -hl -x tmpfs -x devtmpfs`

Find the 9 largest **directories** in /dir/ (does not work correctly at root directory /):<br>
`du -hx -d 1 /dir/ | sort -hr | head`<br>
Find the 9 largest **directories and files** in /dir/ (does not work correctly at root directory /):<br>
`du -hax -d 1 /dir/ | sort -hr | head`<br>
Find the 10 largest **directories and files** in root directory / (This is necessary because `du -d` provides incorrect output when run on the root directory):<br>
`du -shx /* 2>/dev/null | sort -hr | head`

Recursively find the 20 largest **files** in /dir/ larger than 1 gibibyte:<br>
`find /dir/ -type f -size +1G -exec du -h "{}" \+ | sort -hr | head -20`<br>
For files smaller than 1 gibibyte: replace `+1G` with `-1G`<br>
For files larger than 500 mebibytes: replace `+1G` with `+500M`

Recursively find the 20 largest **files** in /dir/ larger than 1 gibibyte, older than 1 year old, and give the total space used by all such files (not just the largest 20):<br>
`find /dir/ -type f -size +1G -mtime +365 -exec du -chx "{}" \+ | sort -h | tail -21`

Find the 10 most recently modified files and directories in /dir/:<br>
`stat -c "%y %n" /dir/* | sort -hr | head -10`<br>
**Recursively** find the 10 most recently modified files in /dir/ that have been modified in the last 24 hours:<br>
`find /dir/ -type f -mtime -1 -exec stat --format '%Y :%y %n' "{}" \; | sort -nr | head -10 | cut -d ":" -f 2-`

See which processes are writing the most to disk:<br>
`iotop -Po -d 10`<br>
Use the [LEFT] and [RIGHT] arrow keys to choose which column to sort the results by, and use the [R] key to reverse the sorting order.<br>
See which files and directories a process is writing to:<br>
`lsof -p [PID]`

Backup a directory to another location:<br>
`rsync -vazHAP [SOURCE] [DESTINATION]`<br>
Same as above but with root privileges on the remote machine:<br>
`rsync --rsync-path="sudo rsync" -vazHAP [SOURCE] [DESTINATION]`<br>
Same as above but using an AskPass program to provide a mandatory password upon sudo-ing:<br>
`rsync --rsync-path="export SUDO_ASKPASS=[PATH-TO-ASKPASS-PROGRAM]; sudo -A rsync" -vazHAP [SOURCE] [DESTINATION]`

Check what processes are listening on what ports:<br>
`ss -tupln`<br>
Check what Docker containers are listening on what ports:<br>
`docker ps --format "table {{.Names}}\t{{.Ports}}"`<br>
Check what Podman containers are listening on what ports:<br>
`podman ps --format "table {{.Names}}\t{{.Ports}}"`

List all virtual machines:<br>
`virsh list --all`<br>
List a virtual machine's allocated CPU's:<br>
`virsh vcpucount [VM-NAME]`<br>
List a virtual machine's allocated memory:<br>
`virsh dommemstat [VM-NAME] | grep actual`<br>
List the mount points of a virtual machine's drives:<br>
`virsh domblklist [VM-NAME]`<br>
Start, stop, or reboot a virtual machine:<br>
`virsh start [VM-NAME]`<br>
`virsh shutdown [VM-NAME]`<br>
`virsh reboot [VM-NAME]`

Verify that one or more TLS certificates are valid:<br>
`openssl verify -CApath /etc/ssl/certs/ -CAfile [CA-BUNDLE.crt] [TLS.crt] [TLS-2.crt] [TLS-3.crt] [...]`<br>
Note that `-CApath /etc/ssl/certs/` is necessary when the CA bundle doesn't contain the root CA certificate, which is normal and expected by most web browsers but not expected by `openssl`.

Record to the terminal the output of one or more commands every 5 seconds:<br>
`while sleep 5; do [ONE OR MORE COMMANDS SEPARATED BY SEMICOLONS]; echo; done`<br>
**Stop it with [Ctrl + C]**<br>
Record in the background to a file the time (with time zone) and output of one or more commands every 5 seconds, and save the recording command's PID:<br>
`while sleep 5; do date +"%H:%M:%S %:z"; [COMMAND 1]; [COMMAND 2]; [...]; echo; done >> record.log & echo $! > recorder.pid`<br>
Monitor updates to that file:<br>
`tail -f record.log`<br>
Remember to kill it when you don't need it anymore, otherwise it will perpetually eat up drive space:<br>
`kill $(cat recorder.pid) && rm recorder.pid`<br>
In case you lose the recording command's PID, it will show up here if you're still in the same terminal session:<br>
`jobs -l`<br>
If you're not in the same terminal session, it will still show up here, though with unrelated processes, so try not to kill anything important:<br>
`ps -C bash -o pid,user,args`<br>
You can see the PID of the current terminal session with this:<br>
`echo $$`

Search for SELinux denials:<br>
`ausearch -m AVC,USER_AVC,SELINUX_ERR,USER_SELINUX_ERR -ts recent -c [COMMAND-OR-PROCESS-NAME]`<br>
If you think SELinux is denying operations inside a container, but don't see any denials from `ausearch` try:<br>
`semanage dontaudit off`<br>
and then run `ausearch` again. Don't forget to<br>
`semanage dontaudit on`<br>
when you're done testing.

SSH through a jump server:<br>
`ssh -J [USERNAME]@[JUMP-SERVER-IP] [USERNAME]@[TARGET-IP]`<br>
Create an SSH tunnel to a TCP socket through a jump server:<br>
`ssh -L localhost:[LOCAL-PORT]:[TARGET-IP]:[TARGET-PORT] -N [USERNAME]@[JUMP-SERVER-IP]`

Completely clear your bash history:<br>
`cat /dev/null > ~/.bash_history && history -c`<br>
You might want to do this when you have sensitive information like passwords in your bash history.

### DNF

List installed packages:<br>
`dnf ls --installed`<br>
List files in a package:<br>
`dnf rq -l [PACKAGE]`<br>
See what package/s provide a file:<br>
`dnf wp [FILENAME]`<br>
See patches already installed for a CVE:<br>
`dnf upif --list --with-cve --installed | grep [CVE]`<br>
See patches for a CVE that are available but not yet installed:<br>
`dnf upif --list --with-cve | grep [CVE]`

### Zypper

List installed packages:<br>
`zypper se -i`<br>
List files in a package:<br>
`rpm -ql [PACKAGE]`<br>
See what package/s provide a file:<br>
`zypper se --provides --match-exact [FILENAME]`<br>
See patches for a CVE that are available but not yet installed:<br>
`zypper lp --cve=[CVE]`
