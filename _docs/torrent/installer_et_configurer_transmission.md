---
title: Installer et configurer Transmission
category: Torrent
order: 1
---

## Introduction

Transmission est un client Bitorrent qui se veut rapide, simple et gratuit.

Il embarque de base une interface web qui permet une gestion conviviale.

Site officiel : https://transmissionbt.com/

## Installation

Pour installer Transmission, rien de plus simple.

> pkg_add -i transmission

Lors de l'installation, l'utilisateur `_transmission` est créé.

## Configuration

Dans cet exemple, Transmission sera utilisé avec Plex. Pour éviter que Plex n'indexe les fichiers dont le téléchargement n'est pas terminé, il faudra les séparer.

Pour cela, il faudra créer un répertoire `Incomplete` et lui attribuer les bons droits.

> mkdir /var/transmission/Incomplete

> chown -R _transmission:_transmission /var/transmission/Incomplete

Le fichier de configuration à modifier est `/var/transmission/.config/transmission-daemon/settings.json`.

Paramètre	Valeur
rpc-enabled	Active l'interface Web
rpc-port	Port de connexion à l'interface Web
rpc-username	nom d'utilisateur
rpc-password	mot de passe
rpc-authentication-required	Active l'authentification de l'interface Web
rpc-whitelist	Limite l'accès à l'interface web aux adresses IP spécifié
rpc-whitelist-enabled	Active la limite d'accès à l'interface Web
download-dir	Répertoire de téléchargement
incomplete-dir	Répertoire ou seront stocké les fichiers en cours de téléchargement
incomplete-dir-enabled	Active le répertoire temporaire
Peer port	Port de téléchargement
dht-enabled	A désactiver
pex-enabled	A désactiver

> nano /var/transmission/.config/transmission-daemon/settings.json

```
{
    "alt-speed-down": 50,
    "alt-speed-enabled": false,
    "alt-speed-time-begin": 540,
    "alt-speed-time-day": 127,
    "alt-speed-time-enabled": false,
    "alt-speed-time-end": 1020,
    "alt-speed-up": 50,
    "bind-address-ipv4": "0.0.0.0",
    "bind-address-ipv6": "::",
    "blocklist-enabled": false,
    "blocklist-url": "",
    "cache-size-mb": 4,
    "dht-enabled": true,
    "download-dir": "/var/transmission/Downloads", // Dossier de destination des téléchargements.
    "download-queue-enabled": true,
    "download-queue-size": 5,
    "encryption": 1,
    "idle-seeding-limit": 30,
    "idle-seeding-limit-enabled": false,
    "incomplète-dir": "/var/transmission/Incomplete", // Dossier qui contiendra les téléchargements en cours.
    "incomplete-dir-enabled": true, // Active l'option "incomplète-dir".
    "lpd-enabled": false,
    "message-level": 2,
    "peer-congestion-algorithm": "",
    "peer-id-ttl-hours": 6,
    "peer-limit-global": 200,
    "peer-limit-per-torrent": 50,
    "peer-port": 51413, // Le port à ouvrir pour permettre à Transmission d'accéder à Internet.
    "peer-port-random-high": 65535,
    "peer-port-random-low": 49152,
    "peer-port-random-on-start": false,
    "peer-socket-tos": "default",
    "pex-enabled": true,
    "port-forwarding-enabled": true,
    "preallocation": 1,
    "prefetch-enabled": true,
    "queue-stalled-enabled": true,
    "queue-stalled-minutes": 30,
    "ratio-limit": 2,
    "ratio-limit-enabled": false,
    "rename-partial-files": true,
    "rpc-authentication-required": false,
    "rpc-bind-address": "0.0.0.0",
    "rpc-enabled": true,
    "rpc-password": "Mot_de_passe", // Le mot de passe de connexion (qui sera crypté au redémarrage de Transmission).
    "rpc-port": 9091, // Le port qui permet d'établir la connexion à Transmission.
    "rpc-url": "/transmission/",
    "rpc-username": "transmission", // Le nom d'utilisateur pour la connexion.
    "rpc-whitelist": "127.0.0.1,192.168.1.*", // Important : Les adresses IP autorisées à se connecter à l'itnerface web.
    "rpc-whitelist-enabled": true, // Cette option active la liste des adresses IP autorisées.
    "scrape-paused-torrents-enabled": true,
    "script-torrent-done-enabled": false,
    "script-torrent-done-filename": "",
    "seed-queue-enabled": false,
    "seed-queue-size": 10,
    "speed-limit-down": 100,
    "speed-limit-down-enabled": false,
    "speed-limit-up": 100,
    "speed-limit-up-enabled": false,
    "start-added-torrents": true,
    "trash-original-torrent-files": false,
    "umask": 18,
    "upload-slots-per-torrent": 14,
    "utp-enabled": true
}
```

## Premier démarrage

Pour exécuter Transmission au démarrage du système, il suffit d'exécuter la commande suivante.

> rcctl enable transmission_daemon

Et pour démarrer Transmission manuellement.

> rcctl start transmission_daemon

Ensuite, il suffit de se connecter à l'interface web de Transmission.

http://votre_serveur:9091/

- Attention, il faut absolument se connecter depuis une adresse IP autorisée ! L'adresse IP ou la plage réseau a été définie dans le fichier de configuration.
- De même que si le port par défaut d'accès à l'interface à été modifié, il faudra le prendre en considération.
- Ne pas oublier non plus d'ouvrir le port de téléchargement (par défaut 51413) sur votre routeur ou parefeu.
