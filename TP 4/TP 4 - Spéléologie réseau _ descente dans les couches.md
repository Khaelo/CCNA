# TP 4 - Spéléologie réseau : descente dans les couches
## Auteur
**Lúca BRUNO** - Bordeaux Ynov Campus Informatique - [Khaelo](https://github.com/Khaelo)
## Sommaire
* [Préparation d'une VM "patron"](#preparation-dune-vm-patron)
* [Mise en place du Lab](#i-mise-en-place-du-lab)
  * [Création des réseaux](#i1-création-des-réseaux)
  * [Création des VMs](#i2-création-des-vms)
  * [Mise en place du routage statique](#i3-mise-en-place-du-routage-statique)
* [Spéléologie réseau](#ii-spéléologie-réseau)
  * [Manipulation 1](#ii11-manipulation-1)
  * [Manipulation 2](#ii12-manipulation-2)
  * [Manipulation 3](#ii13-manipulation-3)
  * [Manipulation 4](#ii14-manipulation-4)
## Préparation d'une VM "patron"
On a dû dans un premier temps créer et installer une VM patron avec les configurations suivantes :
* 512 Mo RAM
* 1 CPU
* une carte NAT
* un disque de 8Go
* l'`.iso` de CentOS 7 (sur le "contrôleur IDE")

![](https://github.com/Khaelo/CCNA/blob/master/TP%204/images/1.png)

Dans un deuxième temps, nous avons dû la configurer à partir des lignes de commandes ci-dessous :
```bash
# Désactivation de SELinux
sudo setenforce 0 # temporaire
sudo sed -i 's/enforcing/permissive/g' /etc/selinux/config # permanent

# Mise à jour des dépôts
sudo yum update -y

# Installation de dépôts additionels
sudo yum install -y epel-release

# Installation de plusieurs paquets réseau dont on se sert souvent
sudo yum install -y traceroute bind-utils tcpdump nc nano

# Désactivation de la carte NAT au reboot
sudo nano /etc/sysconfig/network-scripts/ifcfg-enp0s3
# mettre ONBOOT à NO

# Eteindre la machine
sudo shutdown now
```

## I. Mise en place du Lab
### I.1 Création des réseaux
Nous avons ensuite créé deux nouveaux réseaux host-only dont les spécificités de ceux-ci sont les suivants :
* **vboxnet1** :
  * la carte réseau de l'hôte doit porter l'IP `10.1.0.1`
  * ce réseau n'a **PAS** de serveur DHCP
* **vboxnet2** :
  * la carte réseau de l'hôte doit porter l'IP `10.2.0.1`
  * ce réseau n'a **PAS** de serveur DHCP

![](https://github.com/Khaelo/CCNA/blob/master/TP%204/images/2.png)
![](https://github.com/Khaelo/CCNA/blob/master/TP%204/images/3.png)

### I.2 Création des VMs
Dans cette partie, nous avons cloner trois fois la *VM "patron"* pour obtenir trois VMs distinctes :
* **La VM Cliente** nommée *client1.tp4*
  * qui contient une carte réseau dans `vboxnet1` qui porte l'IP `10.1.0.10`

  ![](https://github.com/Khaelo/CCNA/blob/master/TP%204/images/6.png)

* **La VM Serveur** nommée *server1.tp4*
  * qui contient une carte réseau dans `vboxnet2` qui porte l'IP `10.2.0.10`

  ![](https://github.com/Khaelo/CCNA/blob/master/TP%204/images/7.png)

* **La VM Routeur** nommée *router1.tp4*
  * qui contient une carte réseau dans `vboxnet1` qui porte l'IP `10.1.0.254`
  * qui contient une carte réseau dans `vboxnet2` qui porte l'IP `10.2.0.254`

  ![](https://github.com/Khaelo/CCNA/blob/master/TP%204/images/8.png)

De plus après avoir défini les adresses IPs statiques de chacunes d'elles, nous leur avons défini des noms de domaines, rempli leur fichier `/etc/hosts` et pour terminer nous les avons "pingé" entre elles :
* **ping de *client1* vers *router1.tp4* sur l'IP `10.1.0.254`:**
  ```bash
  [lbruno@client1 ~]$ ping 10.1.0.254
  PING 10.1.0.254 (10.1.0.254) 56(84) bytes of data.
  64 bytes from 10.1.0.254: icmp_seq=1 ttl=64 time=1.29 ms
  64 bytes from 10.1.0.254: icmp_seq=2 ttl=64 time=0.756 ms
  64 bytes from 10.1.0.254: icmp_seq=3 ttl=64 time=0.831 ms
  64 bytes from 10.1.0.254: icmp_seq=4 ttl=64 time=0.817 ms
  64 bytes from 10.1.0.254: icmp_seq=5 ttl=64 time=0.726 ms
  64 bytes from 10.1.0.254: icmp_seq=6 ttl=64 time=0.834 ms
  64 bytes from 10.1.0.254: icmp_seq=7 ttl=64 time=0.698 ms
  64 bytes from 10.1.0.254: icmp_seq=8 ttl=64 time=0.754 ms
  64 bytes from 10.1.0.254: icmp_seq=9 ttl=64 time=0.764 ms
  64 bytes from 10.1.0.254: icmp_seq=10 ttl=64 time=0.764 ms

  --- 10.1.0.254 ping statistics ---
  10 packets transmitted, 10 received, 0% packet loss, time 9054ms
  rtt min/avg/max/mdev = 0.698/0.823/1.290/0.163 ms
  ```
* **ping de *server1* vers *router1.tp4* sur l'IP `10.2.0.254`:**
  ```bash
  [lbruno@server1 ~]$ ping 10.2.0.254
  PING 10.2.0.254 (10.2.0.254) 56(84) bytes of data.
  64 bytes from 10.2.0.254: icmp_seq=1 ttl=64 time=1.31 ms
  64 bytes from 10.2.0.254: icmp_seq=2 ttl=64 time=0.730 ms
  64 bytes from 10.2.0.254: icmp_seq=3 ttl=64 time=0.404 ms
  64 bytes from 10.2.0.254: icmp_seq=4 ttl=64 time=0.776 ms
  64 bytes from 10.2.0.254: icmp_seq=5 ttl=64 time=0.751 ms
  64 bytes from 10.2.0.254: icmp_seq=6 ttl=64 time=0.655 ms
  64 bytes from 10.2.0.254: icmp_seq=7 ttl=64 time=0.770 ms
  64 bytes from 10.2.0.254: icmp_seq=8 ttl=64 time=0.369 ms
  64 bytes from 10.2.0.254: icmp_seq=9 ttl=64 time=0.675 ms
  64 bytes from 10.2.0.254: icmp_seq=10 ttl=64 time=0.430 ms

  --- 10.2.0.254 ping statistics ---
  10 packets transmitted, 10 received, 0% packet loss, time 9030ms
  rtt min/avg/max/mdev = 0.369/0.687/1.316/0.259 ms
  ```

**Tableau récapitulatif** :

Machine | `vboxnet1` | `vboxnet2`
--- | --- | ---
*client1.tp4* | `10.1.0.10` |
*router1.tp4* | `10.1.0.254` | `10.2.0.254`
*server1.tp4* || `10.2.0.10`

### I.3 Mise en place du routage statique
> Pour afficher la table de routage sur Linux : `ip route show`.

* **Sur le Routeur :**
  * On a transformé la VM *router1.tp4* en routeur grâce à la ligne de commande `sudo sysctl -w net.ipv4.conf.all.forwarding=1` ;
  * Afin d'éviter certaines actions non voulues, on a désactivé le *firewall* de manière permanente par la ligne de commande `sudo systemctl disable firewalld` ;
  * Pour finir nous avons vérifier que le routeur aller bien vers les réseaux `vboxnet1` et `vboxnet2` par la commande `ip route show`.

* **Sur le Client :**
  Afin que la machine *client1.tp4* ait une route définitive vers `vboxnet1` et `vboxnet2`, nous avons écrit dans le fichier `/etc/sysconfig/network-scripts/route-enp0s3` le ligne suivante:
  `10.2.0.0/24 via 10.1.0.254 dev enp0s3`
  Une fois le fichier édité, nous effectuons successivement les commandes `sudo ifdown enp0s3` et `sudo ifup enp0s3` (désactivation puis activation de la carte réseau) pour que les modifications agissent.
  **On laisse activer le firewall de la machine**.

* **Sur le Serveur :**
  Afin que la machine *server1.tp4* ait une route définitive vers `vboxnet1` et `vboxnet2`, nous avons écrit dans le fichier `/etc/sysconfig/network-scripts/route-enp0s3` le ligne suivante:
  `10.1.0.0/24 via 10.2.0.254 dev enp0s3`
  Une fois le fichier édité, nous effectuons successivement les commandes `sudo ifdown enp0s3` et `sudo ifup enp0s3` (désactivation puis activation de la carte réseau) pour que les modifications agissent.
  **On laisse activer le firewall de la machine**.

Pour terminer, nous vérifions que la connexion entre le Client et le Serveur est possible grâce aux commandes suivantes :
* à partir de la machine *client1.tp4* :
  * `ping 10.2.0.10`;
  * `traceroute 10.2.0.10`.
* à partir de la machine *server1.tp4* :
  * `ping 10.1.0.10`;
  * `traceroute 10.1.0.10`.

## II. Spéléologie réseau
### II.1 ARP
> Pour afficher la table ARP sur Linux : `ip neigbour show`.

On se connecte au *client1.tp4*, au *server1.tp4* et au *router1.tp4* en **SSH**.

#### II.1.1 Manipulation 1
**1. *client1.tp4* :**
  * **Affichage de la table ARP**
    ```bash
    [lbruno@client1 ~]$ sudo ip neigh flush all
    [lbruno@client1 ~]$ ip neigh show
    10.1.0.1 dev enp0s3 lladdr 0a:00:27:00:00:01 REACHABLE
    ```
  * **Ping vers *server1***
    ```bash
    [lruno@client1 ~]$ ping server1
    PING server1 (10.2.0.10) 56(84) bytes of data.
    64 bytes from server1 (10.2.0.10): icmp_seq=1 ttl=63 time=0.811 ms
    64 bytes from server1 (10.2.0.10): icmp_seq=2 ttl=63 time=0.712 ms
    64 bytes from server1 (10.2.0.10): icmp_seq=3 ttl=63 time=1.11 ms
    64 bytes from server1 (10.2.0.10): icmp_seq=4 ttl=63 time=1.28 ms
    64 bytes from server1 (10.2.0.10): icmp_seq=5 ttl=63 time=1.04 ms
    64 bytes from server1 (10.2.0.10): icmp_seq=6 ttl=63 time=1.22 ms
    64 bytes from server1 (10.2.0.10): icmp_seq=7 ttl=63 time=1.02 ms
    64 bytes from server1 (10.2.0.10): icmp_seq=8 ttl=63 time=1.03 ms
    64 bytes from server1 (10.2.0.10): icmp_seq=9 ttl=63 time=0.785 ms
    64 bytes from server1 (10.2.0.10): icmp_seq=10 ttl=63 time=0.969 ms

    --- server1 ping statistics ---
    10 packets transmitted, 10 received, 0% packet loss, time 9018ms
    rtt min/avg/max/mdev = 0.712/1.000/1.288/0.182 ms
    ```
  * **Nouvel affichage de la table ARP**
    ```bash
    [lbruno@client1 ~]$ ip neigh show
    10.1.0.254 dev enp0s3 lladdr 08:00:27:a9:71:02 STALE
    10.1.0.1 dev enp0s3 lladdr 0a:00:27:00:00:01 DELAY
    ```

**2. *server1.tp4* :**
  * **Affichage de la table ARP**
    ```bash
    [lbruno@server1 ~]$ sudo ip neigh flush all
    [lbruno@server1 ~]$ ip neigh show
    10.2.0.1 dev enp0s3 lladdr 0a:00:27:00:00:02 REACHABLE
    ```
  * **Ping vers *client1***
    ```bash
    [lbruno@server1 ~]$ ping client1
    PING client1 (10.1.0.10) 56(84) bytes of data.
    64 bytes from client1 (10.1.0.10): icmp_seq=1 ttl=63 time=1.52 ms
    64 bytes from client1 (10.1.0.10): icmp_seq=2 ttl=63 time=1.09 ms
    64 bytes from client1 (10.1.0.10): icmp_seq=3 ttl=63 time=1.25 ms
    64 bytes from client1 (10.1.0.10): icmp_seq=4 ttl=63 time=1.11 ms
    64 bytes from client1 (10.1.0.10): icmp_seq=5 ttl=63 time=0.853 ms
    64 bytes from client1 (10.1.0.10): icmp_seq=6 ttl=63 time=1.32 ms
    64 bytes from client1 (10.1.0.10): icmp_seq=7 ttl=63 time=1.07 ms
    64 bytes from client1 (10.1.0.10): icmp_seq=8 ttl=63 time=0.991 ms
    64 bytes from client1 (10.1.0.10): icmp_seq=9 ttl=63 time=0.794 ms
    64 bytes from client1 (10.1.0.10): icmp_seq=10 ttl=63 time=2.69 ms

    --- client1 ping statistics ---
    10 packets transmitted, 10 received, 0% packet loss, time 9058ms
    rtt min/avg/max/mdev = 0.794/1.271/2.695/0.516 ms
    ```
  * **Nouvel affichage de la table ARP**
    ```bash
    [lbruno@server1 ~]$ ip neigh show
    10.2.0.254 dev enp0s3 lladdr 08:00:27:5a:f9:dd STALE
    10.2.0.1 dev enp0s3 lladdr 0a:00:27:00:00:02 DELAY
    ```

#### II.1.2 Manipulation 2
**1. *router1.tp4* :**
* **Affichage de la table ARP**
    ```bash
    [lbruno@router1 ~]$ sudo ip neigh flush all
    [lbruno@router1 ~]$ ip neigh show
    10.1.0.1 dev enp0s3 lladdr 0a:00:27:00:00:01 REACHABLE
    ```
**2. *client1.tp4* :**
  ```bash
  [lbruno@client1 ~]$ ping server1
  PING server1 (10.2.0.10) 56(84) bytes of data.
  64 bytes from server1 (10.2.0.10): icmp_seq=1 ttl=63 time=0.899 ms
  64 bytes from server1 (10.2.0.10): icmp_seq=2 ttl=63 time=1.48 ms
  64 bytes from server1 (10.2.0.10): icmp_seq=3 ttl=63 time=1.46 ms
  64 bytes from server1 (10.2.0.10): icmp_seq=4 ttl=63 time=0.864 ms
  64 bytes from server1 (10.2.0.10): icmp_seq=5 ttl=63 time=1.48 ms

  --- server1 ping statistics ---
  5 packets transmitted, 5 received, 0% packet loss, time 4011ms
  rtt min/avg/max/mdev = 0.864/1.240/1.487/0.292 ms
  ```
**3. *router1.tp4* :**
  ```bash
  [lbruno@router1 ~]$ ip neigh show
  10.1.0.1 dev enp0s3 lladdr 0a:00:27:00:00:01 DELAY
  10.1.0.10 dev enp0s3 lladdr 08:00:27:40:22:b5 STALE
  10.2.0.10 dev enp0s8 lladdr 08:00:27:2a:2e:60 STALE
  ```
#### II.1.3 Manipulation 3
```bash
luca@BRUNO:~$ ip neigh show
10.1.0.254 dev vboxnet1 lladdr 08:00:27:a9:71:02 STALE
10.33.1.253 dev wlp3s0 lladdr 5c:c5:d4:8c:83:c7 STALE
10.33.3.219 dev wlp3s0 lladdr 04:d3:b0:0e:73:a1 STALE
10.33.1.231 dev wlp3s0 lladdr c0:b6:f9:75:bb:ab STALE
10.33.3.254 dev wlp3s0 lladdr 94:0c:6d:84:50:c8 STALE
10.33.3.253 dev wlp3s0 lladdr 00:12:00:40:4c:bf REACHABLE
10.1.0.10 dev vboxnet1 lladdr 08:00:27:40:22:b5 STALE
10.33.1.132 dev wlp3s0 lladdr 98:22:ef:58:bc:87 STALE
10.33.1.141 dev wlp3s0 lladdr 0c:54:15:c5:19:ee STALE
10.2.0.10 dev vboxnet2 lladdr 08:00:27:2a:2e:60 STALE
10.33.2.136 dev wlp3s0 lladdr 38:f9:d3:25:7c:f5 STALE
10.33.1.222 dev wlp3s0 lladdr c0:b6:f9:77:77:a2 STALE
luca@BRUNO:~$ sudo ip neigh flush all
luca@BRUNO:~$ ip neigh show
10.33.3.253 dev wlp3s0 lladdr 00:12:00:40:4c:bf REACHABLE
luca@BRUNO:~$ ip neigh show
10.33.3.253 dev wlp3s0 lladdr 00:12:00:40:4c:bf REACHABLE
```
#### II.1.4 Manipulation 4
```bash
[lbruno@client1 ~]$ sudo ip neigh flush all
[sudo] Mot de passe de yann : 
[lbruno@client1 ~]$ curl google.fr
<HTML><HEAD><meta http-equiv="content-type" content="text/html;charset=utf-8">
<TITLE>301 Moved</TITLE></HEAD><BODY>
<H1>301 Moved</H1>
The document has moved
<A HREF="http://www.google.fr/">here</A>.
</BODY></HTML>
[lbruno@client1 ~]$ ip neigh show
10.1.0.1 dev enp0s3 lladdr 0a:00:27:00:00:01 REACHABLE
10.0.3.3 dev enp0s8 lladdr 52:54:00:12:35:03 REACHABLE
10.0.3.2 dev enp0s8 lladdr 52:54:00:12:35:02 REACHABLE
```
