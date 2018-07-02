---
published: false
---
## Step of prepare you k8s hosts

### Disable swap 

```
[kubernetes/preinstall : Stop if swap enabled] *******************************************************************************************************************************************************************************************
Monday 02 July 2018  17:28:47 +0300 (0:00:00.054)       0:01:12.983 *********** 
fatal: [node2]: FAILED! => {
    "assertion": "ansible_swaptotal_mb == 0", 
    "changed": false, 
    "evaluated_to": false
}
fatal: [node1]: FAILED! => {
    "assertion": "ansible_swaptotal_mb == 0", 
    "changed": false, 
    "evaluated_to": false
```
Check on remote host exactly you swap enabled:
```
root@node1:~# swapon -s
Filename				Type		Size	Used	Priority
/swap    file    	524284	0	-1
```
To disable yur swap
```
root@node1:~# swapoff -a
```

TODO: why need disable swap and how you can ignore this options.
