---
title: Utiliser relayd en tant que Reverse Proxy
category: Web
order: 4
---

Un serveur principal qui héberge plusieurs VM sur lesquels un service web tourne. Dans OpenBSD, relayd permet entre autre de pouvoir rediriger le flux au bon endroit.

> nano /etc/relayd.conf

```
table <cloud>   { 10.99.0.4 }
table <torrent> { 10.99.0.1 }
table <plex>    { 10.99.0.2 }

http protocol "votredomaine" {
        match request quick header "Host" value "cloud.votredomaine.fr" forward to <cloud>
        match request quick header "Host" value "torrent.votredomaine" forward to <torrent>
        match request quick header "Host" value "plex.votredomaine.fr" forward to <plex>
}

relay "plex" {
        listen on 10.99.0.3 port 80
        protocol "votredomaine"
        forward to <plex> port 32400
}

relay "cloud" {
        listen on 10.99.0.3 port 80
        protocol "votredomaine"
        forward to <cloud> port 80
}

relay "torrent" {
        listen on 10.99.0.3 port 80
        protocol "votredomaine"
        forward to <torrent> port 80
}
```

Vérifier que la configuration ne comporte pas d'erreur.

> relayd -n

Puis recharger la configure et/ou redémarrer le service.

> rcctl reload relayd

> rcctl restart relayd

Documentation : http://man.openbsd.org/relayd.8

Documentation : http://man.openbsd.org/cgi-bin/man.cgi/OpenBSD-current/man5/relayd.conf
