# TP 5 - Premier pas dans le monde Cisco
## Auteur
**Lúca BRUNO** - Bordeaux Ynov Campus Informatique - [Khaelo](https://github.com/Khaelo)
## Sommaire
* [Préparation du Lab](#i-préparation-du-lab)
* [Préparation des VMs](#i1-préparation-des-vms)
* [Préparation des Routeurs Cisco](#i2-préparation-des-routeurs-cisco)
* [Préparation des Switches](#i3-préparation-des-switches)
* [Topologie et tableau récapitulatif](#i4-topologie-et-tableau-récapitulatif)
* [Lancement et configuration du Lab](#ii-lancement-et-configuration-du-lab)
* [Configuration des VMs](#ii1-configuration-des-vms)
 * [Définition des IPs statiques](#ii11-définition-des-ips-statiques)
 * [Définition des noms de domaine](#ii12-définition-des-noms-de-domaine)
* [Configuration des Routeurs](#ii2-configuration-des-routeurs)
 * [Définitions des IPs statiques](#ii21-définitions-des-ips-statiques)
 * [Définitions des noms de domaines](#ii22-définitions-des-noms-de-domaine)
* [Ajout des routes](#ii3-ajout-des-routes)
* [DHCP](#iii-dhcp)
## I. Préparation du Lab
### I.1 Préparation des VMs
On a tout d'abord cloné trois fois la `VM_CCNA_Patron` pour obtenir les VMs suivantes :
* **server1.tp5.b1** est dans `NET1` et portera l'IP `10.5.1.10/24` ;
* **client1.tp5.b1** est dans `NET2` et portera l'IP `10.5.2.10/24` ;
* **client2.tp5.b1** est dans `NET2` et portera l'IP `10.5.1.11/24`.

Ensuite après avoir créé le projet **GNS3 - TP5** avec le logiciel *GNS3*, nous avons ajouté ces VMs en suivant la procédure suivante :
* Cliquer sur l'onglet *Edit* ;
* Cliquer sur *Preferences* ;
* Aller dans la sous-rubrique *VirtualBox VMs* ;
* Cliquer sur ***New*** pour ajouter les VMs.

Pour terminer nous avons configuré les réseaux des 3 VMs dans *GNS3* en faisant les manipulations suivantes :
* Faire un clique-droit sur les icônes des VMs ;
* Cliquer sur *Configure* ;
* Aller dans l'onglet *Network* ;
* Mettre pour chaque VMs **2 cartes réseaux**.

### I.2 Préparation des Routeurs Cisco
Pour cette partie nous avons importé l'`.iso` du routeur et mis deux dans GNS3 :
* `router1.tp5.b1` est dans
* `NET1`  et portera l'IP `10.5.1.254/24`
* `NET12`
* `router2.tp5.b1` est dans
* `NET2` et portera l'IP `10.5.2.254/24`
* `NET12`

### I.3 Préparation des Switches
Nous avons ajouté un **Ethernet Switches** dans notre projet *GNS3* .

### I.4 Topologie et tableau récapitulatif
#### Topologie
![](https://github.com/Khaelo/CCNA/blob/master/TP%205/image/Topologie_reseau.png)
#### Tableau récapitulatif
Machine | `NET1` | `NET2` | `NET12`
--- | --- | --- | ---
`client1.tp5.b1` | **X** | `10.5.2.10/24` | **X**
`client2.tp5.b1` | **X** | `10.5.2.11/24` | **X**
`router1.tp5.b1` | `10.5.1.254/24` | **X** | `10.5.12.10/30`
`router2.tp5.b1` | **X** | `10.5.2.254/24` | `10.5.12.9/30`
`server1.tp5.b1` | `10.5.1.10/24` | **X** | **X**

## II. Lancement et configuration du Lab
### II.1 Configuration des VMs
#### II.1.1 Définition des IPs statiques
* **Sur la VM *client1.tp5.b1***
```bash
[lbruno@localhost ~]$ sudo nano /etc/sysconfig/network-scripts/ifcfg-enp0s3
NAME="enp0s3"
DEVICE="enp0s3"

BOOTPROTO="static"
ONBOOT="yes"

IPADDR=10.5.2.10
NETMASK=255.255.255.0
[lbruno@localhost ~]$ sudo ifdown enp0s3
Périphérique « enp0s3 » déconnecté.
[lbruno@localhost ~]$ sudo ifup enp0s3
Connexion activée (chemin D-Bus actif : /org/freedesktop/NetworkManager/ActiveConnection/4)
[lbruno@localhost ~]$ ip a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host
       valid_lft forever preferred_lft forever
2: enp0s3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP group default qlen 1000
    link/ether 08:00:27:de:1b:14 brd ff:ff:ff:ff:ff:ff
    inet 10.5.2.10/24 brd 10.5.2.255 scope global noprefixroute enp0s3
       valid_lft forever preferred_lft forever
    inet6 fe80::d59f:eef0:735a:6542/64 scope link noprefixroute
       valid_lft forever preferred_lft forever
3: enp0s8: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP group default qlen 1000
    link/ether 08:00:27:c1:90:99 brd ff:ff:ff:ff:ff:ff
    inet 192.168.59.3/24 brd 192.168.59.255 scope global noprefixroute dynamic enp0s8
       valid_lft 702sec preferred_lft 702sec
    inet6 fe80::8926:388b:38ae:d2e/64 scope link noprefixroute
       valid_lft forever preferred_lft forever
```
* **Sur la VM *client2.tp5.b1***
```bash
[lbruno@localhost ~]$ sudo nano /etc/sysconfig/network-scripts/ifcfg-enp0s3
NAME="enp0s3"
DEVICE="enp0s3"

BOOTPROTO="static"
ONBOOT="yes"

IPADDR=10.5.2.11
NETMASK=255.255.255.0
[lbruno@localhost ~]$ sudo ifdown enp0s3
Périphérique « enp0s3 » déconnecté.
[lbruno@localhost ~]$ sudo ifup enp0s3
Connexion activée (chemin D-Bus actif : /org/freedesktop/NetworkManager/ActiveConnection/4)
[lbruno@localhost ~]$ ip a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host
       valid_lft forever preferred_lft forever
2: enp0s3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP group default qlen 1000
    link/ether 08:00:27:de:1b:14 brd ff:ff:ff:ff:ff:ff
    inet 10.5.2.11/24 brd 10.5.2.255 scope global noprefixroute enp0s3
       valid_lft forever preferred_lft forever
    inet6 fe80::d59f:eef0:735a:6542/64 scope link tentative noprefixroute dadfailed
       valid_lft forever preferred_lft forever
    inet6 fe80::c93d:2ce6:c8b4:4fe5/64 scope link noprefixroute
       valid_lft forever preferred_lft forever
3: enp0s8: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP group default qlen 1000
    link/ether 08:00:27:14:f6:59 brd ff:ff:ff:ff:ff:ff
    inet 192.168.59.4/24 brd 192.168.59.255 scope global noprefixroute dynamic enp0s8
       valid_lft 1048sec preferred_lft 1048sec
    inet6 fe80::991a:5d87:88b4:fbc3/64 scope link noprefixroute
       valid_lft forever preferred_lft forever
```
* **Sur la VM *server1.tp5.b1***
```bash
[lbruno@localhost ~]$ sudo nano /etc/sysconfig/network-scripts/ifcfg-enp0s3
NAME="enp0s3"
DEVICE="enp0s3"

BOOTPROTO="static"
ONBOOT="yes"

IPADDR=10.5.1.10
NETMASK=255.255.255.0
[lbruno@localhost ~]$ sudo ifdown enp0s3
Périphérique « enp0s3 » déconnecté.
[lbruno@localhost ~]$ sudo ifup enp0s3
Connexion activée (chemin D-Bus actif : /org/freedesktop/NetworkManager/ActiveConnection/4)
[lbruno@localhost ~]$ ip a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host
       valid_lft forever preferred_lft forever
2: enp0s3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP group default qlen 1000
    link/ether 08:00:27:de:1b:14 brd ff:ff:ff:ff:ff:ff
    inet 10.5.1.10/24 brd 10.5.1.255 scope global noprefixroute enp0s3
       valid_lft forever preferred_lft forever
    inet6 fe80::d59f:eef0:735a:6542/64 scope link noprefixroute
       valid_lft forever preferred_lft forever
3: enp0s8: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP group default qlen 1000
    link/ether 08:00:27:3a:78:cf brd ff:ff:ff:ff:ff:ff
    inet 192.168.59.5/24 brd 192.168.59.255 scope global noprefixroute dynamic enp0s8
       valid_lft 1032sec preferred_lft 1032sec
    inet6 fe80::dcca:d21a:c5a8:e7d/64 scope link noprefixroute
       valid_lft forever preferred_lft forever
```
#### II.1.2 Définition des noms de domaine
* **Sur la VM *client1.tp5.b1***
```bash
[lbruno@localhost ~]$ echo 'client1.tp5.b1' | sudo tee /etc/hostname
client1.tp5.b1
```
Après avoir redémarré la VM *client1.tp5.b1*, on obtient :
```bash
Last login: Mon Feb 11 23:25:40 2019
[lbruno@client1 ~]$ hostname
client1.tp5.b1
```
* **Sur la VM *client2.tp5.b1***
```bash
[lbruno@localhost ~]$ echo 'client2.tp5.b1' | sudo tee /etc/hostname
client2.tp5.b1
```
Après avoir redémarré la VM *client2.tp5.b1*, on obtient :
```bash
Last login: Mon Feb 11 23:25:32 2019
[lbruno@client2 ~]$ hostname
client2.tp5.b1
```
* **Sur la VM *server1.tp5.b1***
```bash
[lbruno@localhost ~]$ echo 'server1.tp5.b1' | sudo tee /etc/hostname
server1.tp5.b1
```
Après avoir redémarré la VM *server1.tp5.b1*, on obtient :
```bash
Last login: Mon Feb 11 23:25:50 2019
[lbruno@server1 ~]$ hostname
server1.tp5.b1
```
### II.2 Configuration des Routeurs
#### II.2.1 Définition des IPs statiques
* **Sur la VM *router1.tp5.b1***
```bash
interface Ethernet0/0
 ip address 10.5.1.254 255.255.255.0

interface Ethernet0/1
 ip address 10.5.12.10 255.255.255.252
```
* **Sur la VM *router2.tp5.b1***
```bash
interface Ethernet0/0
 ip address 10.5.12.9 255.255.255.252

interface Ethernet0/1
 ip address 10.5.2.254 255.255.255.0
```
#### II.2.2 Définition des nom de domaine
* **Sur la VM *router1.tp5.b1***
```bash
Router1(config)# hostname router1.tp5.b1
router1.tp5.b1#
```
* **Sur la VM *router2.tp5.b1***
```bash
Router2(config)# hostname router2.tp5.b1
router2.tp5.b1#
```
### II.3 Ajout des routes
* **Sur la VM *router1.tp5.b1***
```bash
router1.tp5.b1#show ip route
Codes: C - connected, S - static, R - RIP, M - mobile, B - BGP
       D - EIGRP, EX - EIGRP external, O - OSPF, IA - OSPF inter area
       N1 - OSPF NSSA external type 1, N2 - OSPF NSSA external type 2
       E1 - OSPF external type 1, E2 - OSPF external type 2
       i - IS-IS, su - IS-IS summary, L1 - IS-IS level-1, L2 - IS-IS level-2
       ia - IS-IS inter area, * - candidate default, U - per-user static route
       o - ODR, P - periodic downloaded static route

Gateway of last resort is 10.5.12.9 to network 0.0.0.0

     10.0.0.0/8 is variably subnetted, 3 subnets, 2 masks
C       10.5.12.8/30 is directly connected, Ethernet0/1
S       10.5.2.0/24 [1/0] via 10.5.12.9
C       10.5.1.0/24 is directly connected, Ethernet0/0
S*   0.0.0.0/0 [1/0] via 10.5.12.9
```
* **Sur la VM *router2.tp5.b1***
```bash
router2.tp5.b1#show ip route
Codes: C - connected, S - static, R - RIP, M - mobile, B - BGP
       D - EIGRP, EX - EIGRP external, O - OSPF, IA - OSPF inter area
       N1 - OSPF NSSA external type 1, N2 - OSPF NSSA external type 2
       E1 - OSPF external type 1, E2 - OSPF external type 2
       i - IS-IS, su - IS-IS summary, L1 - IS-IS level-1, L2 - IS-IS level-2
       ia - IS-IS inter area, * - candidate default, U - per-user static route
       o - ODR, P - periodic downloaded static route

Gateway of last resort is 10.5.12.10 to network 0.0.0.0

     10.0.0.0/8 is variably subnetted, 3 subnets, 2 masks
C       10.5.12.8/30 is directly connected, Ethernet0/0
C       10.5.2.0/24 is directly connected, Ethernet0/1
S       10.5.1.0/24 [1/0] via 10.5.12.10
S*   0.0.0.0/0 [1/0] via 10.5.12.10
```
* **Sur la VM *server1.tp5.b1***
* **Ajout de la route `NET2`**
```bash
[lbruno@server1 ~]$ ip route show
10.5.1.0/24 dev enp0s3 proto kernel scope link src 10.5.1.10 metric 102
10.5.2.0/24 via 10.5.1.254 dev enp0s3 proto static metric 102 192.168.59.0/24 dev enp0s8 proto kernel scope link src 192.168.59.5 metric 101
```
* **Ajout dans les fichiers `hosts`**
```bash
10.5.1.10   server1   server1.tp5.b1
```
* **Sur la VM *client1.tp5.b1***
* **Ajout de la route `NET1`**
```bash
[lbruno@client1 ~]$ ip route show
10.5.1.0/24 via 10.5.2.254 dev enp0s3 proto static metric 102
10.5.2.0/24 dev enp0s3 proto kernel scope link src 10.5.2.10 metric 102
192.168.59.0/24 dev enp0s8 proto kernel scope link src 192.168.59.3 metric 101
```
* **Ajout dans les fichiers `hosts`**
```bash
10.5.2.10   client1   client1.tp5.b1
```
* **Sur la VM *client2.tp5.b1***
* **Ajout de la route `NET1`**
```bash
[lbruno@client2 ~]$ ip route show
10.5.1.0/24 via 10.5.2.254 dev enp0s3 proto static metric 102
10.5.2.0/24 dev enp0s3 proto kernel scope link src 10.5.2.11 metric 102
192.168.59.0/24 dev enp0s8 proto kernel scope link src 192.168.59.4 metric 101
```
* **Ajout dans les fichiers `hosts`**
```bash
10.5.2.11   client2   client2.tp5.b1
```
#### Vérification
* **Ping de *client1.tp5.b1* vers *server1.tp5.b1***
```bash
[lbruno@client1 ~]$ ping server1
PING server1 (10.5.1.10) 56(84) bytes of data.
64 bytes from server1 (10.5.1.10): icmp_seq=1 ttl=62 time=38.1 ms
64 bytes from server1 (10.5.1.10): icmp_seq=2 ttl=62 time=36.2 ms
64 bytes from server1 (10.5.1.10): icmp_seq=3 ttl=62 time=34.5 ms
64 bytes from server1 (10.5.1.10): icmp_seq=4 ttl=62 time=33.0 ms
64 bytes from server1 (10.5.1.10): icmp_seq=5 ttl=62 time=38.7 ms
64 bytes from server1 (10.5.1.10): icmp_seq=6 ttl=62 time=36.0 ms
64 bytes from server1 (10.5.1.10): icmp_seq=7 ttl=62 time=35.6 ms
64 bytes from server1 (10.5.1.10): icmp_seq=8 ttl=62 time=33.8 ms
64 bytes from server1 (10.5.1.10): icmp_seq=9 ttl=62 time=33.8 ms
64 bytes from server1 (10.5.1.10): icmp_seq=10 ttl=62 time=32.7 ms

--- server1 ping statistics ---
10 packets transmitted, 10 received, 0% packet loss, time 9018ms
rtt min/avg/max/mdev = 32.759/35.286/38.725/1.952 ms
```
* **Ping de *client2.tp5.b1* vers *server1.tp5.b1***
```bash
[lbruno@client2 ~]$ ping server1
PING server1 (10.5.1.10) 56(84) bytes of data.
64 bytes from server1 (10.5.1.10): icmp_seq=1 ttl=62 time=21.5 ms
64 bytes from server1 (10.5.1.10): icmp_seq=2 ttl=62 time=29.6 ms
64 bytes from server1 (10.5.1.10): icmp_seq=3 ttl=62 time=24.4 ms
64 bytes from server1 (10.5.1.10): icmp_seq=4 ttl=62 time=31.2 ms
64 bytes from server1 (10.5.1.10): icmp_seq=5 ttl=62 time=29.3 ms
64 bytes from server1 (10.5.1.10): icmp_seq=6 ttl=62 time=28.9 ms
64 bytes from server1 (10.5.1.10): icmp_seq=7 ttl=62 time=25.3 ms
64 bytes from server1 (10.5.1.10): icmp_seq=8 ttl=62 time=31.4 ms
64 bytes from server1 (10.5.1.10): icmp_seq=9 ttl=62 time=28.0 ms
64 bytes from server1 (10.5.1.10): icmp_seq=10 ttl=62 time=23.1 ms

--- server1 ping statistics ---
10 packets transmitted, 10 received, 0% packet loss, time 9036ms
rtt min/avg/max/mdev = 21.564/27.308/31.402/3.285 ms
```
* **Ping de *server1.tp5.b1* vers *client1.tp5.b1***
```bash
[lbruno@server1 ~]$ ping client1
PING client1 (10.5.2.10) 56(84) bytes of data.
64 bytes from client1 (10.5.2.10): icmp_seq=1 ttl=62 time=39.5 ms
64 bytes from client1 (10.5.2.10): icmp_seq=2 ttl=62 time=33.5 ms
64 bytes from client1 (10.5.2.10): icmp_seq=3 ttl=62 time=37.3 ms
64 bytes from client1 (10.5.2.10): icmp_seq=4 ttl=62 time=26.5 ms
64 bytes from client1 (10.5.2.10): icmp_seq=5 ttl=62 time=23.5 ms
64 bytes from client1 (10.5.2.10): icmp_seq=6 ttl=62 time=23.9 ms
64 bytes from client1 (10.5.2.10): icmp_seq=7 ttl=62 time=23.3 ms
64 bytes from client1 (10.5.2.10): icmp_seq=8 ttl=62 time=27.0 ms
64 bytes from client1 (10.5.2.10): icmp_seq=9 ttl=62 time=23.6 ms
64 bytes from client1 (10.5.2.10): icmp_seq=10 ttl=62 time=28.3 ms

--- client1 ping statistics ---
10 packets transmitted, 10 received, 0% packet loss, time 9049ms
rtt min/avg/max/mdev = 23.335/28.691/39.522/5.714 ms
```
* **Ping de *server1.tp5.b1* vers *client2.tp5.b1***
```bash
[lbruno@server1 ~]$ ping client2
PING client2 (10.5.2.11) 56(84) bytes of data.
64 bytes from client2 (10.5.2.11): icmp_seq=1 ttl=62 time=65.9 ms
64 bytes from client2 (10.5.2.11): icmp_seq=2 ttl=62 time=22.8 ms
64 bytes from client2 (10.5.2.11): icmp_seq=3 ttl=62 time=28.6 ms
64 bytes from client2 (10.5.2.11): icmp_seq=4 ttl=62 time=27.7 ms
64 bytes from client2 (10.5.2.11): icmp_seq=5 ttl=62 time=24.7 ms
64 bytes from client2 (10.5.2.11): icmp_seq=6 ttl=62 time=74.5 ms
64 bytes from client2 (10.5.2.11): icmp_seq=7 ttl=62 time=28.3 ms
64 bytes from client2 (10.5.2.11): icmp_seq=8 ttl=62 time=29.5 ms
64 bytes from client2 (10.5.2.11): icmp_seq=9 ttl=62 time=29.0 ms
64 bytes from client2 (10.5.2.11): icmp_seq=10 ttl=62 time=25.6 ms

--- client2 ping statistics ---
10 packets transmitted, 10 received, 0% packet loss, time 9046ms
rtt min/avg/max/mdev = 22.864/35.716/74.567/17.488 ms
```
## III. DHCP
### III.1 Mise en place du serveur DHCP
* **Changement du nom de domaine de la VM *client2.tp5.b1* en *dhcp-net2.tp5.b1***
```bash
[lbruno@client2 ~]$ echo 'dhcp-net2.tp5.b1' | sudo tee /etc/hostname 
dhcp-net2.tp5.b1
```
* **Configuration du serveur DHCP**
```bash
[lbruno@dhcp-net2 ~]$ sudo nano /etc/dhcp/dhcpd.conf
```
* **Démarrage du serveur DHCP**
```bash
[lbruno@dhcp-net2 ~]$ sudo systemctl start dhcpd
[lbruno@dhcp-net2 ~]$ systemctl status dhcpd
dhcpd.service - DHCPv4 Server Daemon
   Loaded: loaded (/usr/lib/systemd/system/dhcpd.service; enabled; vendor preset: disabled)
   Active: active (running) since dim. 2019-02-24 17:39:17 CET; 1min 30s ago
     Docs: man:dhcpd(8)
           man:dhcpd.conf(5)
 Main PID: 3548 (dhcpd)
   Status: "Dispatching packets..."
   CGroup: /system.slice/dhcpd.service
           └─3548 /usr/sbin/dhcpd -f -cf /etc/dhcp/dhcpd.conf -user dhcpd -group dhcpd --no-pid

févr. 24 17:39:17 dhcp-net2.tp5.b1 dhcpd[3548]: Listening on LPF/enp0s3/08:00:27:de:1b:14/10.5.2.11/24
févr. 24 17:39:17 dhcp-net2.tp5.b1 dhcpd[3548]: Sending on   LPF/enp0s3/08:00:27:de:1b:14/10.5.2.11/24
févr. 24 17:39:17 dhcp-net2.tp5.b1 dhcpd[3548]: Sending on   Socket/fallback/fallback-net
févr. 24 17:39:17 dhcp-net2.tp5.b1 systemd[1]: Started DHCPv4 Server Daemon.
```
* **Faire un test**

Nous avons tout d'abord modifié le fichier `/etc/sysconfig/network-scripts/ifcfg-enp0s3` de manière permanente.
```bash
[lbruno@dhcp-net2 ~]$ sudo dhclient -v -r
Internet Systems Consortium DHCP Client 4.2.5
Copyright 2004-2013 Internet Systems Consortium.
All rights reserved.
For info, please visit https://www.isc.org/software/dhcp/

Listening on LPF/enp0s8/08:00:27:14:f6:59
Sending on   LPF/enp0s8/08:00:27:14:f6:59
Listening on LPF/enp0s3/08:00:27:de:1b:14
Sending on   LPF/enp0s3/08:00:27:de:1b:14
Sending on   Socket/fallback
```
On demande une nouvelle adresse IP:
```bash
[lbruno@dhcp-net2 ~]$ dhclient -v enp0s3
Internet Systems Consortium DHCP Client 4.2.5
Copyright 2004-2013 Internet Systems Consortium.
All rights reserved.
For info, please visit https://www.isc.org/software/dhcp/

Listening on LPF/enp0s3/00:50:00:00:04:00
Sending on   LPF/enp0s3/00:50:00:00:04:00
Sending on   Socket/fallback
DHCPDISCOVER on enp0s3 to 255.255.255.255 port 67 interval 3 (xid=0xa2f7eb)
DHCPREQUEST on enp0s3 to 255.255.255.255 port 67 (xid=0xa2f7eb)
DHCPOFFER from 10.5.2.11
DHCPACK from 10.5.2.11 (xid=0xa2f7eb)
bound to 10.5.2.50 -- renewal in 352 seconds.
```
### Explorer un peu DHCP
