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
		* VM1 :
		* VM2 :
		* VM3 :
<!--stackedit_data:
eyJoaXN0b3J5IjpbNDY2NTE2NzI4LC0yMDEzNTc3OTcyXX0=
-->