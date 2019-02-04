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
```
[root@vm1 ~]# ping vm2.tp3.b1
PING vm2.tp3.b1 (10.2.0.10) 56(84) bytes of data.
64 bytes from vm2.tp3.b1 (10.2.0.10): icmp_seq=1 ttl=63 time=0.595 ms
64 bytes from vm2.tp3.b1 (10.2.0.10): icmp_seq=2 ttl=63 time=0.735 ms
64 bytes from vm2.tp3.b1 (10.2.0.10): icmp_seq=3 ttl=63 time=0.910 ms
64 bytes from vm2.tp3.b1 (10.2.0.10): icmp_seq=4 ttl=63 time=0.738 ms
^C
--- vm2.tp3.b1 ping statistics ---
4 packets transmitted, 4 received, 0% packet loss, time 3003ms
rtt min/avg/max/mdev = 0.595/0.744/0.910/0.114 ms
```

- VM2  ( serveur ) -> VM1 ( client )

```
[root@vm2 ~]# ping vm1.tp3.b1
PING vm1.tp3.b1 (10.1.0.10) 56(84) bytes of data.
64 bytes from vm1.tp3.b1 (10.1.0.10): icmp_seq=1 ttl=63 time=1.05 ms
64 bytes from vm1.tp3.b1 (10.1.0.10): icmp_seq=2 ttl=63 time=0.735 ms
64 bytes from vm1.tp3.b1 (10.1.0.10): icmp_seq=3 ttl=63 time=0.764 ms
^C
--- vm1.tp3.b1 ping statistics ---
3 packets transmitted, 3 received, 0% packet loss, time 2011ms
rtt min/avg/max/mdev = 0.735/0.852/1.057/0.145 ms
```

- Traceroute ( client ) :
```
[root@vm1 ~]# traceroute vm2.tp3.b1
traceroute to vm2.tp3.b1 (10.2.0.10), 30 hops max, 60 byte packets
 1  vm3.tp3.b1 (10.1.0.254)  0.314 ms  0.197 ms  0.172 ms
 2  vm2.tp3.b1 (10.2.0.10)  0.654 ms !X  0.555 ms !X  0.702 ms !X
```

## II -  Spéléologie réseau

### 1. ARP
#### **A. Manip 1**

* sur client1:
```
[root@vm1 ~]# sudo ip neigh flush all
[root@vm1 ~]# ip neigh show
10.1.0.1 dev enp0s3 lladdr 0a:00:27:00:00:15 REACHABLE
```
* sur serveur1:
```
[root@vm2 ~]# sudo ip neigh flush all
[root@vm2 ~]# ip neigh show
10.2.0.1 dev enp0s3 lladdr 0a:00:27:00:00:0f REACHABLE
```
* sur client1:
```
[root@vm1 ~]# ping vm2.tp3.b1
PING vm2.tp3.b1 (10.2.0.10) 56(84) bytes of data.
64 bytes from vm2.tp3.b1 (10.2.0.10): icmp_seq=1 ttl=63 time=0.791 ms
64 bytes from vm2.tp3.b1 (10.2.0.10): icmp_seq=2 ttl=63 time=0.630 ms
64 bytes from vm2.tp3.b1 (10.2.0.10): icmp_seq=3 ttl=63 time=0.633 ms
^C
--- vm2.tp3.b1 ping statistics ---
3 packets transmitted, 3 received, 0% packet loss, time 2001ms
rtt min/avg/max/mdev = 0.630/0.684/0.791/0.081 ms
[root@vm1 ~]# ip neigh show
10.1.0.1 dev enp0s3 lladdr 0a:00:27:00:00:15 REACHABLE
10.1.0.254 dev enp0s3 lladdr 08:00:27:dc:b3:e0 REACHABLE
```
- Explication :

