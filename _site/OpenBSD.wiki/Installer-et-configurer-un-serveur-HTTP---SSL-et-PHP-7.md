# Installer et configurer un serveur HTTP + SSL et PHP 7

### **Retrouvez plus d'articles sur http://wiki.dodoritfort.xyz/**

De base, OpenBSD embarque HTTPD en tant que serveur web et SQLite comme serveur de base de données.

Nous allons donc procéder à l'installation de PHP 7.

> pkg_add -i php-7.0.8p0

```
# pkg_add -i php-7.0.8p0
quirks-2.241 signed on 2016-07-26T16:56:10Z
php-7.0.8p0:xz-5.2.2p0: ok
php-7.0.8p0:libxml-2.9.3: ok
php-7.0.8p0:femail-1.0p1: ok
php-7.0.8p0:femail-chroot-1.0p2: ok
php-7.0.8p0: ok
The following new rcscripts were installed: /etc/rc.d/php70_fpm
See rcctl(8) for details.
Look in /usr/local/share/doc/pkg-readmes for extra documentation.
```

Puis nous l'activons et le démarrons.

> rcctl enable php70_fpm

> rcctl start php70_fpm

Nous allons commencer copier un exemple de fichier de configuration.

> cp /etc/examples/httpd.conf /etc/httpd.conf

Puis nous le modifions selon nos besoins.

> nano /etc/httpd.conf

```
# $OpenBSD: httpd.conf,v 1.14 2015/02/04 08:39:35 florian Exp $

#
# Macros
#
ext_addr="*"

#
# Global Options
#
# prefork 3

#
# Servers
#

server "cloud.votredomaine.fr" {
        listen on $ext_addr port 80
        root "/htdocs/cloud/"
        directory index index.php

        location "/*.php*" {
                fastcgi socket "/run/php-fpm.sock"
        }
}

# Include MIME types instead of the built-in ones
types {
        include "/usr/share/misc/mime.types"
}
#
```

Ensuite, on vérifie que la configuration est OK.

> httpd -n

```
# httpd -n
configuration OK
```

Nous devons bien entendu créer le répertoire /var/www/htdocs/cloud au vue de la configuration ci-dessus.

> mkdir /var/www/htdocs/cloud

N'oublions pas non plus de créer un fichier index.php afin que par la suite nous puissions générer notre certificat SSL.

> nano /var/www/htdocs/cloud/index.php

```
<?php echo("test"); ?>
```

Et attribuer ce dossier à son propriétaire.

> chown -R www:daemon /var/www/htdocs/cloud

Nous pouvons à présent activer et démarrer le service `httpd`.

> rcctl enable httpd

> rcctl start httpd

Nous installons ensuite `certbot` afin de générer un certificat SSL.

> pkg_add -i certbot

```
# pkg_add -i certbot
quirks-2.241 signed on 2016-07-26T16:56:10Z
certbot-0.8.1:dialog-1.2.20150920p0: ok
certbot-0.8.1:libffi-3.2.1p2: ok
certbot-0.8.1:python-2.7.12: ok
certbot-0.8.1:py-python2-pythondialog-3.3.0p0: ok
certbot-0.8.1:py-setuptools-18.2p0v0: ok
certbot-0.8.1:py-psutil-4.1.0: ok
certbot-0.8.1:py-asn1-0.1.9p0v0: ok
certbot-0.8.1:py-idna-2.1: ok
certbot-0.8.1:py-ipaddress-1.0.16: ok
certbot-0.8.1:py-cparser-2.10p1: ok
certbot-0.8.1:py-cffi-1.4.2: ok
certbot-0.8.1:py-enum34-1.1.6: ok
certbot-0.8.1:py-six-1.10.0: ok
certbot-0.8.1:py-cryptography-1.4: ok
certbot-0.8.1:py-openssl-0.15.1p1: ok
certbot-0.8.1:py-ndg-httpsclient-0.4.0p0: ok
certbot-0.8.1:py-werkzeug-0.11.10: ok
certbot-0.8.1:py-tz-2016.4: ok
certbot-0.8.1:py-pyRFC3339-1.0p0: ok
certbot-0.8.1:py-requests-2.10.0: ok
certbot-0.8.1:py-acme-0.8.1: ok
certbot-0.8.1:py-ConfigArgParse-0.10.0p0: ok
certbot-0.8.1:py-parsedatetime-2.1: ok
certbot-0.8.1:py-configobj-5.0.6p0: ok
certbot-0.8.1:py-zopeevent-4.1.0p0: ok
certbot-0.8.1:py-zopeinterface-4.1.3p0: ok
certbot-0.8.1:py-pbr-1.8.1: ok
certbot-0.8.1:py-funcsigs-1.0.2: ok
certbot-0.8.1:py-mock-2.0.0: ok
certbot-0.8.1:py-zopecomponent-4.2.2p0: ok
certbot-0.8.1: ok
--- +python-2.7.12 -------------------
If you want to use this package as your default system python, as root
create symbolic links like so (overwriting any previous default):
 ln -sf /usr/local/bin/python2.7 /usr/local/bin/python
 ln -sf /usr/local/bin/python2.7-2to3 /usr/local/bin/2to3
 ln -sf /usr/local/bin/python2.7-config /usr/local/bin/python-config
 ln -sf /usr/local/bin/pydoc2.7  /usr/local/bin/pydoc
```

Nous avons les outils pour générer notre premier certificat.

> certbot certonly --webroot --rsa-key-size 4096 -w /var/www/htdocs/cloud -d cloud.votredomaine.fr

```
# certbot certonly --webroot --rsa-key-size 4096 -w /var/www/htdocs/cloud -d cloud.votredomaine.fr

IMPORTANT NOTES:
 - Congratulations! Your certificate and chain have been saved at
   /etc/letsencrypt/live/cloud.votredomaine.fr/fullchain.pem. Your
   cert will expire on 2017-07-04. To obtain a new or tweaked version
   of this certificate in the future, simply run certbot again. To
   non-interactively renew *all* of your certificates, run "certbot
   renew"
 - If you like Certbot, please consider supporting our work by:

   Donating to ISRG / Let's Encrypt:   https://letsencrypt.org/donate
   Donating to EFF:                    https://eff.org/donate-le
```

Nous arrêtons notre service httpd pour pouvoir modifier notre fichier de configuration.

> rcctl stop httpd

Et on modifie le fichier de configuration de HTTPD.

> nano /etc/httpd.conf

```
# $OpenBSD: httpd.conf,v 1.14 2015/02/04 08:39:35 florian Exp $

#
# Macros
#
ext_addr="*"

#
# Global Options
#
# prefork 3

#
# Servers
#
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

        location "/*.php*" {
                fastcgi socket "/run/php-fpm.sock"
        }
}

# Include MIME types instead of the built-in ones
types {
        include "/usr/share/misc/mime.types"
}
```

Au vue des modifications, une petite vérification de configuration s'impose !

> httpd -n

Si tout est ok, nous pouvons relancer notre service httpd.

> rcctl start httpd

Et voilà, le tour est joué !

### **Retrouvez plus d'articles sur http://wiki.dodoritfort.xyz/**