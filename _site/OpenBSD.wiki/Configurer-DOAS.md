# Configurer DOAS

### **Retrouvez plus d'articles sur http://wiki.dodoritfort.xyz/**

Créer le fichier de configuration `doas.conf`.

> nano /etc/doas.conf

Et y entrer la configuration suivante.

`permit keepenv { PKG_PATH ENV PS1 SSH_AUTH_SOCK } :wheel`

Se déconnecter de la session root pour revenir à un utilisateur standard.

Puis vérifier que cela fonctionne en tapant par exemple la commande de mise à jour des paquets. Normalement, le système demandera le mot de passe root.

> doas pkg_add -uvi

```
# doas pkg_add -uvi
doas: keepenv with list is obsolete
doas (root@openbsd) password:
Update candidates: quirks-2.241 -> quirks-2.241
quirks-2.241 signed on 2016-07-26T16:56:10Z
Update candidates: gettext-0.19.7 -> gettext-0.19.7
Update candidates: libiconv-1.14p3 -> libiconv-1.14p3
Update candidates: libidn-1.32p1 -> libidn-1.32p1
Update candidates: lua-5.1.5p6 -> lua-5.1.5p6
Update candidates: luadbi-0.5p0 -> luadbi-0.5p0
Update candidates: luaexpat-1.3.0p0 -> luaexpat-1.3.0p0
Update candidates: luafs-1.6.3p0 -> luafs-1.6.3p0
Update candidates: luasec-0.6p0 -> luasec-0.6p0
Update candidates: luasocket-3.0rc1p1 -> luasocket-3.0rc1p1
Update candidates: luazlib-20100731p2 -> luazlib-20100731p2
Update candidates: nano-2.6.0 -> nano-2.6.0
Update candidates: prosody-0.9.10p2 -> prosody-0.9.10p2
```

### **Retrouvez plus d'articles sur http://wiki.dodoritfort.xyz/**