* sur routeur1:
```
[root@vm3 network-scripts]# sudo ip neigh flush all
[root@vm3 network-scripts]# ip neigh show
10.1.0.1 dev enp0s3 lladdr 0a:00:27:00:00:15 REACHABLE
[root@vm3 network-scripts]# ip neigh show
10.2.0.10 dev enp0s8 lladdr 08:00:27:4b:7d:5b STALE
10.1.0.1 dev enp0s3 lladdr 0a:00:27:00:00:15 DELAY
10.1.0.10 dev enp0s3 lladdr 08:00:27:98:66:3b STALE
```
 * afficher la table ARP
```
[root@vm1 network-scripts]# ip neigh show
10.1.0.254 dev enp0s3 lladdr 08:00:27:88:74:c4 DELAY
10.1.0.1 dev enp0s3 lladdr 0a:00:27:00:00:16 DELAY
```
 Cela permet d'enregistrer l'ip et la mac pendant environ 60 secondes. (En l’occurrence c'est l'ip/mac du Routeur)
 
**Sur  `server1`:**
* afficher la table ARP
```
[root@vm2 ~]# ip neigh show
10.2.0.254 dev enp0s3 lladdr 08:00:27:41:1a:1b STALE
10.2.0.1 dev enp0s3 lladdr 0a:00:27:00:00:0d REACHABLE
```
 Permet d'enregistre l'ip et l'adresse mac, pendant anviron 60 secondes parce que le client à fait un ping.
---------------------------------------------


### **B. Manip 2**

**Sur  `router1`:**
* afficher la table ARP
```
[root@vm3 ~]# ip neigh show
10.1.0.1 dev enp0s3 lladdr 0a:00:27:00:00:16 DELAY
```
C'est l'adresse de broadcast

 **Sur  `client1`:**
   * ping  `server1`
```
[root@vm1 network-scripts]# ping vm2.tp4
PING vm2.tp4 (10.2.0.10) 56(84) bytes of data.
64 bytes from vm2.tp4 (10.2.0.10): icmp_seq=1 ttl=63 time=1.18 ms
64 bytes from vm2.tp4 (10.2.0.10): icmp_seq=2 ttl=63 time=0.732 ms
64 bytes from vm2.tp4 (10.2.0.10): icmp_seq=3 ttl=63 time=0.739 ms
```

**Sur  `router1`:**
  * afficher la table ARP
```
[root@vm3 ~]# ip neigh show
10.1.0.10 dev enp0s3 lladdr 08:00:27:06:2e:31 STALE
10.2.0.10 dev enp0s8 lladdr 08:00:27:94:ae:9d STALE
10.1.0.1 dev enp0s3 lladdr 0a:00:27:00:00:16 REACHABLE
```
Deux adresses sont ajouté, la première est l'ip du client qui a envoyé un ping. La deuxième c'est l'ip du serveur qui à renvoyé un pong.

### **C. Manip 3**

**Sur l'hôte (votre PC)**
 * Afficher la table ARP:
```
PS C:\Users\Gabin> arp -a

Interface : 192.168.102.2 --- 0xc
  Adresse Internet      Adresse physique      Type
  192.168.102.255       ff-ff-ff-ff-ff-ff     statique
  224.0.0.22            01-00-5e-00-00-16     statique
  224.0.0.251           01-00-5e-00-00-fb     statique
  224.0.0.252           01-00-5e-00-00-fc     statique
  239.255.255.250       01-00-5e-7f-ff-fa     statique
  255.255.255.255       ff-ff-ff-ff-ff-ff     statique

Interface : 10.2.0.1 --- 0xd
  Adresse Internet      Adresse physique      Type
  10.2.0.10             08-00-27-94-ae-9d     dynamique
  10.2.0.255            ff-ff-ff-ff-ff-ff     statique
  224.0.0.22            01-00-5e-00-00-16     statique
  224.0.0.251           01-00-5e-00-00-fb     statique
  224.0.0.252           01-00-5e-00-00-fc     statique
  239.255.255.250       01-00-5e-7f-ff-fa     statique

Interface : 10.1.0.1 --- 0x16
  Adresse Internet      Adresse physique      Type
  10.1.0.10             08-00-27-06-2e-31     dynamique
  10.1.0.254            08-00-27-88-74-c4     dynamique
  10.1.0.255            ff-ff-ff-ff-ff-ff     statique
  224.0.0.22            01-00-5e-00-00-16     statique
  224.0.0.251           01-00-5e-00-00-fb     statique
  224.0.0.252           01-00-5e-00-00-fc     statique
  239.255.255.250       01-00-5e-7f-ff-fa     statique

Interface : 10.33.2.105 --- 0x17
  Adresse Internet      Adresse physique      Type
  10.33.0.14            7c-76-35-51-ad-8d     dynamique
  10.33.0.68            90-cd-b6-64-bc-e7     dynamique
  10.33.2.41            08-d4-0c-c7-20-04     dynamique
  10.33.3.253           00-12-00-40-4c-bf     dynamique
  10.33.3.254           94-0c-6d-84-50-c8     dynamique
  10.33.3.255           ff-ff-ff-ff-ff-ff     statique
  224.0.0.22            01-00-5e-00-00-16     statique
  224.0.0.251           01-00-5e-00-00-fb     statique
  224.0.0.252           01-00-5e-00-00-fc     statique
  239.255.255.250       01-00-5e-7f-ff-fa     statique
  255.255.255.255       ff-ff-ff-ff-ff-ff     statique
```


  * Afficher de nouveau la table ARP:
