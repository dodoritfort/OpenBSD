---
layout: page
title: Installer le serveur VPN OpenVPN
wikiPageName: Installer-le-serveur-VPN-OpenVPN
menu: wiki
---

# Installer le serveur VPN OpenVPN

### **Retrouvez plus d'articles sur http://wiki.dodoritfort.xyz/**

> pkg_add -i openvpn openvpn_bsdauth easy-rsa

```
# pkg_add -i openvpn openvpn_bsdauth easy-rsa
quirks-2.241 signed on 2016-07-26T16:56:10Z
openvpn-2.3.11:lzo2-2.09: ok
openvpn-2.3.11: ok
openvpn_bsdauth-7p0: ok
easy-rsa-3.0.1:openssl-1.0.2h: ok
easy-rsa-3.0.1: ok
Look in /usr/local/share/doc/pkg-readmes for extra documentation.
--- +openssl-1.0.2h -------------------
You may wish to add /usr/local/lib/eopenssl/man to /etc/man.conf
```

> install -m 700 -d /etc/openvpn/private

> install -m 755 -d /etc/openvpn/certs

> install -m 755 -d /var/log/openvpn

> mkdir -p /tmp/openvpn

> cd /tmp/openvpn

> cp -r /usr/local/share/easy-rsa/* .

> cp vars.example vars

> nano vars

```
set_var EASYRSA_REQ_COUNTRY     "FR"
set_var EASYRSA_REQ_PROVINCE    "France"
set_var EASYRSA_REQ_CITY        "Roubaix"
set_var EASYRSA_REQ_ORG         "votredomaine.fr"
set_var EASYRSA_REQ_EMAIL       "openvpn@votredomaine.fr"
set_var EASYRSA_REQ_OU          "votredomaine.fr"

set_var EASYRSA_KEY_SIZE        4096
```

> ./easyrsa init-pki

```
# ./easyrsa init-pki

Note: using Easy-RSA configuration from: ./vars

init-pki complete; you may now create a CA or requests.
Your newly created PKI dir is: /tmp/openvpn/pki
```

> ./easyrsa build-ca nopass

```
# ./easyrsa build-ca nopass

Note: using Easy-RSA configuration from: ./vars
Generating a 4096 bit RSA private key
................................++
........................++
writing new private key to '/tmp/openvpn/pki/private/ca.key.vzoRmOw16U'
-----
You are about to be asked to enter information that will be incorporated
into your certificate request.
What you are about to enter is what is called a Distinguished Name or a DN.
There are quite a few fields but you can leave some blank
For some fields there will be a default value,
If you enter '.', the field will be left blank.
-----
Common Name (eg: your user, host, or server name) [Easy-RSA CA]:

CA creation complete and you may now import and sign cert requests.
Your new CA certificate file for publishing is at:
/tmp/openvpn/pki/ca.crt
```

> ./easyrsa build-server-full server nopass

```
# ./easyrsa build-server-full server nopass

Note: using Easy-RSA configuration from: ./vars
Generating a 4096 bit RSA private key
................................................................++
........................................................................................................................++
writing new private key to '/tmp/openvpn/pki/private/server.key.ZwVuZpJIRM'
-----
Using configuration from /tmp/openvpn/openssl-1.0.cnf
Check that the request matches the signature
Signature ok
The Subject's Distinguished Name is as follows
commonName            :ASN.1 12:'server'
Certificate is to be certified until Apr  3 17:01:11 2027 GMT (3650 days)

Write out database with 1 new entries
Data Base Updated
```

> ./easyrsa gen-dh

```
# ./easyrsa gen-dh

Note: using Easy-RSA configuration from: ./vars
Generating DH parameters, 4096 bit long safe prime, generator 2
This is going to take a long time
...............................+.............
.............................................
...........+................................ 
DH parameters of size 4096 created at /tmp/openvpn/pki/dh.pem
```

> ./easyrsa gen-crl

```
# ./easyrsa gen-crl

Note: using Easy-RSA configuration from: ./vars
Using configuration from /tmp/openvpn/openssl-1.0.cnf

An updated CRL has been created.
CRL file: /tmp/openvpn/pki/crl.pem
```

> chown -R _openvpn:wheel pki/*

> chmod -R 600 pki/*

> cd /etc/openvpn

> cp -p /tmp/openvpn/pki/ca.crt certs/ca.crt

> cp -p /tmp/openvpn/pki/issued/server.crt certs/server.crt

> cp -p /tmp/openvpn/pki/private/server.key private/server.key

> cp -p /tmp/openvpn/pki/dh.pem dh.pem

> cp -p /tmp/openvpn/pki/crl.pem crl.pem

> rm -rf /tmp/openvpn

> nano /etc/openvpn/private/mgmt.pwd

```Votre_mot_de_passe```

> chown root:wheel /etc/openvpn/private/mgmt.pwd

> chmod 600 /etc/openvpn/private/mgmt.pwd

> nano /etc/openvpn/server.conf

```
# configuration reseau
dev tun0
server 10.8.0.0 255.255.255.0
push "redirect-gateway def1"
# On propose le resolveur de FDN
# https://www.fdn.fr/actions/dns/
push "dhcp-option DNS 80.67.169.12"
# Si le serveur a son propre resolveur
# push "dhcp-option DNS 10.8.0.1"

# les certificats
ca /etc/openvpn/certs/ca.crt
cert /etc/openvpn/certs/server.crt
key /etc/openvpn/private/server.key
dh /etc/openvpn/dh.pem
crl-verify /etc/openvpn/crl.pem

# options diverses
comp-lzo
daemon openvpn
float
group _openvpn
user _openvpn
ifconfig-pool-persist /var/openvpn/ipp.txt
keepalive 10 120
management 127.0.0.1 1195 /etc/openvpn/private/mgmt.pwd
max-clients 100
persist-key
persist-tun
port 1194
proto udp

# authentification
client-cert-not-required
username-as-common-name
script-security 3 system
auth-user-pass-verify /usr/local/libexec/openvpn_bsdauth via-env
auth-nocache

# journaux
log-append  /var/log/openvpn/openvpn.log
status /var/log/openvpn/openvpn-status.log
verb 3
```

> chown root:_openvpn /etc/openvpn/server.conf

> chmod 640 /etc/openvpn/server.conf

> nano /etc/pf.conf

```
tun_if = "tun0"
pass out on $ext_if from 10.8.0.0/24 to any nat-to ($ext_if)
pass in quick on $tun_if keep state
```

> pfctl -d

> pfctl -ef /etc/pf.conf

> nano /etc/sysctl.conf

```
net.inet.ip.forwarding=1
```

> sysctl net.inet.ip.forwarding=1

> useradd -G _openvpnusers utilisateur

> passwd utilisateur

> nano /etc/hostname.tun0

```
up
!/usr/local/sbin/openvpn --config /etc/openvpn/server.conf
```

> sh /etc/netstart

> tail -f /var/log/openvpn/*

> kill -9 $(pgrep openvpn)

> cat /etc/openvpn/certs/ca.crt

```
-----BEGIN CERTIFICATE-----
MIIDQTCCAimgAwIBAgIJAOwa/Yq2rNGJMA0GCSqGSIb3DQEBCwUAMBoxGDAWBgNV
..........
OxjoYBwhWgWq/avx6qprB/HklcI6O7tjApGgbgSZNcTNrqu17xUftCxKthgqQfzt
TTzpUC8e3es1Dvrq+pYs4wYY3mqp
-----END CERTIFICATE-----
```

Côté client (Windows 7), télécharger et installer OpenVPN sur le site officiel : [https://openvpn.net/index.php/open-source/downloads.html](https://openvpn.net/index.php/open-source/downloads.html)

Créer un dossier et créer dedans un fichier `votredomaine.fr.ovpn` avec le contenu suivant.

```
client
dev tun0
proto udp
remote 5.39.83.4 1194
ca [inline]
auth-user-pass
comp-lzo
auth-nocache

<ca>
-----BEGIN CERTIFICATE-----
MIIDQTCCAimgAwIBAgIJAOwa/Yq2rNGJMA0GCSqGSIb3DQEBCwUAMBoxGDAWBgNV
..........
OxjoYBwhWgWq/avx6qprB/HklcI6O7tjApGgbgSZNcTNrqu17xUftCxKthgqQfzt
TTzpUC8e3es1Dvrq+pYs4wYY3mqp
-----END CERTIFICATE-----
</ca>

auth-user-pass user-pass.txt
```
Dans ce même dossier, créer un second fichier nommé `user-pass.txt` contenant le compte utilisateur créé ci-dessus.

```
monuservpn
monpasswordvpn
```

Importer ensuite la configuration dans OpenVPN GUI.

### **Retrouvez plus d'articles sur http://wiki.dodoritfort.xyz/**
