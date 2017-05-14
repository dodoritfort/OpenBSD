---
layout: page
title: HTTPD   Exemple(s)
wikiPageName: HTTPD---Exemple(s)
menu: wiki
---

# HTTPD - Exemple(s)

### **Retrouvez plus d'articles sur http://wiki.dodoritfort.xyz/**

> nano /etc/httpd.conf

```
# $OpenBSD: httpd.conf,v 1.14 2015/02/04 08:39:35 florian Exp $

############
#  Macros  #
############
ext_addr="*"

#############
#  Servers  #
#############
server "cloud.votredomaine.fr" {
        listen on * port 80
        block return 301 "https://$SERVER_NAME$REQUEST_URI"
        log access "cloud.log"
}

server "cloud.votredomaine.fr" {
        listen on $ext_addr tls port 443
        root "/htdocs/cloud/"
        directory index index.php
        hsts
        tls {
                key "/etc/letsencrypt/live/cloud.votredomaine.fr/privkey.pem"
                certificate "/etc/letsencrypt/live/cloud.votredomaine.fr/fullchain.pem"
        }
        log access "cloud.log"
        log error "cloud.log"

        # Set max upload size to 3G (in bytes)
        connection max request body 3145728000

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

#####################################################
#  Include MIME types instead of the built-in ones  #
#####################################################
types {
        include "/usr/share/misc/mime.types"
}
```

Documentation : [http://man.openbsd.org/httpd.conf](http://man.openbsd.org/httpd.conf)

### **Retrouvez plus d'articles sur http://wiki.dodoritfort.xyz/**
