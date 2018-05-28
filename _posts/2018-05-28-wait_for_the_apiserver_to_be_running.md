---
published: false
---
## Something wrong for kubespray installations:

```
RUNNING HANDLER [kubernetes/master : Master | wait for the apiserver to be running] ***********************************************************************************************************************************************************
Monday 28 May 2018  18:00:59 +0300 (0:00:00.350)       0:02:29.380 ************ 
FAILED - RETRYING: Master | wait for the apiserver to be running (30 retries left).
```

You can see this message in syslog on one of your node:
```
failed to run Kubelet: Running with swap on is not supported, please disable swap!
```

It means you forgote about swap.

Disable swap you can by command:

```
swapoff -a
```

It can reduce time for debug. good luck!