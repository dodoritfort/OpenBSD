---
layout: page
title: Configurer SSH
wikiPageName: Configurer-SSH
menu: wiki
---

# Configurer SSH

### **Retrouvez plus d'articles sur http://wiki.dodoritfort.xyz/**

> nano /etc/ssh/sshd_config

Changer le port de SSH `Port 222`.

Interdire la connexion SSH en root `PermitRootLogin no`.

Autoriser 6 tentatives de connexions maximum `MaxAuthTries 6`.

Limiter à 10 le nombre de session `MaxSessions 10`.

Puis recharger la configuration.

> rcctl reload sshd

Et bien entendu, nous devons nous déconnecter et reconnecter (sur le port 222 à présent).

### **Retrouvez plus d'articles sur http://wiki.dodoritfort.xyz/**
