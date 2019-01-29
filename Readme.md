#   TP4 - Reseau
## I. Mise en place du lab

### 1. Création des réseaux
### 2. Création des VMs

Configuration VM Client:

```
[root@vm1 ~]# hostname
vm1.tp1.b1
[root@vm1 ~]# ip route
10.1.0.0/24 dev enp0s3 proto kernel scope link src 10.1.0.10 metric 100
```

Configuration VM Serveur:

```
[root@vm2 ~]# hostname
vm2.tp3.b1
[root@vm2 ~]# ip route
10.2.0.0/24 dev enp0s3 proto kernel scope link src 10.2.0.10 metric 100
```

Configuration VM Routeur:

```
[root@vm3 ~]# hostname
vm3.tp1.b1
[root@vm3 ~]# ip route
10.1.0.0/24 dev enp0s3 proto kernel scope link src 10.1.0.254 metric 100
10.2.0.0/24 dev enp0s8 proto kernel scope link src 10.2.0.254 metric 101
```

* Definition IP statique :
VM1 :
	

		[root@vm1 network-scripts]# pwd
		/etc/sysconfig/network-scripts
		[root@vm1 network-scripts]# cat ifcfg-enp0s3
		NAME=enp0s3
		DEVICE=enp0s3

		BOOTPROTO=static
		ONBOOT=yes


		IPADDR=10.1.0.10
		NETMASK=255.255.255.0
				

		- VM2 : 
		
		[root@vm2 network-scripts]# pwd
		/etc/sysconfig/network-scripts
		[root@vm2 network-scripts]# cat ifcfg-enp0s3
		NAME=enp0s3
		DEVICE=enp0s3

		BOOTPROTO=static
		ONBOOT=yes

		IPADDR=10.2.0.10
		NETMASK=255.255.255.0
		

		- VM3 :

			[root@vm3 network-scripts]# pwd
		/etc/sysconfig/network-scripts
		[root@vm3 network-scripts]# cat ifcfg-enp0s3
		NAME=enp0s3
		DEVICE=enp0s3

		BOOTPROTO=static
		ONBOOT=yes

		IPADDR=10.1.0.254
		NETMASK=255.255.255.0
		[root@vm3 network-scripts]# cat ifcfg-enp0s8
		NAME=enp0s8
		DEVICE=enp0s8

		BOOTPROTO=static
		ONBOOT=yes

		IPADDR=10.2.0.254
		NETMASK=255.255.255.0


* `client1` ping `router1.tp4` sur l'IP `10.1.0.254` :
	
	```
	[root@vm1 network-scripts]# ping vm3.tp3.b1
	PING vm3.tp3.b1 (10.1.0.254) 56(84) bytes of data.
	64 bytes from vm3.tp3.b1 (10.1.0.254): icmp_seq=1 ttl=64 time=0.521 ms
	64 bytes from vm3.tp3.b1 (10.1.0.254): icmp_seq=2 ttl=64 time=0.445 ms
	64 bytes from vm3.tp3.b1 (10.1.0.254): icmp_seq=3 ttl=64 time=0.311 ms
	^C
	--- vm3.tp3.b1 ping statistics ---
	3 packets transmitted, 3 received, 0% packet loss, time 2000ms
	rtt min/avg/max/mdev = 0.311/0.425/0.521/0.090 ms
	```

* `server1` ping `router1.tp4` sur l'IP `10.2.0.254`
	
	```
	[root@vm2 network-scripts]# ping vm3.tp3.b1
	PING vm3.tp3.b1 (10.2.0.254) 56(84) bytes of data.
	64 bytes from vm3.tp3.b1 (10.2.0.254): icmp_seq=1 ttl=64 time=0.556 ms
	64 bytes from vm3.tp3.b1 (10.2.0.254): icmp_seq=2 ttl=64 time=0.441 ms
	64 bytes from vm3.tp3.b1 (10.2.0.254): icmp_seq=3 ttl=64 time=0.420 ms
	64 bytes from vm3.tp3.b1 (10.2.0.254): icmp_seq=4 ttl=64 time=0.453 ms
	64 bytes from vm3.tp3.b1 (10.2.0.254): icmp_seq=5 ttl=64 time=0.464 ms
	64 bytes from vm3.tp3.b1 (10.2.0.254): icmp_seq=6 ttl=64 time=0.411 ms
	^C
	--- vm3.tp3.b1 ping statistics ---
	6 packets transmitted, 6 received, 0% packet loss, time 5017ms
	rtt min/avg/max/mdev = 0.411/0.457/0.556/0.052 ms
	```

### 3. Mise en place du routage statique

* sur routeur 1 :
```
[root@vm3 network-scripts]# sudo sysctl -w net.ipv4.conf.all.forwarding=1
net.ipv4.conf.all.forwarding = 1
[root@vm3 network-scripts]# sudo systemctl stop firewalld
[root@vm3 network-scripts]# ip route show
10.1.0.0/24 dev enp0s3 proto kernel scope link src 10.1.0.254 metric 100
10.2.0.0/24 dev enp0s8 proto kernel scope link src 10.2.0.254 metric 101
```

*	sur client 1: 
```
PS C:\Users\Gabin> ssh root@10.1.0.10
root@10.1.0.10's password:
Last login: Tue Jan 29 14:44:33 2019 from 10.1.0.1
[root@vm1 ~]# ip route
10.1.0.0/24 dev enp0s3 proto kernel scope link src 10.1.0.10 metric 100
10.2.0.0/24 via 10.1.0.254 dev enp0s3 proto static metric 100
```

* sur server 1 : 
```
PS C:\Users\Gabin> ssh root@10.2.0.10
root@10.2.0.10's password:
Last login: Tue Jan 29 14:45:07 2019 from 10.2.0.1
[root@vm2 ~]# ip route
10.2.0.0/24 dev enp0s3 proto kernel scope link src 10.2.0.10 metric 100
10.2.0.0/24 via 10.2.0.254 dev enp0s3 proto static metric 100
```

* test :
	- VM1 ( client ) -> VM2 ( serveur )
``

```
	- VM2  ( serveur ) -> VM1 ( client )

	
<!--stackedit_data:
eyJoaXN0b3J5IjpbMTEzMjUyNzkwNywyOTM2NTgwMDUsLTEzMj
U1MjAxMzgsLTE5ODU4NDE5MiwxMzU0NzYzMTI3LDIwMzQ0MDIz
MDMsLTIwMTM1Nzc5NzJdfQ==
-->