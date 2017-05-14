# Monter les partitions OpenBSD sous FreeBSD11.0-Rescue

Nous devons commencer par récupérer le nom de notre disque-dur.

> ls /dev/ada*

```
root@rescue-bsd:~ # ls /dev/ada*
/dev/ada0       /dev/ada0s4
```

Il s'agit donc de **ada0s4**.

Nous modifions le type de _filesystem_.

> fsck -t ffs /dev/ada0s4

```
root@rescue-bsd:~ # fsck -t ffs /dev/ada0s4
** /dev/ada0s4
** Last Mounted on /
** Phase 1 - Check Blocks and Sizes
** Phase 2 - Check Pathnames
** Phase 3 - Check Connectivity
** Phase 4 - Check Reference Counts
** Phase 5 - Check Cyl groups
SUMMARY INFORMATION BAD
SALVAGE? [yn] y

1789 files, 32005 used, 482434 free (66 frags, 60296 blocks, 0.0% fragmentation)

***** FILE SYSTEM MARKED CLEAN *****

***** FILE SYSTEM WAS MODIFIED *****
```

Puis nous n'avons plus qu'à monter notre disque comme si de rien n'était.

> mount /dev/ada0s4 /mnt/

Et on vérifie que tout a bien fonctionné.

> ls -l /mnt/

```
root@rescue-bsd:~ # ls -l /mnt/
total 38818
-rw-r--r--   1 root  wheel       578 Jul 26  2016 .cshrc
-rw-r--r--   1 root  wheel       468 Jul 26  2016 .profile
drwxr-xr-x   2 root  wheel       512 Jul 26  2016 altroot
drwxr-xr-x   2 root  wheel      1024 Jul 26  2016 bin
-rw-r--r--   1 root  wheel     70980 Mar 26 08:54 boot
-rw-r--r--   1 root  wheel  10554411 Mar 26 08:58 bsd
-rw-r--r--   1 root  wheel  10554411 Mar 26 08:42 bsd.mp
-rw-r--r--   1 root  wheel   7905392 Mar 26 08:42 bsd.rd
-rw-r--r--   1 root  wheel  10504752 Mar 26 08:42 bsd.sp
drwxr-xr-x   3 root  wheel     19456 Mar 26 19:57 dev
drwxr-xr-x  26 root  wheel      2048 Apr  4 18:08 etc
drwxr-xr-x   2 root  wheel       512 Mar 26 08:38 home
drwxr-xr-x   2 root  wheel       512 Jul 26  2016 mnt
drwx------   2 root  wheel       512 Mar 26 19:51 root
drwxr-xr-x   2 root  wheel      1536 Jul 26  2016 sbin
lrwxr-xr-x   1 root  wheel        11 Jul 26  2016 sys -> usr/src/sys
drwxr-xr-x   2 root  wheel       512 Mar 26 08:38 tmp
drwxr-xr-x   2 root  wheel       512 Mar 26 08:38 usr
drwxr-xr-x   2 root  wheel       512 Mar 26 08:38 var
```

Bingo !

Documentation : [http://wiki.euserv.com/index.php/Manual_FreeBSD_Rescue_System/en](http://wiki.euserv.com/index.php/Manual_FreeBSD_Rescue_System/en)