___________________________________________________________________________________________________________________________________________________________

# **HPC (HIGH PERFORMANCE COMPUTING) DOCUMENTATION**

Created By: Cynthia Kamau (Cyndie)

Bioinformatics, System Admin intern at ICIPE, Kenya (International Center of Insect Physiology and Ecology).

Objective: Set Up a HPC Cluster from scratch.
___________________________________________________________________________________________________________________________________________________________


# **Creating a HPC Cluster(High Availability)**

## Pre-requisites:

- Several desktops
- USB Flash Drive / DVD
- Ethernet cables
- Switch
- CentOS

# 1. Setting Up The Desktops:

Connect all the dektops, ensure the CPUSs are okay. Connect to appropriate Ethernet ports.

# 2. Installing CentOS

[CentOS](https://www.centos.org/) is the OS used in HPC Clusters. One can run CentOS in 3 different ways:

- Dual-booting with another OS (preferably Windows for easier storage partitioning)
- Creating Virtual Machines using Virtualbox
- Booting CentOS using a drive, DVD

In my case I booted CentOS using an USB.


**_Procedure_**

- Backup any important files from the current OS
- Download CentOS 7 [here](https://centos.mirror.liquidtelecom.com/7.9.2009/isos/x86_64/) as an ISO image (raw CD image).

I downloaded the DVD ISO image for better configuration.

- Download [Belena Etcher](https://etcher.download/) to create a CentOS Bootable USB Drive. [Rufus](https://rufus.ie/en/) is also another option.
- Follow the steps in the Etcher GUI to create a Bootable USB Drive.

**N.B** The USB needs to be empty to avoid overwriting important data during flashing.

- Once done, safely eject the USB, load it to the target desktop, power it up.
- Enter into BIOS mode depending on the computer's model (for HP its F10) to install CentOS.
- Successful installation should bring this prompt:

![CentOS Installation Prompt](https://user-images.githubusercontent.com/63792575/192247516-b7a2b161-88cd-4878-8742-33661b6a3e0c.png)

- Here's where one can tweak the default installation options.

![CentOS Installation Prompt](https://user-images.githubusercontent.com/63792575/192248128-db97e1ee-69c0-4767-8430-6ddf5fbfdc79.png)

- It's also important to configure Network settings to enter desired hostname (very important as it will be used in HPC Clustering) and for networking junkies, manually configure the IP addresses.

![CentOS Installation Prompt](https://user-images.githubusercontent.com/63792575/192249752-9b65fdc0-e4ca-4748-8c0d-524acd403a2c.png)

**N.B** In the **Software** Section, for my head node I configured the **Server with GUI** as my base environment, then for my server nodes I configured the **Compute Node** setting (This is why I chose the DVD ISO image).

- Install CentOS, then reboot your machine.


# 3. Configuring CentOS
## 3.1 Verify Network 

* Login as `root` in the terminal using `su --login` command.

```
[cynhpcadmin@admin ~]$ su --login
Password: 
Last login: Sat Sep 24 16:13:27 EAT 2022 on pts/0
[root@localhost ~]# 

```

* Verify the machine's IP address using `ip a` command.

```
[root@localhost ~]# ip a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host 
       valid_lft forever preferred_lft forever
2: eno1: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether a0:b3:cc:f8:23:df brd ff:ff:ff:ff:ff:ff
    inet 10.2.13.21/24 brd 10.2.13.255 scope global noprefixroute dynamic eno1
       valid_lft 74327sec preferred_lft 74327sec
    inet6 fe80::6d07:2d47:9af6:d5a7/64 scope link noprefixroute 
       valid_lft forever preferred_lft forever
3: virbr0: <NO-CARRIER,BROADCAST,MULTICAST,UP> mtu 1500 qdisc noqueue state DOWN group default qlen 1000
    link/ether 52:54:00:84:93:96 brd ff:ff:ff:ff:ff:ff
    inet 192.168.122.1/24 brd 192.168.122.255 scope global virbr0
       valid_lft forever preferred_lft forever
4: virbr0-nic: <BROADCAST,MULTICAST> mtu 1500 qdisc fq_codel master virbr0 state DOWN group default qlen 1000
    link/ether 52:54:00:84:93:96 brd ff:ff:ff:ff:ff:ff

```

As you can see my first machine's IP address is `10.2.13.21`. This is my head node.

* To see your machine's full configuration, use the `ifconfig` command.

```
[root@localhost ~]# ifconfig
eno1: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 10.2.13.21  netmask 255.255.255.0  broadcast 10.2.13.255
        inet6 fe80::6d07:2d47:9af6:d5a7  prefixlen 64  scopeid 0x20<link>
        ether a0:b3:cc:f8:23:df  txqueuelen 1000  (Ethernet)
        RX packets 1649789  bytes 623196420 (594.3 MiB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 198939  bytes 39090798 (37.2 MiB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0
        device interrupt 20  memory 0xf7c00000-f7c20000  

lo: flags=73<UP,LOOPBACK,RUNNING>  mtu 65536
        inet 127.0.0.1  netmask 255.0.0.0
        inet6 ::1  prefixlen 128  scopeid 0x10<host>
        loop  txqueuelen 1000  (Local Loopback)
        RX packets 66084  bytes 6984938 (6.6 MiB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 66084  bytes 6984938 (6.6 MiB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

virbr0: flags=4099<UP,BROADCAST,MULTICAST>  mtu 1500
        inet 192.168.122.1  netmask 255.255.255.0  broadcast 192.168.122.255
        ether 52:54:00:84:93:96  txqueuelen 1000  (Ethernet)
        RX packets 0  bytes 0 (0.0 B)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 0  bytes 0 (0.0 B)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0


```

* Do the same for the rest of your desktops (In HPC language, nodes).

I'll use 2 extra nodes as an example.

```
[root@server1 ~]# ip a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host 
       valid_lft forever preferred_lft forever
2: eno1: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP group default qlen 1000
    link/ether 24:be:05:0c:5a:23 brd ff:ff:ff:ff:ff:ff
    inet 10.4.1.36/16 brd 10.4.255.255 scope global noprefixroute dynamic eno1
       valid_lft 70980sec preferred_lft 70980sec
    inet6 fe80::4854:1df4:7f3:c342/64 scope link noprefixroute 
       valid_lft forever preferred_lft forever

```

* My first node's IP address is `10.4.1.36`

```
[root@cynhpc ~]# ip a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host 
       valid_lft forever preferred_lft forever
2: eno1: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP group default qlen 1000
    link/ether a0:b3:cc:f8:23:9e brd ff:ff:ff:ff:ff:ff
    inet 10.2.13.29/24 brd 10.2.13.255 scope global noprefixroute dynamic eno1
       valid_lft 70685sec preferred_lft 70685sec
    inet6 fe80::ee7b:a5b4:def8:1c47/64 scope link noprefixroute 
       valid_lft forever preferred_lft forever


```

* My second node's IP address is `10.2.13.29`


* Next we can check the **IP routes** are okay:

For my Head Node:

```
[root@localhost ~]# ip route
default via 10.2.13.1 dev eno1 proto dhcp metric 100 
10.2.13.0/24 dev eno1 proto kernel scope link src 10.2.13.21 metric 100 
192.168.122.0/24 dev virbr0 proto kernel scope link src 192.168.122.1 

```

My First Node:

```
[root@server1 ~]# ip route
default via 10.4.0.1 dev eno1 proto dhcp metric 100 
10.4.0.0/16 dev eno1 proto kernel scope link src 10.4.1.36 metric 100 

```

My Second Node:

```
[root@cynhpc ~]# ip route
default via 10.2.13.1 dev eno1 proto dhcp metric 100 
10.2.13.0/24 dev eno1 proto kernel scope link src 10.2.13.29 metric 100 

```

* We then check for each node's **Hostname**, or technically speaking, **Fully Qualified Domain Name (FQDN)**

Head Node:

```
[root@localhost ~]# hostname
localhost.localdomain

```

Node 1:

```
[root@server1 ~]# hostname
localhost.localdomain

```

Node 2:

```
[root@cynhpc ~]# hostname
cynhpc

```

Well... Apparently I forgot to change the default hostnames for my two machines! Not to worry though.

Let's now start inter-connecting our 3 babies so they talk to each other!


## 3.2 Configure Communication between all Nodes

* To each of the nodes, we'll edit the host file of our system, i.e `/etc/hosts` to add each IP address and preferrable local domain name of each node.

`[root@localhost ~]# nano /etc/hosts`


```

127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4
::1         localhost localhost.localdomain localhost6 localhost6.localdomain6
#Head Node IP address and local	domain
10.2.13.21 admin.hpc.com hpcadmin
#Node1
10.4.1.36 server1.hpc.com server1
#Node2
10.2.13.29 cynhpc server2



```

* To test whether they've connected, we now `ping` all the 3 nodes.

```
[root@localhost ~]# ping -c 3 cynhpc
PING cynhpc (10.2.13.29) 56(84) bytes of data.
64 bytes from cynhpc (10.2.13.29): icmp_seq=1 ttl=64 time=0.234 ms
64 bytes from cynhpc (10.2.13.29): icmp_seq=2 ttl=64 time=0.263 ms
64 bytes from cynhpc (10.2.13.29): icmp_seq=3 ttl=64 time=0.248 ms

--- cynhpc ping statistics ---
3 packets transmitted, 3 received, 0% packet loss, time 2000ms
rtt min/avg/max/mdev = 0.234/0.248/0.263/0.017 ms

```

* Here I pinged my second node, `cynhpc`, 3 times, from my head node.


```
[root@localhost ~]# ping -c 3 server1.hpc.com
PING server1.hpc.com (10.4.1.36) 56(84) bytes of data.
64 bytes from server1.hpc.com (10.4.1.36): icmp_seq=1 ttl=63 time=0.372 ms
64 bytes from server1.hpc.com (10.4.1.36): icmp_seq=2 ttl=63 time=0.327 ms
64 bytes from server1.hpc.com (10.4.1.36): icmp_seq=3 ttl=63 time=0.362 ms

--- server1.hpc.com ping statistics ---
3 packets transmitted, 3 received, 0% packet loss, time 1999ms
rtt min/avg/max/mdev = 0.327/0.353/0.372/0.029 ms

```


* That's my first node, `server1.hpc.com`.

* I finally pinged my head node, `admin.hpc.com`.

```
[root@localhost ~]# ping -c 1 admin.hpc.com
PING admin.hpc.com (10.2.13.21) 56(84) bytes of data.
64 bytes from admin.hpc.com (10.2.13.21): icmp_seq=1 ttl=64 time=0.066 ms

--- admin.hpc.com ping statistics ---
1 packets transmitted, 1 received, 0% packet loss, time 0ms
rtt min/avg/max/mdev = 0.066/0.066/0.066/0.000 ms

```

Our 3 nodes are communicating!

Now at this point, all the back and forth between the three nodes is already exhausting. Let's configure remote login through `SSH` because I'm such a lazy person.


## 3.3 Remote Login Configuration Via SSH

* To configure remote login so as to access all the machines remotely, we use the `ssh -l` command.

* I'll now access my node1 and 2 on my head node, and login as root on each node. I'll then check the IP address of each node remotely.

Node1:

```
[root@localhost ~]# ssh -l root server1.hpc.com
root@server1.hpc.com's password: 
Last login: Mon Sep 26 15:01:41 2022 from admin.hpc.com
[root@localhost ~]# 

```

* My output is slightly different as I've been logging remotely for some time. For first time login, it should be like this:

```
[root@admin ~]# ssh -l root server1.hpc.com
The authenticity of host 'server1.hpc.com (10.4.1.36)' can't be established.
ECDSA key fingerprint is SHA256:AvmOXLnXRYeXfkKee7tS2t3u1lZ/4keq2PRB3J9QSY8.
ECDSA key fingerprint is MD5:0a:cd:eb:dd:97:87:e4:f7:3e:03:f6:0f:5c:d9:c2:56.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added 'server1.hpc.com,10.4.1.36' (ECDSA) to the list of known hosts.
root@server1.hpc.com's password: 
Last login: Sat Sep 24 16:59:51 2022
[root@localhost ~]# 

```

We can now check the IP address.

```
Last login: Mon Sep 26 15:01:41 2022 from admin.hpc.com
[root@localhost ~]# ip a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host 
       valid_lft forever preferred_lft forever
2: eno1: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP group default qlen 1000
    link/ether 24:be:05:0c:5a:23 brd ff:ff:ff:ff:ff:ff
    inet 10.4.1.36/16 brd 10.4.255.255 scope global noprefixroute dynamic eno1
       valid_lft 61856sec preferred_lft 61856sec
    inet6 fe80::4854:1df4:7f3:c342/64 scope link noprefixroute 
       valid_lft forever preferred_lft forever

```

As you can see, its `10.4.1.36`.

* To log out of Node1, just type `logout`.

```
[root@localhost ~]# logout
Connection to server1.hpc.com closed.

```
* Let's login to Node2, and check its IP address.

```
[root@localhost ~]# ssh -l root cynhpc
root@cynhpc's password: 
Last login: Mon Sep 26 13:38:55 2022 from admin.hpc.com
[root@cynhpc ~]# ip a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host 
       valid_lft forever preferred_lft forever
2: eno1: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP group default qlen 1000
    link/ether a0:b3:cc:f8:23:9e brd ff:ff:ff:ff:ff:ff
    inet 10.2.13.29/24 brd 10.2.13.255 scope global noprefixroute dynamic eno1
       valid_lft 61445sec preferred_lft 61445sec
    inet6 fe80::ee7b:a5b4:def8:1c47/64 scope link noprefixroute 
       valid_lft forever preferred_lft forever
[root@cynhpc ~]# 

```

As you can see, its `10.2.13.29`.

* You can now access your nodes as root admin on your head node, and make changes, edit files etc.


# 4. Setting Up a HPC Cluster

* To set up a cluster, the important tools we need are:

`pacemaker` :  It's responsible for all cluster-related activities, such as monitoring cluster membership, managing the services and resources, and fencing cluster members.

`corosync` : This is the framework used by Pacemaker for handling communication between the cluster nodes.

`pcs` : Provides a command-line interface to create, configure, and control every aspect of a Pacemaker/corosync cluster.

* So naturally, the first thing I did was to install Pacemaker using `yum`:

```
[root@admin ~]# yum install pcs pacemaker fence-agents-all
Loaded plugins: fastestmirror, langpacks
Loading mirror speeds from cached hostfile
 * base: mirror.aptus.co.tz
 * epel: d2lzkl7pfhq30w.cloudfront.net
 * extras: mirror.aptus.co.tz
 * updates: mirror.aptus.co.tz
Package pcs-0.9.169-3.el7.centos.1.x86_64 already installed and latest version
Package pacemaker-1.1.23-1.el7_9.1.x86_64 already installed and latest version
Package fence-agents-all-4.2.1-41.el7_9.6.x86_64 already installed and latest version
Nothing to do


```

* So all the tools we need were already installed. Great!

* The next step I made was to set up and enable `pcsd` for cluster configuration sync.

```
[root@admin ~]# systemctl start pcsd.service
[root@admin ~]# systemctl enable pcsd.service
Created symlink from /etc/systemd/system/multi-user.target.wants/pcsd.service to /usr/lib/systemd/system/pcsd.service.

```

* I then configured the user `hacluster` on each node, so we can start using `pcs` commands.


`hacluster` is the default user the cluster packages create after a successful installation.


```
[root@admin ~]# passwd hacluster
Changing password for user hacluster.
New password: 
Retype new password: 
passwd: all authentication tokens updated successfully.

```

* I then authenticated `pcs` each node using their local domain using `pcs host auth`, example:

```
[root@admin ~]# pcs host auth admin.hpc.com

Usage: pcs [-f file] [-h] [commands]...
Control and configure pacemaker and corosync.

Options:
    -h, --help         Display usage and exit.
    -f file            Perform actions on file instead of active CIB.
    --debug            Print all network traffic and external commands run.
    --version          Print pcs version information. List pcs capabilities if
                       --full is specified.
    --request-timeout  Timeout for each outgoing request to another node in
                       seconds. Default is 60s.
    --force            Override checks and errors, the exact behavior depends on
                       the command. WARNING: Using the --force option is
                       strongly discouraged unless you know what you are doing.

Commands:
    cluster     Configure cluster options and nodes.
    resource    Manage cluster resources.
    stonith     Manage fence devices.
    constraint  Manage resource constraints.
    property    Manage pacemaker properties.
    acl         Manage pacemaker access control lists.
    qdevice     Manage quorum device provider on the local host.
    quorum      Manage cluster quorum settings.
    booth       Manage booth (cluster ticket manager).
    status      View cluster status.
    config      View and manage cluster configuration.
    pcsd        Manage pcs daemon.
    node        Manage cluster nodes.
    alert       Manage pacemaker alerts.
    client      Manage pcsd client configuration.


```

We are now running on the PCS Shell.

* I now tried to create a cluster using my 3 nodes using the `pcs cluster auth` command:

```
[root@admin ~]# pcs cluster auth admin.hpc.com server1.hpc.com cynhpc
Username: hacluster
Password: 
admin.hpc.com: Authorized
Error: Unable to communicate with server1.hpc.com
Error: Unable to communicate with cynhpc

```

* I got this error on all the nodes; they only authorized their local domain, but not of the other nodes.

Example, on Node2:

```
[root@cynhpc ~]# pcs cluster auth admin.hpc.com server1.hpc.com cynhpc
Username: hacluster
Password: 
Error: Unable to communicate with admin.hpc.com
Error: Unable to communicate with server1.hpc.com
cynhpc: Authorized

```
* Meaning the nodes were still not fully communicating to form a cluster. But luckily I found a solution; I had not configured the `firewalld` settings.

* I now had to allow cluster-related services through the local firewall on each node.


```
[root@admin ~]# firewall-cmd --permanent --add-service=high-availability
success
[root@admin ~]# firewall-cmd --add-service=high-availability
success
[root@admin ~]# firewall-cmd --reload
success

```

* I then used `pcs cluster setup`on each node to generate and synchronize the corosync configuration: 

```
[root@admin ~]# pcs cluster setup --local --name hpc-cluster admin.hpc.com server1.hpc.com cynhpc
Shutting down pacemaker/corosync services...
Killing any remaining services...
Removing all cluster configuration files...
[root@admin ~]#

```

* I then restarted `Pacemaker` and `corosync` packages again:

```
[root@admin ~]# systemctl start corosync
[root@admin ~]# systemctl start pacemaker

```

* After that I re-attempted our `pcs cluster auth` command on each node.

```
[root@admin ~]# pcs cluster auth admin.hpc.com server1.hpc.com cynhpc
Username: hacluster
Password: 
admin.hpc.com: Authorized
server1.hpc.com: Authorized
cynhpc: Authorized

```

And It was a Success!!!!

* For confirmation, I ran the command `crm_mon` on all nodes, to ensure the cluster was fully connected. The output was as follows;


```
Stack: corosync
Current DC: admin.hpc.com (version 1.1.23-1.el7_9.1-9acf116022) - partition with quorum
Last updated: Mon Sep 26 20:15:22 2022
Last change: Mon Sep 26 11:05:54 2022 by hacluster via crmd on admin.hpc.com

3 nodes configured
0 resource instances configured

Online: [ admin.hpc.com cynhpc server1.hpc.com ]

No active resources

```

At last, our 3 nodes are now fully connected to form a cluster.

* To check the status of our cluster, `hpc-cluster`, I used the `pcs cluster status` command.

```
[root@localhost ~]# pcs cluster status
Cluster Status:
 Stack: corosync
 Current DC: admin.hpc.com (version 1.1.23-1.el7_9.1-9acf116022) - partition with quorum
 Last updated: Mon Sep 26 20:19:31 2022
 Last change: Mon Sep 26 11:05:54 2022 by hacluster via crmd on admin.hpc.com
 3 nodes configured
 0 resource instances configured

PCSD Status:
  admin.hpc.com: Online
  cynhpc: Online
  server1.hpc.com: Online
[root@localhost ~]# 

```

I'm still learning about `partition with quorum` , and generally Pacemaker's `quorum` context. But so far we now have a 3-node HPC Cluster!!

* To start our cluster, we'll use the `pcs cluster start --all` command.

```
[root@localhost ~]# pcs cluster start --all
admin.hpc.com: Starting Cluster (corosync)...
server1.hpc.com: Starting Cluster (corosync)...
cynhpc: Starting Cluster (corosync)...
admin.hpc.com: Starting Cluster (pacemaker)...
server1.hpc.com: Starting Cluster (pacemaker)...
cynhpc: Starting Cluster (pacemaker)...
[root@localhost ~]# 

```

* We'll then check if the configuration is still okay using the `corosync-cfgtool -s` command, on each node.

```
[root@localhost ~]# corosync-cfgtool -s
Printing ring status.
Local node ID 1
RING ID 0
	id	= 10.2.13.21
	status	= ring 0 active with no faults

```
Head node, Check.

```
[root@localhost ~]# ssh -l root server1.hpc.com
root@server1.hpc.com's password: 
Last login: Mon Sep 26 16:03:28 2022 from admin.hpc.com
[root@localhost ~]# corosync-cfgtool -s
Printing ring status.
Local node ID 2
RING ID 0
	id	= 10.4.1.36
	status	= ring 0 active with no faults
[root@localhost ~]# 

```

Node1, check.

```
[root@localhost ~]# logout
Connection to server1.hpc.com closed.
[root@localhost ~]# ssh -l root cynhpc
root@cynhpc's password: 
Last login: Mon Sep 26 16:15:52 2022 from admin.hpc.com
[root@cynhpc ~]# corosync-cfgtool -s
Printing ring status.
Local node ID 3
RING ID 0
	id	= 10.2.13.29
	status	= ring 0 active with no faults
[root@cynhpc ~]# 

```

Node2, check.

* We then check the membership configuration to ensure (for the last time!) that all the nodes have joined the cluster.
* First we type in the command `corosync-cmapctl | grep members` on the head node.

```
[root@cynhpc ~]# corosync-cmapctl | grep members
runtime.totem.pg.mrp.srp.members.1.config_version (u64) = 0
runtime.totem.pg.mrp.srp.members.1.ip (str) = r(0) ip(10.2.13.21) 
runtime.totem.pg.mrp.srp.members.1.join_count (u32) = 1
runtime.totem.pg.mrp.srp.members.1.status (str) = joined
runtime.totem.pg.mrp.srp.members.2.config_version (u64) = 0
runtime.totem.pg.mrp.srp.members.2.ip (str) = r(0) ip(10.4.1.36) 
runtime.totem.pg.mrp.srp.members.2.join_count (u32) = 1
runtime.totem.pg.mrp.srp.members.2.status (str) = joined
runtime.totem.pg.mrp.srp.members.3.config_version (u64) = 0
runtime.totem.pg.mrp.srp.members.3.ip (str) = r(0) ip(10.2.13.29) 
runtime.totem.pg.mrp.srp.members.3.join_count (u32) = 1
runtime.totem.pg.mrp.srp.members.3.status (str) = joined
[root@cynhpc ~]# 

```
As you can see, our 3 IP addresses for all the nodes are present.

* We then run the `pcs status corosync` command.

```
[root@cynhpc ~]# pcs status corosync

Membership information
----------------------
    Nodeid      Votes Name
         1          1 admin.hpc.com
         3          1 cynhpc (local)
         2          1 server1.hpc.com
[root@cynhpc ~]# 

```

At this point my brain was so fried from running commands I didn't notice I was working on Node2. But everything is well connected!. We'll stop there for now. If you've made it here so far, pat yourself on the back! You're a HPC System Admin already hehe.


## Remote Login Without Password via SSH

* To login as root to other nodes without having to be prompted for a password, we'll generate a `SSH Key` from the head node, and share it across our server nodes.

* We'll first generate a key on the head node using the `ssh-keygen` command.

```
[root@localhost ~]# ssh-keygen 
Generating public/private rsa key pair.
Enter file in which to save the key (/root/.ssh/id_rsa): 
Enter passphrase (empty for no passphrase): 
Enter same passphrase again: 
Your identification has been saved in /root/.ssh/id_rsa.
Your public key has been saved in /root/.ssh/id_rsa.pub.
The key fingerprint is:
SHA256:e5cyGrvk0eD0QDJW9Y04fsB/S311YrOOsa26kwFxV9o root@localhost.localdomain
The key's randomart image is:
+---[RSA 2048]----+
|        ...  ..  |
|       ....o.=   |
|      + .o=.+ E o|
|     . +.. + . =o|
|        S.. + + o|
|       o *.. X ..|
|        * *o= +  |
|       o *o+ .   |
|        =.o+.    |
+----[SHA256]-----+

```
* It will now generate a  directory called `.ssh`, with the public Key stored in `id_rsa.pub` and the private key on `id_rsa`.

We'll copy the whole directory to other nodes using **Secure Copy** `scp -r`.

```
[root@localhost ~]# scp -r ~/.ssh server1.hpc.com:
root@server1.hpc.com's password: 
known_hosts                                                                                                                                   100%  366   288.8KB/s   00:00    
id_rsa                                                                                                                                        100% 1675     1.3MB/s   00:00    
id_rsa.pub                                                                                                                                    100%  408   343.7KB/s   00:00    
authorized_keys                                                                                                                               100%  408   388.7KB/s   00:00    
[root@localhost ~]#

```

* To test out if we can login to Node1 without a password prompt, we'll use `ssh` .

```
[root@localhost ~]# ssh server1.hpc.com
Last login: Tue Sep 27 10:26:11 2022 from admin.hpc.com
[root@localhost ~]#

```

* It's well connected.

* We'll connect Node2 as well.


```
[root@localhost ~]# scp -r ~/.ssh cynhpc:
root@cynhpc's password: 
known_hosts                                                                                                                                   100%  366    77.9KB/s   00:00    
id_rsa                                                                                                                                        100% 1675     1.5MB/s   00:00    
id_rsa.pub                                                                                                                                    100%  408   415.6KB/s   00:00    
authorized_keys                                                                                                                               100%  408   478.7KB/s   00:00    
[root@localhost ~]# 

```

* Okay let's test it out.

```
[root@localhost ~]# ssh cynhpc
Last login: Tue Sep 27 10:34:54 2022 from admin.hpc.com
[root@cynhpc ~]# 

```

* Great! We can now login without a password prompt. Unprotected keys are however not recommended for servers exposed to outside networks.

## Configuring a Parallel Shell using `pdsh` Tool

* To configure a parallel shell to run commands across various nodes simultaneously, you configure the `pdsh` tool.

* On the head node, you first ensure `epel-release` package is present before installing `pdsh`, using the ` yum repolist` command.

```
[root@admin ~]# yum repolist
Loaded plugins: fastestmirror, langpacks
Loading mirror speeds from cached hostfile
 * base: mirror.aptus.co.tz
 * epel: d2lzkl7pfhq30w.cloudfront.net
 * extras: mirror.aptus.co.tz
 * updates: mirror.aptus.co.tz
repo id                                                               repo name                                                                                           status
base/7/x86_64                                                         CentOS-7 - Base                                                                                     10,072
epel/x86_64                                                           Extra Packages for Enterprise Linux 7 - x86_64                                                      13,752
extras/7/x86_64                                                       CentOS-7 - Extras                                                                                      516
updates/7/x86_64                                                      CentOS-7 - Updates                                                                                   4,244
repolist: 28,584

```

* If the Epel package is not present, you install it using the `yum -y install epel-release` command.

* You then install `pdsh` using the `yum install pdsh` command.

* To check the version, use the `pdsh -V` command.

```
[root@admin ~]# pdsh -V
pdsh-2.34 (+readline+debug)
rcmd modules: ssh,exec (default: ssh)
misc modules: (none)

```

* Since we had already connected our nodes to a cluster, configuring a parallel shell is easy.

* We'll use the `pdsh -w` and list the nodes based on their hostnames.

```
[root@admin ~]# pdsh -w admin.hpc.com ls -s
admin: ssh_askpass: exec(/usr/libexec/openssh/ssh-askpass): No such file or directory
admin: Host key verification failed.
pdsh@admin: admin: ssh exited with exit code 255

```

Aaand an error occurs! It seems we don't have `ssh-askpass` in our system.

* To install `ssh-askpass`, we first configure `makecache`.

```
[root@admin ~]# yum makecache
Loaded plugins: fastestmirror, langpacks
Loading mirror speeds from cached hostfile
epel/x86_64/metalink                                                                                                                                     |  65 kB  00:00:00     
 * base: mirror.aptus.co.tz
 * epel: ftp.plusline.net
 * extras: mirror.aptus.co.tz
 * updates: mirror.aptus.co.tz
base                                                                                                                                                     | 3.6 kB  00:00:00     
extras                                                                                                                                                   | 2.9 kB  00:00:00     
updates                                                                                                                                                  | 2.9 kB  00:00:00     
(1/7): epel/x86_64/filelists_db                                                                                                                          |  12 MB  00:00:01     
(2/7): base/7/x86_64/filelists_db                                                                                                                        | 7.2 MB  00:00:01     
(3/7): epel/x86_64/prestodelta                                                                                                                           |  510 B  00:00:00     
(4/7): epel/x86_64/other_db                                                                                                                              | 3.4 MB  00:00:00     
(5/7): extras/7/x86_64/filelists_db                                                                                                                      | 277 kB  00:00:00     
(6/7): updates/7/x86_64/filelists_db                                                                                                                     | 9.5 MB  00:00:01     
(7/7): base/7/x86_64/other_db                                                                                                                            | 2.6 MB  00:00:02     
Metadata Cache Created

```

* After creating a Cache, we then install `ssh-askpass` using `yum`.

```
[root@admin ~]# yum -y install openssh-askpass
Loaded plugins: fastestmirror, langpacks
Loading mirror speeds from cached hostfile
 * base: mirror.aptus.co.tz
 * epel: d2lzkl7pfhq30w.cloudfront.net
 * extras: mirror.aptus.co.tz
 * updates: mirror.aptus.co.tz
Resolving Dependencies
--> Running transaction check
---> Package openssh-askpass.x86_64 0:7.4p1-22.el7_9 will be installed
--> Finished Dependency Resolution

Dependencies Resolved

================================================================================================================================================================================
 Package                                       Arch                                 Version                                         Repository                             Size
================================================================================================================================================================================
Installing:
 openssh-askpass                               x86_64                               7.4p1-22.el7_9                                  updates                                77 k

Transaction Summary
================================================================================================================================================================================
Install  1 Package

Total download size: 77 k
Installed size: 16 k
Downloading packages:
openssh-askpass-7.4p1-22.el7_9.x86_64.rpm                                                                                                                |  77 kB  00:00:00     
Running transaction check
Running transaction test
Transaction test succeeded
Running transaction
  Installing : openssh-askpass-7.4p1-22.el7_9.x86_64                                                                                                                        1/1 
  Verifying  : openssh-askpass-7.4p1-22.el7_9.x86_64                                                                                                                        1/1 

Installed:
  openssh-askpass.x86_64 0:7.4p1-22.el7_9                                                                                                                                       

Complete!

```
* To make `pdsh` run with `ssh` enabled, you add this command to the `bashrc` file;

`export PDSH_RCMD_TYPE=ssh`

* To create the environment variable, you can either logout then login again, or run `source bashrc`.

* We can now use `pdsh` successfully.

```
[root@admin ~]# pdsh -w admin.hpc.com ls -s
admin: Warning: Permanently added 'admin.hpc.com,10.2.13.21' (ECDSA) to the list of known hosts.
admin: total 2196
admin:    4 anaconda-ks.cfg
admin:    4 automake-1.14
admin: 2184 automake-1.14.tar.gz
admin:    4 initial-setup-ks.cfg

```

* We can now `pdsh` into a node successfully. What about running a command to all nodes simultaneously?


* We'll need to create a file like `/etc/hosts`,to list all nodes' IP addresses and host names, for `pdsh` to use it.


```
[root@admin ~]# mkdir PDSH
[root@admin ~]# cd PDSH/
[root@admin PDSH]# nano hosts

```
* Inside the `hosts` file, we list all IP addresses without adding any spaces.

```
10.2.13.21 admin.hpc.com hpcadmin
10.4.1.36 server1.hpc.com server1
10.2.13.29 cynhpc server2
hosts (END)

```

* We'll then export `WCOLL`, an environment variable that points `pdsh` to the file with a list of hosts.

```
[root@admin PDSH]# export WCOLL=/root/PDSH/hosts

```

* We'll also add it to the `bashrc` file, then logout, login to create the environment variable.


* With that set, we can now run multiple shells at the same time, using `pdsh -w` command.


```
[root@admin ~]# pdsh -w admin.hpc.com,cynhpc,server1.hpc.com hostname 
admin: admin.hpc.com
cynhpc: cynhpc
server1: server1.hpc.com

```

* Here, I used `pdsh` to find the hostnames for all 3 nodes simultaneously. Our Parallel Shell is now ready to go!!


# 5. Setting Up Slurm Job Scheduler

Slurm is an open-source workload manager designed for Linux clusters of all sizes. It’s a great system for queuing jobs for your HPC applications.

The installation procedure is described below.

## 5.1 Installing MariaDB Database

MariaDB is important to store the accounting that Slurm provides. It's only installed in the head node.

`yum install mariadb-server mariadb-devel -y`

We'll finish configuring it after Slurm.

## 5.2 Configuring User and Group IDs for Slurm, Munge

Slurm and Munge require a consistent Group ID in all the nodes, which are listed in the `/etc/group` file.

To configure this;

* We open the file `/etc/group`, check the used IDs to look for a unique one for Munge and Slurm.
* It should be between 900 and 999, as 1000 is for our admin user.

```
input:x:998:
systemd-journal:x:190:
systemd-network:x:192:
dbus:x:81:
polkitd:x:997:
apache:x:48:
printadmin:x:996:
cgred:x:995:
colord:x:994:
unbound:x:993:
gluster:x:992:
libstoragemgmt:x:991:
rpc:x:32:
saned:x:990:
dip:x:40:
ssh_keys:x:989:
saslauth:x:76:
abrt:x:173:
rtkit:x:172:
pulse-access:x:988:
pulse-rt:x:987:
pulse:x:171:

```

Here's a snippet of my `/etc/group` file. I chose `980` for `munge`, `979` for `slurm`.

With that set, let's configure the GIDs.

* We first export a variable `MUNGEUSER` with the GID.

`[root@admin ~]# export MUNGEUSER=980`

* After creating the variable, we then create the `munge` group using `groupadd -g`

`[root@admin ~]# groupadd -g $MUNGEUSER munge`

* To confirm it's been successfully added, we check our `/etc/group` file.

```
tcpdump:x:72:
cynhpcadmin:x:1000:cynhpcadmin
munge:x:980:

```
* It's been successfully created.

* We then create Munge user using `useradd`

```
[root@admin ~]# useradd  -m -c "MUNGE Uid 'N' Gid Emporium" -d /var/lib/munge -u $MUNGEUSER -g munge  -s /sbin/nologin munge
useradd: invalid user ID '-g' 

```
* It might bring the error above, but after some time it works.

* Do the same in all nodes.

## 5.3 Installing Munge

* WE first ensure the latest EPEL repository is in our system.

`yum install epel-release`

* We can now install Munge.

`yum install munge munge-libs munge-devel -y`

* After installing Munge, we'll need to create a secret key on our head node, then copy it to other nodes.

* First, we install rng-tools to properly create the key.


```
yum install rng-tools -y
rngd -r /dev/urandom

```

* Now, we create the secret key. 

```
/usr/sbin/create-munge-key -r

dd if=/dev/urandom bs=1 count=1024 > /etc/munge/munge.key
chown munge: /etc/munge/munge.key
chmod 400 /etc/munge/munge.key

```

* After the secret key is created, we,ll send this key to all of the compute nodes, using `scp`

```
[root@admin ~]# scp /etc/munge/munge.key root@server1.hpc.com:/etc/munge
[root@admin ~]# scp /etc/munge/munge.key root@cynhpc:/etc/munge

```

* Now, we SSH into every node and correct the permissions as well as start the Munge service.

```
chown -R munge: /etc/munge/ /var/log/munge/
chmod 0700 /etc/munge/ /var/log/munge/

```

* To start the Munge service;

```
systemctl enable munge
systemctl start munge

```

* To test Munge, we can try to access our Node1 with Munge from Node2


```
[root@cynhpc ~]# munge -n
[root@cynhpc ~]# munge -n | unmunge
[root@cynhpc ~]# munge -n | ssh server1.hpc.com unmunge
STATUS:           Success (0)
ENCODE_HOST:      cynhpc (10.2.13.29)
ENCODE_TIME:      2022-09-29 12:49:05 +0300 (1664444945)
DECODE_TIME:      2022-09-29 12:49:05 +0300 (1664444945)
TTL:              300
CIPHER:           aes128 (4)
MAC:              sha1 (3)
ZIP:              none (0)
UID:              root (0)
GID:              root (0)
LENGTH:           0

```

## 5.4 Setting Up NFS (Network File System)

Here's how I set up my NFS to share files across the nodes;

**Setting Up The NFS Master Server**

* Install a couple of NFS libraries on the head node:

`yum install nfs-utils nfs-utils-lib -y`

* We then enable the NFS Server.

```
systemctl enable rpcbind nfs-server
systemctl start rpcbind nfs-server

```

* We create a new empty folder that will be the shared folder:

`mkdir /nfsshare`

* We then edit the `/etc/exports` file created; We add our name of the shared folder and the IP addresses that we want the shared folder to be shared,together with file permissions:

```
/nfsshare server1.hpc.com(rw,sync,no_root_squash,no_subtree_check)
/nfsshare cynhpc(rw,sync,no_root_squash,no_subtree_check)

```

* We load the `/etc/exports`  to add the new changes:

`exportfs -a`

* We then change the firewall to allow NFS and complimenting services:

```
firewall-cmd --permanent --zone=public --add-service=nfs
firewall-cmd --permanent --zone=public --add-service=mountd
firewall-cmd --permanent --zone=public --add-service=rpc-bind
firewall-cmd --reload

```

* We then restart NFS service:

`systemctl restart nfs`

**Setting up NFS Client: node1, node2**

* Install a couple of NFS libraries:

```
yum install nfs-utils nfs-utils-lib -y

```

* Make a folder where the shared folder from master will be mounted on node1, node2:

`mkdir /nfsshare`

* Make sure that the following two commands do not return any errors:

```
showmount -e admin.hpc.com
rpcinfo -p admin.hpc.com
mount admin.hpc.com:/nfsshare /nfsshare

```

* To see if `admin.hpc.com:/nfsshare mount` has been created:

`df -h`

* To test if the shared folder actually works:

`touch /nfsshare/test.txt`

* On master, if we cd /nfsshare, we will see test.txt is inside the folder.

* NFS is now successfully configured.

## 5.5 Installing Slurm

(Documentation On Slurm Installation In Progress)

## **Current Slurm Errors**
`
### 1. On the Compute Nodes

```
[root@admin ~]# pdsh -w server1.hpc.com,cynhpc systemctl status slurmd.service
server1: ● slurmd.service - Slurm node daemon
server1:    Loaded: loaded (/usr/lib/systemd/system/slurmd.service; enabled; vendor preset: disabled)
server1:    Active: failed (Result: exit-code) since Thu 2022-09-29 19:23:40 EAT; 14h ago
server1:   Process: 19064 ExecStart=/usr/sbin/slurmd -D -s $SLURMD_OPTIONS (code=exited, status=1/FAILURE)
server1:  Main PID: 19064 (code=exited, status=1/FAILURE)
server1: 
server1: Sep 29 19:23:40 server1.hpc.com systemd[1]: Started Slurm node daemon.
server1: Sep 29 19:23:40 server1.hpc.com slurmd[19064]: slurmd: error: ClusterName 1 specified more than once, latest value used
server1: Sep 29 19:23:40 server1.hpc.com slurmd[19064]: error: ClusterName 1 specified more than once, latest value used
server1: Sep 29 19:23:40 server1.hpc.com slurmd[19064]: slurmd: error: Node configuration differs from hardware: CPUs=4:4(hw) Boards=1:1(hw) SocketsPerBoard=4:1(hw) CoresPerSocket=1:2(hw) ThreadsPerCore=1:2(hw)
server1: Sep 29 19:23:40 server1.hpc.com systemd[1]: slurmd.service: main process exited, code=exited, status=1/FAILURE
server1: Sep 29 19:23:40 server1.hpc.com systemd[1]: Unit slurmd.service entered failed state.
server1: Sep 29 19:23:40 server1.hpc.com systemd[1]: slurmd.service failed.
pdsh@admin: server1: ssh exited with exit code 3
cynhpc: ● slurmd.service - Slurm node daemon
cynhpc:    Loaded: loaded (/usr/lib/systemd/system/slurmd.service; enabled; vendor preset: disabled)
cynhpc:    Active: failed (Result: exit-code) since Thu 2022-09-29 19:23:40 EAT; 14h ago
cynhpc:   Process: 10069 ExecStart=/usr/sbin/slurmd -D -s $SLURMD_OPTIONS (code=exited, status=1/FAILURE)
cynhpc:  Main PID: 10069 (code=exited, status=1/FAILURE)
cynhpc: 
cynhpc: Sep 29 19:23:40 cynhpc systemd[1]: Started Slurm node daemon.
cynhpc: Sep 29 19:23:40 cynhpc slurmd[10069]: slurmd: error: ClusterName 1 specified more than once, latest value used
cynhpc: Sep 29 19:23:40 cynhpc slurmd[10069]: error: ClusterName 1 specified more than once, latest value used
cynhpc: Sep 29 19:23:40 cynhpc slurmd[10069]: slurmd: error: Node configuration differs from hardware: CPUs=4:4(hw) Boards=1:1(hw) SocketsPerBoard=4:1(hw) CoresPerSocket=1:2(hw) ThreadsPerCore=1:2(hw)
cynhpc: Sep 29 19:23:40 cynhpc systemd[1]: slurmd.service: main process exited, code=exited, status=1/FAILURE
cynhpc: Sep 29 19:23:40 cynhpc systemd[1]: Unit slurmd.service entered failed state.
cynhpc: Sep 29 19:23:40 cynhpc systemd[1]: slurmd.service failed.
pdsh@admin: cynhpc: ssh exited with exit code 3

```

* The errors recorded in the log files:

```
[root@admin ~]# pdsh -w server1.hpc.com,cynhpc tail /var/log/slurmd.log
server1: [2022-09-29T19:23:40.406] error: Node configuration differs from hardware: CPUs=4:4(hw) Boards=1:1(hw) SocketsPerBoard=4:1(hw) CoresPerSocket=1:2(hw) ThreadsPerCore=1:2(hw)
server1: [2022-09-29T19:23:40.409] error: cgroup namespace 'freezer' not mounted. aborting
server1: [2022-09-29T19:23:40.409] error: unable to create freezer cgroup namespace
server1: [2022-09-29T19:23:40.409] error: Couldn't load specified plugin name for proctrack/cgroup: Plugin init() callback failed
server1: [2022-09-29T19:23:40.409] error: cannot create proctrack context for proctrack/cgroup
server1: [2022-09-29T19:23:40.409] error: slurmd initialization failed
cynhpc: [2022-09-29T19:23:40.398] error: Node configuration differs from hardware: CPUs=4:4(hw) Boards=1:1(hw) SocketsPerBoard=4:1(hw) CoresPerSocket=1:2(hw) ThreadsPerCore=1:2(hw)
cynhpc: [2022-09-29T19:23:40.399] error: cgroup namespace 'freezer' not mounted. aborting
cynhpc: [2022-09-29T19:23:40.399] error: unable to create freezer cgroup namespace
cynhpc: [2022-09-29T19:23:40.399] error: Couldn't load specified plugin name for proctrack/cgroup: Plugin init() callback failed
cynhpc: [2022-09-29T19:23:40.399] error: cannot create proctrack context for proctrack/cgroup
cynhpc: [2022-09-29T19:23:40.399] error: slurmd initialization failed

```

### 2. Errors on the head node

```
[root@admin slurm]# systemctl status slurmctld.service
● slurmctld.service - Slurm controller daemon
   Loaded: loaded (/usr/lib/systemd/system/slurmctld.service; enabled; vendor preset: disabled)
   Active: failed (Result: exit-code) since Fri 2022-09-30 09:31:34 EAT; 1min 29s ago
  Process: 21068 ExecStart=/usr/sbin/slurmctld -D -s $SLURMCTLD_OPTIONS (code=exited, status=1/FAILURE)
 Main PID: 21068 (code=exited, status=1/FAILURE)

Sep 30 09:31:34 admin.hpc.com slurmctld[21068]: slurmctld: error: CPUs value "4:4" is not a valid number
Sep 30 09:31:34 admin.hpc.com slurmctld[21068]: error: CPUs value "4:4" is not a valid number
Sep 30 09:31:34 admin.hpc.com slurmctld[21068]: error: Parse error in file /etc/slurm/slurm.conf line 49: " CPUs=4:4 State=UNKNOWN"
Sep 30 09:31:34 admin.hpc.com slurmctld[21068]: slurmctld: error: CPUs value "4:4" is not a valid number
Sep 30 09:31:34 admin.hpc.com slurmctld[21068]: slurmctld: error: CPUs value "4:4" is not a valid number
Sep 30 09:31:34 admin.hpc.com slurmctld[21068]: error: CPUs value "4:4" is not a valid number
Sep 30 09:31:34 admin.hpc.com systemd[1]: slurmctld.service: main process exited, code=exited, status=1/FAILURE
Sep 30 09:31:34 admin.hpc.com slurmctld[21068]: slurmctld: fatal: Unable to process configuration file
Sep 30 09:31:34 admin.hpc.com systemd[1]: Unit slurmctld.service entered failed state.
Sep 30 09:31:34 admin.hpc.com systemd[1]: slurmctld.service failed.

```
* No errors, however, were recorded on the ` /var/log/slurmctld.log` file.

* My Node1, NOde2, head node `slurm.config` file:


```
# slurm.conf file generated by configurator easy.html.
# Put this file on all nodes of your cluster.
# See the slurm.conf man page for more information.
#
ClusterName=hpc-cluster
SlurmctldHost=hpcadmin
#
#MailProg=/bin/mail
MpiDefault=none
#MpiParams=ports=#-#
ProctrackType=proctrack/cgroup
ReturnToService=1
SlurmctldPidFile=/var/run/slurmctld.pid
#SlurmctldPort=6817
SlurmdPidFile=/var/run/slurmd.pid
#SlurmdPort=6818
SlurmdSpoolDir=/var/spool/slurmd
SlurmUser=slurm
#SlurmdUser=root
StateSaveLocation=/var/spool/slurmctld
SwitchType=switch/none
TaskPlugin=task/affinity
#
#
# TIMERS
#KillWait=30
#MinJobAge=300
#SlurmctldTimeout=120
#SlurmdTimeout=300
#
#
# SCHEDULING
SchedulerType=sched/backfill
SelectType=select/cons_tres
SelectTypeParameters=CR_Core
#
#
# LOGGING AND ACCOUNTING
AccountingStorageType=accounting_storage/none
#JobAcctGatherFrequency=30
JobAcctGatherType=jobacct_gather/none
#SlurmctldDebug=info
SlurmctldLogFile=/var/log/slurmctld.log
#SlurmdDebug=info
SlurmdLogFile=/var/log/slurmd.log
#
#
# COMPUTE NODES
NodeName=admin,server1,cynhpc NodeAddr=10.2.13.21,10.4.1.36,10.2.13.29 CPUs=12 CPUs=12 Boards=3 SocketsPerBoard=1 CoresPerSocket=2 ThreadsPerCore=2 RealMemory=10983 State=UNKN$
PartitionName=debug Nodes=ALL Default=YES MaxTime=INFINITE State=UP

```

* Node1,2 head node `cgroup.config` file:


```
###
#
# Slurm cgroup support configuration file
#
# See man slurm.conf and man cgroup.conf for further
# information on cgroup configuration parameters
#--
CgroupAutomount=yes
ConstrainCores=yes
ConstrainRAMSpace=yes
ConstrainSwapSpace=yes
ConstrainDevices=yes
AllowedRamSpace=100
AllowedSwapSpace=0
MaxRAMPercent=100
MaxSwapPercent=100
MinRAMSpace=30
ConstrainKmemSpace=no        #avoid known Kernel issues
CgroupMountpoint=/sys/fs/cgroup
CgroupPlugin=autodetect
IgnoreSystemd=no
IgnoreSystemdOnFailure=yes

```

* Screenshot of the Web Interface used to create the `slurm.config` file
 
![Screenshot from 2022-09-30 13-07-25](https://user-images.githubusercontent.com/63792575/193247811-9cf13a95-8cfe-4466-a37f-0af0790866bf.png)


* Screenshot of the `slurm.config` file generated

![Screenshot from 2022-09-30 13-10-54](https://user-images.githubusercontent.com/63792575/193248102-47854ea3-4602-4842-bd57-814592b921ab.png)


