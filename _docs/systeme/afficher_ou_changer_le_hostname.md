---
title: Afficher / Changer le hostname
category: Système
order: 6
---

## Introduction

Le hostname (nom d'hôte) est le nom donné à une machine.

## Configuration

Pour afficher le nom d'hôte, exécuter la commande suivante.

> cat /etc/myname

Pour changer le hostname, il suffit d'éditer le fichier `/etc/myname` où `exemple` est le nom de la machine et `votredomaine.fr` le nom de domaine dont elle fait partie.

> nano /etc/myname

```
exemple.votredomaine.fr
```

Pour que les modifications soient prises en compte il faut redémarrer la machine où exécuter la commande suivante.

> sh /etc/netstart