```
Interface : 192.168.102.2 --- 0xc
  Adresse Internet      Adresse physique      Type
  224.0.0.22            01-00-5e-00-00-16     statique

Interface : 10.2.0.1 --- 0xd
  Adresse Internet      Adresse physique      Type
  224.0.0.22            01-00-5e-00-00-16     statique

Interface : 10.1.0.1 --- 0x16
  Adresse Internet      Adresse physique      Type
  224.0.0.22            01-00-5e-00-00-16     statique

Interface : 10.33.2.105 --- 0x17
  Adresse Internet      Adresse physique      Type
  10.33.3.253           00-12-00-40-4c-bf     dynamique
  224.0.0.22            01-00-5e-00-00-16     statique
```



 * Afficher encore la table ARP:
```
PS C:\WINDOWS\system32> arp -a

Interface : 192.168.102.2 --- 0xc
  Adresse Internet      Adresse physique      Type
  224.0.0.22            01-00-5e-00-00-16     statique

Interface : 10.2.0.1 --- 0xd
  Adresse Internet      Adresse physique      Type
  224.0.0.22            01-00-5e-00-00-16     statique

Interface : 10.1.0.1 --- 0x16
  Adresse Internet      Adresse physique      Type
  224.0.0.22            01-00-5e-00-00-16     statique

Interface : 10.33.2.105 --- 0x17
  Adresse Internet      Adresse physique      Type
  10.33.0.68            90-cd-b6-64-bc-e7     dynamique
  10.33.2.41            08-d4-0c-c7-20-04     dynamique
  10.33.3.253           00-12-00-40-4c-bf     dynamique
  224.0.0.22            01-00-5e-00-00-16     statique
```
  * Expliquer le(s) changement(s):
  Des nouvelles connexions enregistrées quelques secondes.
  
### **D. Manip 4**

**Sur  `client1`:**

* afficher la table ARP
```
[root@vm1 network-scripts]# ip neigh show
10.1.0.1 dev enp0s3 lladdr 0a:00:27:00:00:16 REACHABLE
```

* activer la carte NAT
```
3: enp0s10: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP group default qlen 1000
    link/ether 08:00:27:20:37:a9 brd ff:ff:ff:ff:ff:ff
    inet 10.0.5.15/24 brd 10.0.5.255 scope global noprefixroute dynamic enp0s10
       valid_lft 86117sec preferred_lft 86117sec
    inet6 fe80::6baa:de39:c1ad:6865/64 scope link noprefixroute
       valid_lft forever preferred_lft forever
```

