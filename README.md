# Dépôt GitHub personnel pour la SAE21

J'ai été chargé de faire le serveur DNS et le serveur WEB externe. Nous avons opté pour une solution "durable" en désignant mon Raspberry Pi 3 (qui prennait la poussière au passage) comme chef de serveurs DNS et WEB.

## Serveur DNS

- J'ai tout d'abord crée mon fichier de zone, à savoir [db.ksp](/bind/db.ksp) dans /etc/bind/, en copiant le modèle db.empty grâce à la commande `cp db.empty db.ksp` .

- J'ai ensuite édité le fichier [named.conf.local](/bind/named.conf.local) toujours dans /etc/bind, et y ai ajouté les lignes suivantes :
> 
    zone "ksp.fr" IN {
        type master;
        file "/etc/bind/db.ksp";
        };

De ce fait, j'ai lié mon fichier de zone [db.ksp](/bind/db.ksp) à ma zone ksp.fr (nous empêchons donc l'accès à ce site en interne mais ce n'est qu'un détail). Ce fichier est d'ailleurs le master, d'où la spécification dans le type, et nous n'avons pas de slave.


- Pour en finir avec le serveur DNS, j'ai édité le fichier [named.conf.options](/bind/named.conf.options) pour rediriger vers d'autres DNS car bien évidemment je n'allais pas chercher à la main chaque site à mettre dans mon fichier de zone :)
J'ai donc ajouté ces deux lignes dans le fichier :
>
    forwarders {
        8.8.8.8;
        10.255.255.200;
    };

## Serveur WEB

- Pour le serveur web, j'ai choisit de me tourner vers [nginx](http://nginx.com/) parce que pourquoi pas (j'ai voulu divorcer d'Apache). On aurait pu choisir lighttpd, mais on a pris nginx au hasard en réalité, on voulait faire simple et fonctionnel. Un petit `sudo apt get install nginx`

- C'est pour cela que la page web [index.nginx-debian.html](/var/www/html/index.nginx-debian.html) est la page web par défaut de nginx. Nous l'avons seulement modifiée pour inscrire nos noms dessus.



## Configutation IP

- Parce qu'il faut bien désigner son adresse IP fixe pour bien confiruger le DNS. Notre configuration était donc la suivante :
```bash
    pi@ksp:~ $ ip a show dev eth0
    2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP group default qlen 1000
    link/ether b8:27:eb:ca:f4:f5 brd ff:ff:ff:ff:ff:ff
    inet 10.40.21.249/29 brd 10.40.21.255 scope global noprefixroute eth0
       valid_lft forever preferred_lft forever
    inet6 fe80::3a41:67cd:8ac4:3fbb/64 scope link
       valid_lft forever preferred_lft forever
```
La route par défaut est par ailleurs configurée vers 10.40.21.254 (routeur MikroTik)

<br><br><br><br><br><br><br>

Je tiens à m'excuser de ne pas avoir suivi le "daily logging" de mes activités
