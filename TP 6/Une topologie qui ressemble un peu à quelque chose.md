
# TP 6 - Une topologie qui ressemble un peu à quelque chose, enfin ?

## Auteur
**Lúca BRUNO** - Bordeaux Ynov Campus Informatique - [Khaelo](https://github.com/Khaelo)


## Lab 1 : Simple OSPF

### 1. Présentation du lab et contexte

Présentation du lab et contextualisation du TP [ici](https://github.com/It4lik/B1-Reseau-2018/tree/master/tp/6#1-pr%C3%A9sentation-du-lab-et-contexte "Sujet du TP6").

### 2. Mise en place du lab

#### Checklist IP Routeurs

On parle de `r1.tp6.b1`, `r2.tp6.b1`, `r3.tp6.b1`, `r4.tp6.b1` et `r5.tp6.b1` :

- [X] Définition des IPs statiques (Exemple avec le routeur 1 (ici R1): 
-`R1# conf t`
-`R1(config)#int [nom de l'interface]`
-`R1(conf-t)#ip address [IP du réseau] [IP du masque]`)
- [X] Définition du nom de domaine (Exemple avec le routeur 1 (ici R1): 
-`R1# conf t`
-`R1(config)#hostname [hostname]`

__Ne pas oublier de sauvegarder les configurations avec `wr` (Non, je me suis pas fait avoir, c'est pas vrai)__

#### Checklist VMs

On parle de `client1.tp6.b1`, `client2.tp6.b1` et `server1.tp6.b1` :

- [X] Désactiver SELinux (déjà fait dans la VM patron)
- [X] Installation de différents paquets réseau (déjà fait dans la VM patron)
- [X] Enlever la carte NAT (déjà fait dans la VM patron)
- [X] Définition des IP statiques (Manip' classico classique, `ip a` pour connaitre le nom de l'interface dont on veut changer l'ip, modification du fichier `ifcfg` avec la commande 
*`su vi /etc/sysconfig/network-scripts/ifcfg-[Nom de l'interface]` 
(Ne pas oublier pour ce TP la Gateway) et pour finir le redémarrage de l'interface avec:
*`su ifdown [Nom de l'interface]` et `sudo ifup [Nom de l'interface]`)
- [X] Définition du nom de domaine (`su vi /etc/hostname`)
- [X] Remplissage des fichiers `hosts` (`su vi /etc/hosts`, une ligne se forme de la manière suivante : 
*`[IP] [premier nom] [second nom] [plus de nom]`, (par exemple : **10.6.201.11 client2 client2.tp6.b1**)

#### Configuration OSPF

Configuration des routeurs seulement :

- [X] Activation d'**OSPF** : 
-`conf t` 
-`(config)#router ospf 1`.
- [X] Configurer le **router-id** : 
-`(config-router)#router-id [ID du routeur]` (Par exemple : `(config-router)#router-id 1.1.1.1`).
- [X] Configuration des routes à partager :
-`(config-router)#network [IP du réseau à partager] [Nombre d'IP disponibles dans ce réseau] area [Nunméro de Zone]`, qui donne par exemple : `(config-router)#network 10.6.100.0 0.0.0.3 area 0`.

Pour vérifier que toutes ces configurations fonctionnent, on `ping` ou `traceroute` le serveur avec le client1 puis le client2 :

* Traceroute du `client1.tp6.b1` vers `server.tp6.b1`:

    ```
    [root@client1 ~]$ traceroute server
    traceroute to server (10.6.202.10), 30 hops max, 60 byte packets
    1  gateway (10.6.201.254)  9.493 ms  9.382 ms  9.227 ms
    2  10.6.101.1 (10.6.101.1)  30.648 ms  30.454 ms  30.249 ms
    3  10.6.100.13 (10.6.100.13)  51.203 ms  51.093 ms  51.094 ms
    4  10.6.100.5 (10.6.100.5)  61.816 ms  61.695 ms  61.726 ms
    5  server (10.6.202.10)  61.746 ms !X  61.583 ms !X  61.632 ms !X
    ```

* Traceroute de `server.tp6.b1` vers `client2.tp6.b1`:

    ```
    [root@server ~]$ traceroute client2.tp6.b1
    traceroute to client2.tp6.b1 (10.6.201.11), 30 hops max, 60 byte packets
    1  gateway (10.6.202.254)  7.955 ms  7.816 ms  7.890 ms
    2  10.6.100.2 (10.6.100.2)  29.094 ms  28.931 ms  28.826 ms
    3  10.6.100.10 (10.6.100.10)  49.689 ms  49.557 ms  49.640 ms
    4  10.6.101.2 (10.6.101.2)  60.777 ms  60.691 ms  60.674 ms
    5  client2 (10.6.201.11)  60.648 ms !X  60.585 ms !X  60.551 ms !X
    ```

## Lab 2 : Let's end this properly

### 1. NAT : accès internet

On télécharge la GNS3 VM, puis on configure son accès sur GNS3. Une fois fait, on redémarre GNS3 puis on ajoute le p'tit nuage à un routeur afin d'apporter internet au réseau (Ici il s'agira du router4.tp6.b1).
Une fois ajouté, on vérifie sur quelle interface de notre routeur la carte NAT est branchée. Elle n'a normalement pas encore d'IP, let's go:
-`r4.tp6.b1# conf t` 
-`r4.tp6.b1(config)# interface [Nom de l'interface]`) 
puis on configure son IP comme dynamique grâce au DHCP : 
-`r4.tp6.b1(config-if)# ip address dhcp`

Pour vérifier que ça fonctionne, il faut faire une requête HTTP :

-`r4.tp6.b1(config)# ip domain-lookup` pour activer le "Lookup DNS"
-`r4.tp6.b1(config)# ip name-server 8.8.8.8` pour configurer un serveur DNS (ici, celui de google)
-`r4.tp6.b1# telnet trip-hop.net 80` afin de récupérer l'HTML de la page internet souhaité, pour vérifier que l'on a bien l'Internet.

Enfin, on configure le NAT en précisant dans la configuration de chaque interface du routeur connecté à internet: -(`r4.tp6.b1(config)# interface [Nom de l'interface]`) 
si il est dans le réseau de routeurs ou si il **sort** vers l'Interweb 
-(`r4.tp6.b1(config-if)# ip nat inside` à l'intérieur du réseau ou `r4.tp6.b1(config-if)# ip nat outside` si il va vers l'extérieur).

Ensuite, on active le NAT pour les membres de la liste 1 avec la commande 
-`r4.tp6.b1(config)# ip nat inside source list 1 interface fastEthernet 0/0 overload` 
puis on ajoute toutes les machines du réseau à la liste 1 : 
-`r4.tp6.b1(config)# access-list 1 permit any`.

Puis on partage la route par défaut à tout le monde (une fois sur la config routeur `r4.tp6.b1# conf t`, puis `r4.tp6.b1(config)# router ospf 1`) avec la commande `r4.tp6.b1(config-router)# default-information originate`.

Pour tester, on peut faire un **`curl google.com`** depuis les VMs.
Par exemple ici, on curl google.com depuis `server.tp6.b1` :
```
[root@server ~]$ curl google.com
<HTML><HEAD><meta http-equiv="content-type" content="text/html;charset=utf-8">
<TITLE>301 Moved</TITLE></HEAD><BODY>
<H1>301 Moved</H1>
The document has moved
<A HREF="http://www.google.com/">here</A>.
</BODY></HTML>
```

### 2. Un service d'infra

> Pour simuler ça, on va mettre en place un simple serveur web avec une page d'accueil moche. ~~ It4Lik

Sur la machine `server.tp6.b1`:

* 1. On vérifie que le `firewall` est en fonctionnement :
```
[root@server ~]$ sudo systemctl status firewalld
[sudo] password for root:
● firewalld.service - firewalld - dynamic firewall daemon
   Loaded: loaded (/usr/lib/systemd/system/firewalld.service; enabled; vendor preset: enabled)
   Active: active (running) since Tue 2019-03-12 09:43:44 CET; 2h 4min ago
     Docs: man:firewalld(1)
 Main PID: 2654 (firewalld)
   CGroup: /system.slice/firewalld.service
           └─2654 /usr/bin/python -Es /usr/sbin/firewalld --nofork --nopid

Mar 12 09:43:43 server.tp6.b1 systemd[1]: Starting firewalld - dynamic firewall daemon...
Mar 12 09:43:44 server.tp6.b1 systemd[1]: Started firewalld - dynamic firewall daemon.
```
(si il ne fonctionne pas, il faut le démarrer avec la commande `sudo systemctl start firewalld`)

* 2. On ajoute une règle pour autoriser le trafique web :
Avec la commande:
-`sudo firewall-cmd --add-port=80/tcp --permanent` 
puis on redémarre le FireWall: 
-`sudo firewall-cmd --reload`.

* 3. On installe le serveur web :
```
sudo yum install -y epel-release
sudo yum install -y nginx
```

* 4. Puis on le lance avec `sudo systemctl start nginx`

* 5. Enfin, on s'assure qu'il fonctionne avec la commande `curl localhost`:
```
[root@server ~]$ curl localhost
<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.1//EN" "http://www.w3.org/TR/xhtml11/DTD/xhtml11.dtd">

<html xmlns="http://www.w3.org/1999/xhtml" xml:lang="en">
    <head>
        <title>Test Page for the Nginx HTTP Server on Fedora</title>
        <meta http-equiv="Content-Type" content="text/html; charset=UTF-8" />
        <style type="text/css">
            <Plein de choses intéressantes en HTML mais pas en réseau>
        </style>
    </head>

    <body>
        <Plein de choses intéressantes en HTML mais pas en réseau>
    </body>
</html>
```

Puis, vu que j'ai bien fait mon taff , je curl `server1.tp6.b1` depuis `client1.tp6.b1`:
```
[root@client1 ~]$ curl server.tp6.b1
<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.1//EN" "http://www.w3.org/TR/xhtml11/DTD/xhtml11.dtd">

<html xmlns="http://www.w3.org/1999/xhtml" xml:lang="en">
    <head>
        <title>Test Page for the Nginx HTTP Server on Fedora</title>
        <meta http-equiv="Content-Type" content="text/html; charset=UTF-8" />
        <style type="text/css">
            <Plein de choses intéressantes en HTML mais pas en réseau>
        </style>
    </head>

    <body>
        <Plein de choses intéressantes en HTML mais pas en réseau>
    </body>
</html>
```

### 3. Serveur DHCP

Le serveur DHCP, c'est pour le réseau des clients : `10.6.201.0/24`. Pour économiser des ressources, on va recycler `client2.tp6.b1` en `dhcp.tp6.b1` :
* Renommer la machine `sudo nano /etc/hostname`
* On récupère le fichier `dhcpd.conf` et on l'adapte à ce réseau.

Une fois le DHCP installé, et lancé, on modifie le fichier `/etc/sysconfig/network-scripts/ifcfg-enp0s3` pour lui permettre d’accéder au serveur DCHP et de récupérer une IP dans la plage d'IP précisée dans le fichier `dhcpd.conf`.
On vérifie que que l’adresse de `client1.tp6.b1` à correctement changé (car hors de la plage donnée au DHCP) avec un `ip a`:
```
[root@client1 ~]$ ip a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host
       valid_lft forever preferred_lft forever
2: enp0s3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP group default qlen 1000
    link/ether 08:00:27:fc:f3:44 brd ff:ff:ff:ff:ff:ff
    inet 10.6.201.50/24 brd 10.6.201.255 scope global noprefixroute dynamic enp0s3
       valid_lft 597sec preferred_lft 597sec
    inet6 fe80::a00:27ff:fefc:f344/64 scope link
       valid_lft forever preferred_lft forever
3: enp0s8: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP group default qlen 1000
    link/ether 08:00:27:8d:d7:9f brd ff:ff:ff:ff:ff:ff
    inet 192.168.16.2/24 brd 192.168.16.255 scope global noprefixroute enp0s8
       valid_lft forever preferred_lft forever
    inet6 fe80::a00:27ff:fe8d:d79f/64 scope link
       valid_lft forever preferred_lft forever
```

### 4. Serveur DNS

Une fois le serveur DNS installé, et toutes les configurations faites, on peut tester avec :
* Un `dig server1.tp6.b1`:
```
[root@client1 ~]$ dig server1.tp6.b1

; <<>> DiG 9.9.4-RedHat-9.9.4-72.el7 <<>> server1.tp6.b1
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 19181
;; flags: qr aa rd; QUERY: 1, ANSWER: 1, AUTHORITY: 1, ADDITIONAL: 1
;; WARNING: recursion requested but not available

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 4096
;; QUESTION SECTION:
;server1.tp6.b1.                        IN      A

;; ANSWER SECTION:
server1.tp6.b1.         604800  IN      A       10.6.202.10

;; AUTHORITY SECTION:
tp6.b1.                 604800  IN      NS      server1.tp6.b1.

;; Query time: 65 msec
;; SERVER: 10.6.202.10#53(10.6.202.10)
;; WHEN: Tue Mar 12 17:28:14 CET 2019
;; MSG SIZE  rcvd: 73
```

* Un `dig client2.tp6.b1`:

```
[root@client1 ~]$ dig client2.tp6.b1

; <<>> DiG 9.9.4-RedHat-9.9.4-72.el7 <<>> client2.tp6.b1
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 15961
;; flags: qr aa rd; QUERY: 1, ANSWER: 1, AUTHORITY: 1, ADDITIONAL: 2
;; WARNING: recursion requested but not available

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 4096
;; QUESTION SECTION:
;client2.tp6.b1.                        IN      A

;; ANSWER SECTION:
client2.tp6.b1.         604800  IN      A       10.6.201.11

;; AUTHORITY SECTION:
tp6.b1.                 604800  IN      NS      server1.tp6.b1.

;; ADDITIONAL SECTION:
server1.tp6.b1.         604800  IN      A       10.6.202.10

;; Query time: 81 msec
;; SERVER: 10.6.202.10#53(10.6.202.10)
;; WHEN: Tue Mar 12 17:30:43 CET 2019
;; MSG SIZE  rcvd: 97
```

* Un `dig -x 10.6.201.10`:

```
[root@client1 ~]$ dig -x 10.6.201.10

; <<>> DiG 9.9.4-RedHat-9.9.4-72.el7 <<>> -x 10.6.201.10
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 23580
;; flags: qr aa rd; QUERY: 1, ANSWER: 1, AUTHORITY: 1, ADDITIONAL: 2
;; WARNING: recursion requested but not available

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 4096
;; QUESTION SECTION:
;10.201.6.10.in-addr.arpa.      IN      PTR

;; ANSWER SECTION:
10.201.6.10.in-addr.arpa. 86400 IN      PTR     client1.tp6.b1.

;; AUTHORITY SECTION:
6.10.in-addr.arpa.      86400   IN      NS      server1.tp6.b1.

;; ADDITIONAL SECTION:
server1.tp6.b1.         604800  IN      A       10.6.202.10

;; Query time: 81 msec
;; SERVER: 10.6.202.10#53(10.6.202.10)
;; WHEN: Tue Mar 12 17:31:31 CET 2019
;; MSG SIZE  rcvd: 119
```

Pour reconfigurer le serveur DHCP, il suffit de changer la ligne précédemment `option domain-name "tp6.b1";` par `option domain-name-servers 10.6.202.10;`