* joindre internet (`curl google.com`  par exemple)
```
[root@vm1 ~]# curl google.com
<HTML><HEAD><meta http-equiv="content-type" content="text/html;charset=utf-8">
<TITLE>301 Moved</TITLE></HEAD><BODY>
<H1>301 Moved</H1>
The document has moved
<A HREF="http://www.google.com/">here</A>.
</BODY></HTML>
```
* afficher la table ARP
```
[root@vm1 ~]# ip neigh show
10.0.5.2 dev enp0s10 lladdr 52:54:00:12:35:02 STALE
10.1.0.1 dev enp0s3 lladdr 0a:00:27:00:00:16 REACHABLE
```


## 2. Wireshark
## A. Interception d'ARP et  `ping`
1. sur  `router1`:
* lancer Wireshark pour enregistrer le trafic
```
[root@vm3 /]# sudo tcpdump -i enp0s3 -w ping.pcap
tcpdump: listening on enp0s3, link-type EN10MB (Ethernet), capture size 262144 bytes
```
2. sur  `client1`:
* Vider la table ARP
```
[root@vm1 ~]# sudo ip neigh flush all
```
* Envoyer 4 pings à  `server1`:
```
[root@vm1 ~]# ping -c 4 vm2.tp4
PING vm2.tp4 (10.2.0.10) 56(84) bytes of data.
64 bytes from vm2.tp4 (10.2.0.10): icmp_seq=1 ttl=63 time=1.15 ms
64 bytes from vm2.tp4 (10.2.0.10): icmp_seq=2 ttl=63 time=0.696 ms
64 bytes from vm2.tp4 (10.2.0.10): icmp_seq=3 ttl=63 time=0.687 ms
64 bytes from vm2.tp4 (10.2.0.10): icmp_seq=4 ttl=63 time=0.687 ms

--- vm2.tp4 ping statistics ---
4 packets transmitted, 4 received, 0% packet loss, time 3003ms
rtt min/avg/max/mdev = 0.687/0.806/1.155/0.202 ms
```
3. Sur `router1`:
* Vérification de la présence du fichier  `ping.pcap`  avec un  `ls`:
```
[root@vm3 /]# ls
bin  boot  dev  etc  home  lib  lib64  media  mnt  opt ** ping.pcap **  proc  root  run  sbin  srv  sys  tmp  usr  var
```
4. sur l'hôte (votre PC) :

* Ouvrir le fichier  `ping.pcap`  dans Wireshark:

 <img src="./Capture_ping_wirechark.PNG">

## B. Interception d'une communication  `netcat`

1. s'occuper du firewall sur le serveur1:
```
[root@vm2 ~]# firewall-cmd --add-port=5454/tcp --permanent
success
```
2. Videz les tables ARP de tout le monde:
* Serveur1:
```
[root@vm1 ~]# sudo ip neigh flush all
[root@vm1 ~]# ip neigh show
10.1.0.1 dev enp0s3 lladdr 0a:00:27:00:00:16 DELAY
[root@vm1 ~]#
```
* Client1:
```
[root@vm2 ~]# sudo ip neigh flush all
[root@vm2 ~]# ip neigh show
10.2.0.1 dev enp0s3 lladdr 0a:00:27:00:00:0d DELAY
[root@vm2 ~]#
```
* Routeur:
```
[root@vm3 /]# sudo ip neigh flush all
[root@vm3 /]# ip neigh show
10.1.0.1 dev enp0s3 lladdr 0a:00:27:00:00:16 DELAY
[root@vm3 /]#
```
3. Ouvrir netcat et échangez quelques messages
* Serveur1:
```
[root@vm2 ~]# nc -l 5454
wesh
ça marche
cool
```
* Client1:
```
[root@vm1 ~]# nc vm2.tp4 5454
wesh
ça marche
cool
```
* Routeur:
```
sudo tcpdump -i enp0s3 -w netcat_ok.pcap
```
* Capture:
<img src="./netcat.PNG">


<!--stackedit_data:
eyJoaXN0b3J5IjpbMTE0NDEzMzc3NiwtMTM5NzcyMjk4NCwtNT
EwNDAzMDc4LDI5MzY1ODAwNSwtMTMyNTUyMDEzOCwtMTk4NTg0
MTkyLDEzNTQ3NjMxMjcsMjAzNDQwMjMwMywtMjAxMzU3Nzk3Ml
19
-->
