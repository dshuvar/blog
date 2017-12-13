---
published: true
tag: work
tags: work
categories: work
---
Last days I saw strange mistake when tryed up nodes by vagrant use kubespray.
After rethinking what that means I understud, it means need symlink between files.
it so stupid.

problem:

```
$ vagrant.exe up
There was an error loading a Vagrantfile. The file being loaded
and the error message are shown below. This is usually caused by
a syntax error.

Path: D:/git/kubespray/Vagrantfile
Line number: 0
Message: Errno::EACCES: Permission denied @ rb_file_s_symlink - (D:/git/kubespray/inventory, D:/git/kubespray/.vagrant/provisioners/ansible/inventory)

```

solution: 

`ln -s inventory .vagrant/provisioners/ansible/inventory`

```¯\_(ツ)_/¯```
