#   TP4 - Reseau
## I. Mise en place du lab

### 1. Création des réseaux
#### 2. Création des VMs

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
		- VM1 :
		```

		[root@vm1 network-scripts]# pwd
		/etc/sysconfig/network-scripts
		[root@vm1 network-scripts]# cat ifcfg-enp0s3
		NAME=enp0s3
		DEVICE=enp0s3

		BOOTPROTO=static
		ONBOOT=yes


		IPADDR=10.1.0.10
		NETMASK=255.255.255.0
		```		

		- VM2 : 
		``
		ijiji
		``



		- VM3 :
		```
		ijij
		```
<!--stackedit_data:
eyJoaXN0b3J5IjpbMTMwNDk0MjM1MCwtMjAxMzU3Nzk3Ml19
-->