---
layout: page
title: PacketFilter   Exemple(s)
wikiPageName: PacketFilter---Exemple(s)
menu: wiki
---

# PacketFilter - Exemple(s)

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
tun_if = "tun0"                 # Carte reseau VPN
port_ssh = "222"                # Port SSH
port_http = "{ www https }"     # Port(s) HTTP(S)
set skip on lo                  # Pas de filtre en local
set limit table-entries 400000  # Nombre d entree maximale dans une table

######################
#  TABLES Blacklist  #
######################
table <ssh_abuse> persist       # Table pour le SSH
table <http_abuse> persist      # Table pour le HTTP

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
pass in on $ext_if proto tcp to any port $port_ssh flags S/SA modulate state \
        (max-src-conn 3, max-src-conn-rate 3/60, overload <ssh_abuse> flush global)

################################
#  HTTP(S) -  Anti BruteForce  #
################################
block in log quick proto tcp from <http_abuse> to any port $port_http
pass in on $ext_if proto tcp to any port $port_http flags S/SA modulate state \
        (max-src-conn 100, max-src-conn-rate 40/5, overload <http_abuse> flush)

#########
#  VPN  #
#########
pass in quick on $tun_if synproxy state
pass out on $ext_if from 10.8.0.0/24 to any nat-to ($ext_if)
```

Documentation : [http://man.openbsd.org/pf.conf](http://man.openbsd.org/pf.conf)

### **Retrouvez plus d'articles sur http://wiki.dodoritfort.xyz/**
