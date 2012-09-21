lxctools
========

Simple helper scripts for managing lxc. Only tested with Ubuntu 12.04 LTS.

As default (for my dev env) each container will get its own separate mounted /opt (under host's /opt/lxc/\<container name\>) and /mnt/dbext4 (/mnt/dbext4/lxc/\<container name\>) and /mnt/dbxfs (/mnt/dbxfs/lxc/\<container name\>).
Containers will be named as 'name-seqno'.
Paths/defaults can be changed by editing etc/config. There are no shared disks between the containers.

Install:
<pre>
$ export PATH=(path to lxctools/bin):$PATH
</pre>

Create 3 containers for web use
-------------------------------
<pre>
$ create-lxc 1 3 web
start=1, stop=3, template=ubuntu, name=web

No config file specified, using the default config
debootstrap is /usr/sbin/debootstrap
Checking cache download in /var/cache/lxc/precise/rootfs-amd64 ...
Copy /var/cache/lxc/precise/rootfs-amd64 to /var/lib/lxc/web-1/rootfs ...
Copying rootfs to /var/lib/lxc/web-1/rootfs ...

##
# The default user is 'ubuntu' with password 'ubuntu'!
# Use the 'sudo' command to run tasks as root in the container.
##

'ubuntu' template installed
'web-1' created
Created container web-1
Disabled sudo password prompt
...
Mount host's devices (see etc/config) ? [Y/n]: y
/opt/lxc/web-1  /var/lib/lxc/web-1/rootfs/opt   none    bind    0       0
/mnt/dbext4/lxc/web-1 /var/lib/lxc/web-1/rootfs/mnt/dbext4      none    bind    0       0
/mnt/dbxfs/lxc/web-1 /var/lib/lxc/web-1/rootfs/mnt/dbxfs        none    bind    0       0
fstab updated for web-1
...
Use static IP ranges? 10.0.3.10, 10.0.3.20 ... [Y/n]: y
start=1, stop=3, name=web, incr=10
Start from 10.0.3.N [0 + 10]:
lxc.network.ipv4 = 10.0.3.10
lxc.network.ipv4 = 10.0.3.20
lxc.network.ipv4 = 10.0.3.30
Set pub ssh key [Y/n]: y
...
Added pub ssh key web-3
</pre>

Start 3 containers, web-1, web-2 and web-3
------------------------------------------
<pre>
$ node-x 1 3 web
start=1, stop=3, name=web
Starting container web-1
Starting container web-2
Starting container web-3
</pre>

Start only web container 2
--------------------------
<pre>
$ node-x 2 web
start=2, stop=2, name=web
Starting container web-2
</pre>

Logon to web container 2
-------------------------
<pre>
$ console-x 2 web
or
$ ssh ubuntu@10.0.3.20
</pre>

Kill web container 3
---------------------
<pre>
$ kill-x 3 web
start=3, stop=3, name=web
Killing container web-3
</pre>

Shutdown all 3 web containers nicely
------------------------------------
<pre>
$ shutdown-x 1 3 web
start=1, stop=3, name=web, -w
Shutdown container web-1
Container web-1 has shut down
Shutdown container web-2
Container web-2 has shut down
Shutdown container web-3
Container web-3 has shut down
</pre>

Destroy all 3 web containers. Purges /var/lib/lxc/\<container name\>, /opt/lxc/\<container name\> and /mnt/{dbext4,dbxfs}/lxc/\<container name\>
-----------------------------
<pre>
$ destroy-x 1 3 web
start=1, stop=3, name=web
Destroying container web-1
Destroying container web-2
Destroying container web-3
</pre>

Limit cpu and memory allocations (cgroup)
-----------------------------------------
<pre>
$ cgroup-x start 3 name web subsystem cpuset.cpus
start=3, stop=3, name=web, subsystem=cpuset.cpus, value=
Getting cpuset.cpus for web-3
0-11

$ cgroup-x start 3 name web subsystem cpuset.cpus value 4
start=3, stop=3, name=web, subsystem=cpuset.cpus, value=4
Setting cpuset.cpus for web-3

$ cgroup-x start 3 name web subsystem cpuset.cpus
start=3, stop=3, name=web, subsystem=cpuset.cpus, value=
Getting cpuset.cpus for web-3
4

$ cgroup-x start 3 name web subsystem memory.limit_in_bytes
start=3, stop=3, name=web, subsystem=memory.limit_in_bytes, value=
Getting memory.limit_in_bytes for web-3
9223372036854775807
</pre>
