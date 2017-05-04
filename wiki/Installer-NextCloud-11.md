---
layout: page
title: Installer NextCloud 11
wikiPageName: Installer-NextCloud-11
menu: wiki
---

# 8. Installer NextCloud

### **Retrouvez plus d'articles sur http://wiki.dodoritfort.xyz/**

Pour commencer, nous avons besoin de l'outil `wget` pour télécharger l'archive NextCloud. Commençons donc par l'installer.

> pkg_add -i wget

```
# pkg_add -i wget
quirks-2.241 signed on 2016-07-26T16:56:10Z
wget-1.18:pcre-8.38p0: ok
wget-1.18:libunistring-0.9.6p0: ok
wget-1.18:libpsl-0.13.0: ok
wget-1.18: ok
```

Puis, téléchargeons l'archive NextCloud.

> wget https://download.nextcloud.com/server/releases/nextcloud-11.0.2.tar.bz2

```
# wget https://download.nextcloud.com/server/releases/nextcloud-11.0.2.tar.bz2
--2017-04-05 19:36:10--  https://download.nextcloud.com/server/releases/nextcloud-11.0.2.tar.bz2
Resolving download.nextcloud.com (download.nextcloud.com)... 88.198.160.133
Connecting to download.nextcloud.com (download.nextcloud.com)|88.198.160.133|:443... connected.
HTTP request sent, awaiting response... 200 OK
Length: 38598274 (37M) [application/x-bzip2]
Saving to: 'nextcloud-11.0.2.tar.bz2'

nextcloud-11.0.2.tar.bz2     100%[=============================================>]  36.81M  9.37MB/s    in 5.4s

2017-04-05 19:36:16 (6.88 MB/s) - 'nextcloud-11.0.2.tar.bz2' saved [38598274/38598274]
```

Puis nous devons la désarchiver.

> tar xfvj nextcloud-11.0.2.tar.bz2

Ensuite, nous déplaçons le dossier `nextcloud` à l'espace prévu par notre serveur web.

> mv nextcloud/ /var/www/htdocs/

Puis nous changeons les permissions sur ce dossier.

> chown -R www:daemon /var/www/htdocs/nextcloud

Pour fonctionner, NextCloud a besoin de certaines dépendances PHP. Nous allons donc procéder à leur installation.

> pkg_add -i php-zip php-gd php-curl php-intl php-bz2 pecl-redis icu4c

```
# pkg_add -i php-zip-7.0.8p0 php-gd-7.0.8p0 php-curl-7.0.8p0
quirks-2.241 signed on 2016-07-26T16:56:10Z
php-zip-7.0.8p0: ok
php-gd-7.0.8p0:png-1.6.23: ok
php-gd-7.0.8p0:jpeg-1.5.0p0v0: ok
Can't install php-gd-7.0.8p0 because of libraries
|library X11.16.1 not found
| not found anywhere
|library Xpm.9.0 not found
| not found anywhere
|library freetype.25.0 not found
| not found anywhere
Direct dependencies for php-gd-7.0.8p0 resolve to png-1.6.23 php-7.0.8p0 jpeg-1.5.0p0v0
Full dependency tree is jpeg-1.5.0p0v0 femail-1.0p1 libxml-2.9.3 php-7.0.8p0 xz-5.2.2p0 femail-chroot-1.0p2 gettext-0.19.7 png-1.6.23 libiconv-1.14p3
php-curl-7.0.8p0:nghttp2-1.12.0: ok
php-curl-7.0.8p0:curl-7.49.0: ok
php-curl-7.0.8p0: ok
```

Problème ! Nous constatons que certaines librairies sont manquantes. Il s'agit des sets X qui n'ont pas été installé lors de l'installation du système d'exploitation. Pour résoudre ce problème se rendre sur [cette page](https://github.com/dodoritfort/OpenBSD/wiki/Installer-les-headers-X-manquants).

Une fois le set X installé, on peut retenter l'installation de php-gd.

> pkg_add -i php-gd-7.0.8p0

```
# pkg_add -i php-gd-7.0.8p0
quirks-2.241 signed on 2016-07-26T16:56:10Z
php-gd-7.0.8p0: ok
```

Parfait !

Nous activons ensuite ces extensions.

> cp /etc/php-7.0.sample/* /etc/php-7.0/

Voici le résultat de cette copie.

> ls -l /etc/php-7.0/

```
# ls -l /etc/php-7.0/
total 16
-rw-r--r--  1 root  wheel  18 Mar 26 19:53 curl.ini
-rw-r--r--  1 root  wheel  16 Mar 26 19:53 gd.ini
-rw-r--r--  1 root  wheel  26 Mar 26 19:53 opcache.ini
-rw-r--r--  1 root  wheel  17 Mar 26 19:53 zip.ini
```

Une chose importante avant de continuer mais surtout avant d'utiliser NextCloud... Il faut modifier le fichier de configuration PHP afin de lui signaler que nous voulons augmenter la taille maximale possible des fichiers en upload.

> nano /etc/php-7.0.ini

Puis nous modifions les lignes `post_max_size` et `upload_max_filesize` tel que ci-dessous afin d'augmenter ce volume à 5Go.

```
post_max_size = 5000M
upload_max_filesize = 5000M
```

Documentation : [https://docs.nextcloud.com/server/9/admin_manual/configuration_files/big_file_upload_configuration.html](https://docs.nextcloud.com/server/9/admin_manual/configuration_files/big_file_upload_configuration.html)

Puis nous redémarrons le service PHP.

> rcctl restart php70_fpm

```
# rcctl restart php70_fpm
php70_fpm(ok)
php70_fpm(ok)
```

Enfin, nous modifions le fichier de configuration du serveur httpd pour lui spécifier que notre cloud doit pouvoir accepter des upload à hauteur de 513Mb maximum.

> nano /etc/httpd.conf

```
server "cloud.votredomaine.fr" {
        listen on * port 80
        block return 301 "https://$SERVER_NAME$REQUEST_URI"
}

server "cloud.votredomaine.fr" {
        listen on $ext_addr tls port 443
        root "/htdocs/nextcloud/"
        directory index index.php
        tls {
                key "/etc/letsencrypt/live/cloud.votredomaine.fr/privkey.pem"
                certificate "/etc/letsencrypt/live/cloud.votredomaine.fr/fullchain.pem"
        }
        hsts

        # Set max upload size to 513M (in bytes)
        connection max request body 537919488

        # First deny access to the specified files
        location "/db_structure.xml" { block }
        location "/.ht*"             { block }
        location "/README"           { block }
        location "/data*"            { block }
        location "/config*"          { block }

        location "/*.php*" {
                fastcgi socket "/run/php-fpm.sock"
        }
}
```

On vérifie la configuration, on al recharge et enfin on redémarre.

> httpd -n

> rcctl reload httpd

> rcctl restart httpd

Rendons-nous ensuite à l'adresse de notre serveur pour la configuration de NextCloud.

`http://cloud.votredomaine.fr/`

### **Retrouvez plus d'articles sur http://wiki.dodoritfort.xyz/**
