Check the **CPU** usage of the 5 most CPU-intensive processes:<br>
`ps -eo %cpu,comm,pid,user,etime --sort=-%cpu | head -6`

Check the **memory** usage of the 5 most memory-hungry processes:<br>
`ps -eo %mem,comm,pid,user,etime --sort=-%mem | head -6`

List all partitions:<br>
`df -h -x tmpfs -x devtmpfs`<br>
List only local partitions:<br>
`df -hl -x tmpfs -x devtmpfs`

Recursively find the 20 largest **files** in /dir/ larger than 1 gibibyte:<br>
`find /dir/ -type f -size +1G -exec du -sh "{}" \; | sort -hr | head -20`<br>
For files smaller than 1 gibibyte: replace `+1G` with `-1G`<br>
For files larger than 500 mebibytes: replace `+1G` with `+500M`

Find the 10 largest **directories** in /dir/:<br>
`du -shx /dir/* | sort -hr | head -10`<br>
Find the 10 largest directories in **/**:<br>
`du -hx -d 1 / | sort -hr | head -11 | tail -10`

Find the 10 most recently modified files and directories in /dir/:<br>
`stat -c "%y %n" /dir/* | sort -hr | head -10`<br>
**Recursively** find the 10 most recently modified files in /dir/ that have been modified in the last 24 hours:<br>
`find /dir/ -type f -mtime -1 -exec stat --format '%Y :%y %n' "{}" \; | sort -nr | cut -d ":" -f 2- | head -10`

Check what processes are listening on what ports:<br>
`ss -tupln`<br>
Check what Docker containers are listening on what ports:<br>
`docker ps --format "table {{.Names}}\t{{.Ports}}"`<br>
Check what Podman containers are listening on what ports:<br>
`podman ps --format "table {{.Names}}\t{{.Ports}}"`

List all virtual machines managed by `virt-manager`:<br>
`virsh list --all`<br>
List a virtual machine's allocated CPU's:<br>
`virsh vcpucount [VM-NAME]`<br>
List a virtual machine's allocated memory:<br>
`virsh memtune [VM-NAME]`<br>
List the mount points of a virtual machine's drives:<br>
`virsh domblklist [VM-NAME]`<br>
Start, stop, or reboot a virtual machine:<br>
`virsh start [VM-NAME]`<br>
`virsh shutdown [VM-NAME]`<br>
`virsh reboot [VM-NAME]`

Record to the terminal the output of one or more commands every 5 seconds:<br>
`while sleep 5; do [ONE OR MORE COMMANDS SEPARATED BY SEMICOLONS]; echo; done`<br>
**Stop it with [Ctrl + C]**<br>
Record in the background to a file the time (with time zone) and output of one or more commands every 5 seconds, and save the recording command's PID:<br>
`while sleep 5; do date +"%H:%M:%S %:z"; [COMMAND 1]; [COMMAND 2]; echo; done >> record.log & echo $! > recorder.pid`<br>
Monitor updates to that file:<br>
`tail -f record.log`<br>
Kill the recording command:<br>
`kill $(cat recorder.pid) && rm recorder.pid`<br>
Remember to kill it when you don't need it anymore.

Completely clear your bash history:<br>
`cat /dev/null > ~/.bash_history && history -c`<br>
You might want to do this when you have sensitive information like passwords in your bash history.
