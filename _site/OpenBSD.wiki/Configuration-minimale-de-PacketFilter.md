# Configuration minimale de PacketFilter

### **Retrouvez plus d'articles sur http://wiki.dodoritfort.xyz/**

> nano /etc/pf.conf

```
#       $OpenBSD: pf.conf,v 1.54 2014/08/23 05:49:42 deraadt Exp $
#
# See pf.conf(5) and /etc/examples/pf.conf

###########
#  MACRO  #
###########
ext_if = "em0"                  # Interface
port_ssh = "22"                 # Port SSH
set skip on lo                  # Pas de filtre en local
set limit table-entries 400000  # Nombre d entree maximale dans une table

######################
#  TABLES Blacklist  #
######################
table <ssh_abuse> persist

#####################################################
#  Protection contre l'usurpation de l'adresse IP   #
#####################################################
block in quick from urpf-failed
antispoof for $ext_if

block return    # block stateless traffic
pass            # establish keep-state

# By default, do not permit remote connections to X11
block return in on ! lo0 proto tcp to port 6000:6010

###########################
#  SSH - Anti BruteForce  #
###########################
block in log quick proto tcp from <ssh_abuse> to any port $port_ssh
pass in on $ext_if proto tcp to any port $port_ssh flags S/SA keep state \
        (max-src-conn 3, max-src-conn-rate 3/60, overload <ssh_abuse> flush global)
```

Activer PacketFilter
> pfctl -e

Désactiver PacketFilter
> pfctl -d

Vérifier la configuration sans l'injecter
> pfctl -nf /etc/pf.conf

Recharger la configuration à chaud
> pfctl -f /etc/pf.conf

Voir les règles chargées
> pfctl -s rules

Afficher les flux en temps réel
> tcpdump -n -e -ttt -i pflog0

Afficher les logs
> tcpdump -n -e -ttt -r /var/log/pflog

Afficher les IP de la blackliste
> pfctl -t ssh_abuse -T show

Pour supprimer une adresse IP de la blackliste
> pfctl -t ssh_abuse -T delete xxx.xxx.xxx.xxx

Vider les règles du pare feu
> pfctl -F all

Voir les connexions en cours
> pfctl -s states

Documentation : [http://man.openbsd.org/pf.conf](http://man.openbsd.org/pf.conf)

### **Retrouvez plus d'articles sur http://wiki.dodoritfort.xyz/**