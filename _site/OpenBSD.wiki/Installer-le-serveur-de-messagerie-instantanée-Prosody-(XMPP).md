# Installer le serveur de messagerie Prosody (XMPP)

### **Retrouvez plus d'articles sur http://wiki.dodoritfort.xyz/**

Prosody est un serveur de messagerie instantanée XMPP écrit en LUA. Ses objectifs sont d'être simple à configurer, d'être modulaire et de consommer peu de ressource système.

## 1. Installation

Rien de plus simple pour l'installer ! Il suffit d'exécuter la commande suivante.

> pkg_add -i prosody

```
# pkg_add -i prosody
quirks-2.241 signed on 2016-07-26T16:56:10Z
prosody-0.9.10p2:libidn-1.32p1: ok
prosody-0.9.10p2:lua-5.1.5p6: ok
prosody-0.9.10p2:luafs-1.6.3p0: ok
prosody-0.9.10p2:luadbi-0.5p0: ok
prosody-0.9.10p2:luazlib-20100731p2: ok
prosody-0.9.10p2:luasocket-3.0rc1p1: ok
prosody-0.9.10p2:luasec-0.6p0: ok
prosody-0.9.10p2:luaexpat-1.3.0p0: ok
prosody-0.9.10p2: ok
The following new rcscripts were installed: /etc/rc.d/prosody
See rcctl(8) for details.
Look in /usr/local/share/doc/pkg-readmes for extra documentation.
```

## 2. Configuration minimale

Une fois l'installation effectuée, il faut éditer le fichier de configuration `prosody.cfg.lua`.

A savoir que le double-tiret `--` sert à commenter les lignes.

> nano /etc/prosody/prosody.cfg.lua

```
-- VirtualHost "localhost"
VirtualHost "votredomaine.fr"
```

Documentation : https://prosody.im/doc/configure

## 3. Premier démarrage

Après avoir configuré notre domaine, nous pouvons dès à présent démarrer notre serveur prosody.

> rcctl start prosody

Nous vérifions ensuite que le service est bien démarré.

> prosodyctl status

```
# prosodyctl status
Prosody is running with PID 99255
```

Pour qu'il soit exécuté à chaque démarrage, nous devons l'activer.

> rcctl enable prosody

Documentation : https://prosody.im/doc/prosodyctl

## 4. Ajouter un utilisateur

Par défaut, l'option de création de compte via un client XMPP est désactivée. Il s'agit de la ligne `allow_registration = false;` du fichier de configuration.

Nous devons donc le créer manuellement via la commande suivante où `votredomaine.fr` est celui que nous avons spécifié dans le `VirtualHost` du fichier de configuration.

> prosodyctl adduser user@votredomaine.fr

Nous pouvons ensuite nous connecter avec le client XMPP de notre choix et vérifier le fichier log.

> cat /var/prosody/prosody.log

```
# cat /var/prosody/prosody.log
Mar 25 11:48:13 general info    Hello and welcome to Prosody version 0.9.10
Mar 25 11:48:13 general info    Prosody is using the select backend for connection handling
Mar 25 11:48:13 portmanager     info    Activated service 'c2s' on [::]:5222, [*]:5222
Mar 25 11:48:13 portmanager     info    Activated service 'legacy_ssl' on no ports
Mar 25 11:48:13 portmanager     info    Activated service 's2s' on [::]:5269, [*]:5269
Mar 25 11:48:13 mod_posix       info    Prosody is about to detach from the console, disabling further console output
Mar 25 11:48:13 mod_posix       info    Successfully daemonized to PID 5826
Mar 25 11:49:16 c2s107ce75ddf80 info    Client connected
Mar 25 11:49:20 c2s107ce75ddf80 info    Authenticated as user@votredomaine.fr
Mar 25 11:49:37 c2s107ce75ddf80 info    Client disconnected: closed
```

Pour changer le mot de passe d'un utilisateur.

> prosodyctl passwd user@votredomaine.fr

Pour supprimer un utilisateur.

> prosodyctl deluser user@votredomaine.fr

Documentation : http://prosody.im/doc/creating_accounts

## 5. Configurer le mode SSL

Commençons par générer le certificat.

> prosodyctl cert generate votredomaine.fr

```
# prosodyctl cert generate votredomaine.fr
Choose key size (2048): 2048
Generating RSA private key, 2048 bit long modulus
...............................................+++
...........................+++
e is 65537 (0x10001)
Key written to /var/prosody/votredomaine.fr.key
Please provide details to include in the certificate config file.
Leave the field empty to use the default value or '.' to exclude the field.
countryName (GB): FR
localityName (The Internet): 
organizationName (Your Organisation): 
organizationalUnitName (XMPP Department):
commonName (votredomaine.fr):
emailAddress (xmpp@votredomaine.fr):

Config written to /var/prosody/votredomaine.fr.cnf
Certificate written to /var/prosody/votredomaine.fr.crt
```

Puis spécifions cela dans le fichier de configuration.

> nano /etc/prosody/prosody.cfg.lua

```
-- These are the SSL/TLS-related settings. If you don't want
-- to use SSL/TLS, you may comment or remove this
ssl = {
        key = "/var/prosody/votredomaine.fr.key";
        certificate = "/var/prosody/votredomaine.fr.crt";
}
```

Ensuite on recharge la configuration et on redémarre le serveur.

> prosodyctl reload

```
# prosodyctl reload
Prosody log files re-opened and config file reloaded. You may need to reload modules for some changes to take effect.
```

> prosodyctl restart

```
# prosodyctl restart
Stopped
Started
```

Documentation : https://prosody.im/doc/certificates

### **Retrouvez plus d'articles sur http://wiki.dodoritfort.xyz/**