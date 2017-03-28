# Installer OpenBSD sur votre serveur Kimsufi

## Etape 1 : Installer le système d'exploitation et obtenir des informations

Une fois notre serveur Kimsufi en poche, nous devons commencer par installer un système d’exploitation. 
Nous choisirons donc d’installer **Debian 7.10 (Wheezy) (oldstable) 64 bits**. Une fois l'installation terminée, nous recevons un mail avec les identifiants de connexion SSH.

Nous nous connectons ensuite à notre serveur en SSH à l'aide du logiciel PuTTY.

```
login as: root
root@5.39.xx.xx's password:
Debian GNU/Linux 7.11

Linux ns30xxxxx.ip-5-39-xx.eu 3.14.32-xxxx-grs-ipv6-64 #9 SMP Thu Oct 20 14:53:52 CEST 2016 x86_64 GNU/Linux

server    : 27xxxx
hostname  : ns30xxxxx.ip-5-39-xx.eu
eth0 IPv4 : 5.39.xx.xx
eth0 IPv6 : 2001:41d0:x:xxxx::1/128
Last login: Thu Mar 16 16:16:21 2017 from cache-ng.ovh.net
```

Nous récupérons les informations concernant le processeur, l'objectif étant de vérifier si notre serveur utilise un processeur mono ou double coeurs.

> dmesg | grep -i ^cpu

```
root@ns30xxxxx:~# dmesg | grep -i ^cpu
CPU: Physical Processor ID: 0
CPU: Processor Core ID: 0
CPU0: Thermal monitoring enabled (TM2)
cpuidle: using governor ladder
cpuidle: using governor menu
```

Ensuite, récupérons les informations concernant l'adressage réseau.

> ifconfig

```
root@ns30xxxxx:~# ifconfig
eth0      Link encap:Ethernet  HWaddr 00:22:4d:a1:c9:47
          inet addr:5.39.xx.xx  Bcast:5.39.83.255  Mask:255.255.255.0
          inet6 addr: 2001:41d0:x:xxxx::1/128 Scope:Global
          inet6 addr: fe80::222:xxxx:xxxx:xxxx/64 Scope:Link
          UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
          RX packets:51277 errors:0 dropped:0 overruns:0 frame:0
          TX packets:27385 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1000
          RX bytes:69740971 (66.5 MiB)  TX bytes:2887482 (2.7 MiB)
          Interrupt:16 Memory:80400000-80420000

lo        Link encap:Local Loopback
          inet addr:127.0.0.1  Mask:255.0.0.0
          inet6 addr: ::1/128 Scope:Host
          UP LOOPBACK RUNNING  MTU:65536  Metric:1
          RX packets:465 errors:0 dropped:0 overruns:0 frame:0
          TX packets:465 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:0
          RX bytes:54882 (53.5 KiB)  TX bytes:54882 (53.5 KiB)
```

Et enfin les nameserver.

> cat /etc/resolv.conf

```
root@ns30xxxxx:~# cat /etc/resolv.conf
nameserver 127.0.0.1
nameserver 213.186.33.99
search ovh.net
```

## Etape 2 : Préparation de l'installation

Après quoi nous devons nous rendre sur notre interface client Kimsufi et paramétrer notre serveur pour qu'il démarre en mode rescue. Pour cela il suffit de cliquer sur le bouton **Netboot**, puis sur **Rescue** et enfin de sélectionner **rescue64-pro**. Valider en cliquant sur le bouton **Suivant**.

Pour finir, nous cliquons sur le bouton **Redémarrer** pour faire un redémarrage à froid. L'opération prend quelques secondes/minutes.

Lorsque le serveur a démarré en mode rescue, nous recevons à nouveau un mail avec les identifiants de connexion SSH. Nous les utilisons pour nous connecter à nouveau à notre serveur.

```
login as: root
root@5.39.xx.xx's password:

The programs included with the Debian GNU/Linux system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Debian GNU/Linux comes with ABSOLUTELY NO WARRANTY, to the extent
permitted by applicable law.

root@rescue:~#
```

Nous allons créer un dossier /tmp et télécharger l'image _miniroot60.fs_ sur le site officiel de l'éditeur.

> wget -P /tmp/ http://ftp.fr.openbsd.org/pub/OpenBSD/6.0/amd64/miniroot60.fs

```
root@rescue:~# wget -P /tmp/ http://ftp.fr.openbsd.org/pub/OpenBSD/6.0/amd64/miniroot60.fs
--2017-03-16 17:21:30--  http://ftp.fr.openbsd.org/pub/OpenBSD/6.0/amd64/miniroot60.fs
Resolving ftp.fr.openbsd.org (ftp.fr.openbsd.org)... 145.238.209.46
Connecting to ftp.fr.openbsd.org (ftp.fr.openbsd.org)|145.238.209.46|:80... conn                                                                                                                                                             ected.
HTTP request sent, awaiting response... 200 OK
Length: 4063232 (3.9M) [application/octet-stream]
Saving to: ‘/tmp/miniroot60.fs’

/tmp/miniroot60.fs  100%[=====================>]   3.88M  10.1MB/s   in 0.4s

2017-03-16 17:21:30 (10.1 MB/s) - ‘/tmp/miniroot60.fs’ saved [4063232/4063232]
```

Ensuite nous téléchargeons la signature SHA256.

> wget -P /tmp/ http://ftp.fr.openbsd.org/pub/OpenBSD/6.0/amd64/SHA256

```
root@rescue:~# wget -P /tmp/ http://ftp.fr.openbsd.org/pub/OpenBSD/6.0/amd64/SHA256
--2017-03-16 17:21:53--  http://ftp.fr.openbsd.org/pub/OpenBSD/6.0/amd64/SHA256
Resolving ftp.fr.openbsd.org (ftp.fr.openbsd.org)... 145.238.209.46
Connecting to ftp.fr.openbsd.org (ftp.fr.openbsd.org)|145.238.209.46|:80... connected.
HTTP request sent, awaiting response... 200 OK
Length: 1989 (1.9K) [application/octet-stream]
Saving to: ‘/tmp/SHA256’

/tmp/SHA256         100%[=====================>]   1.94K  --.-KB/s   in 0.03s

2017-03-16 17:21:53 (59.5 KB/s) - ‘/tmp/SHA256’ saved [1989/1989]
```

Puis on compare la signature de l'archive et celle téléchargée. Si les deux sont identiques, c'est tout bon !

> sha256sum /tmp/miniroot60.fs; grep miniroot60.fs /tmp/SHA256 | cut -d ' ' -f 4

```
root@rescue:~# sha256sum /tmp/miniroot60.fs; grep miniroot60.fs /tmp/SHA256 | cut -d ' ' -f 4
a6ff63e72ed9f45a211b0a57fb566e6f674242988e369209cd6b2a205d8bcec1  /tmp/miniroot6                                                                                                                                                             0.fs
a6ff63e72ed9f45a211b0a57fb566e6f674242988e369209cd6b2a205d8bcec1
```

Nous nous rendons dans le dossier _/tmp_.

> cd /tmp

```
root@rescue:~# cd /tmp
```

Nous lançons une machine virtuelle à l'aide de QEmu.

> qemu-system-x86_64 -curses -drive file=miniroot60.fs -drive file=/dev/sda -net nic -net user

```
root@rescue:/tmp# qemu-system-x86_64 -curses -drive file=miniroot60.fs -drive file=/dev/sda -net nic -net user
```

Parfait ! L'installation de OpenBSD commence...

## Etape 3 : Installation de OpenBSD

```
Welcome to the OpenBSD/amd64 6.0 installation program.
(I)nstall, (U)pgrade, (A)utoinstall or (S)hell? i
At any prompt except password prompts you can escape to a shell by
typing '!'. Default answers are shown in []'s and are selected by
pressing RETURN.  You can exit this program at any time by pressing
Control-C, but this can leave your system in an inconsistent state.
```

Ici nous choisissons le _keyboard_ que nous souhaitons.

> L

> us

```
Choose your keyboard layout ('?' or 'L' for list) [default] L
Available layouts: be br cf de dk es fr hu is it jp la lt lv nl no pl pt ru sf s
g si sv tr ua uk us
Choose your keyboard layout ('?' or 'L' for list) [default] us
kbd: keyboard mapping set to us
```

Nous définissons ensuite le nom d'hôte.

```
System hostname? (short form, e.g. 'foo') hostname
```

Puis nous configurons le réseau en DHCP.

> em0

> dhcp

> none

> done

```
Available network interfaces are: em0 vlan0.
Which network interface do you wish to configure? (or 'done') [em0]
IPv4 address for em0? (or 'dhcp' or 'none') [dhcp]
DHCPDISCOVER on em0 - interval 1
DHCPOFFER from 10.0.2.2 (52:55:0a:00:02:02)
DHCPREQUEST on em0 to 255.255.255.255
DHCPACK from 10.0.2.2 (52:55:0a:00:02:02)
bound to 10.0.2.15 -- renewal in 43200 seconds.
IPv6 address for em0? (or 'rtsol' or 'none') [none]
Available network interfaces are: em0 vlan0.
Which network interface do you wish to configure? (or 'done') [done]
Using DNS domainname my.domain
Using DNS nameservers at 10.0.2.3
```

Spécification du mot de passe root. Idéalement, un minimum de 12 caractères avec des chiffres et des lettres puis au moins un caractère spécial.

```
Password for root account? (will not echo)
Password for root account? (again)
```

Nous devons bien entendu démarrer le service _sshd_ par défaut. Sinon il ne nous sera pas possible de nous connecter à notre serveur.

Nous n'avons pas l'utilité du service _X Window System_ du fait que le serveur sera uniquement piloté par le mode console. En revanche, pour les personnes souhaitant avoir un _remote desktop_, il faudra activer cette option. Cependant pas de panique, il sera toujours possible de le faire à posteriori.

> yes

> no

> no

```
Start sshd(8) by default? [yes]
Do you expect to run the X Window System? [yes] no
Change the default console to com0? [no]
```

Etape importante ! Il faut configurer un utilisateur, celui-ci servira à se connecter à distance à notre serveur. Pour des raisons de sécurité il n'est pas conseillé de permettre à l'utilisateur root de se connecter en SSH. Si tel était le cas, les attaquants pourraient simplement faire du _bruteforce_ via ce compte.

```
Setup a user? (enter a lower-case loginname, or 'no') [no] username
Full name for user username? [username]
Password for user username? (will not echo)
Password for user username? (again)
```

Comme dit plus haut, nous ne souhaitons pas que l'utilisateur root puisse se connecter en SSH. Beaucoup trop dangereux.

> no

```
WARNING: root is targeted by password guessing attacks, pubkeys are safer.
Allow root ssh login? (yes, no, prohibit-password) [no]
```

Enfin, la configuration de la _timezone_.

> Europe/Paris

```
What timezone are you in? ('?' for list) [Europe/Paris]
```

A présent, il est temps de configurer le partitionnement sur le disque-dur. Nous allons donc lister les disque-durs du serveur et choisir celui de notre choix. Dans notre exemple, il s'agira de **wd1**. Ensuite, nous utiliserons le **(W)hole disk** et nous ferons un **(A)uto layout**.

L'opération prend un certain temps...

> ?

> wd1

> w

> a

> done

```
Available disks are: wd0 wd1.
Which disk is the root disk? ('?' for details) [wd0] ?
wd0: QEMU HARDDISK (0.0G)
wd1: QEMU HARDDISK (465.8G)
Available disks are: wd0 wd1.
Which disk is the root disk? ('?' for details) [wd0] wd1
No valid MBR or GPT.
Use (W)hole disk MBR, whole disk (G)PT or (E)dit? [whole] w
Setting OpenBSD MBR partition to whole wd1...done.
The auto-allocated layout for wd1 is:
#                size           offset  fstype [fsize bsize  cpg]
  a:          1024.0M               64  4.2BSD   2048 16384    1 # /
  b:           223.8M          2097216    swap
  c:        476940.0M                0  unused
  d:          4096.0M          2555456  4.2BSD   2048 16384    1 # /tmp
  e:          4319.8M         10944064  4.2BSD   2048 16384    1 # /var
  f:          2048.0M         19790912  4.2BSD   2048 16384    1 # /usr
  g:          1024.0M         23985216  4.2BSD   2048 16384    1 # /usr/X11R6
  h:         10240.0M         26082368  4.2BSD   2048 16384    1 # /usr/local
  i:          2048.0M         47053888  4.2BSD   2048 16384    1 # /usr/src
  j:          2048.0M         51248192  4.2BSD   2048 16384    1 # /usr/obj
  k:        307200.0M         55442496  4.2BSD   4096 32768    1 # /home
Use (A)uto layout, (E)dit auto layout, or create (C)ustom layout? [a] a
/dev/rwd1a: 1024.0MB in 2097152 sectors of 512 bytes
6 cylinder groups of 202.47MB, 12958 blocks, 25984 inodes each
/dev/rwd1k: 307200.0MB in 629145600 sectors of 512 bytes
378 cylinder groups of 814.44MB, 26062 blocks, 52224 inodes each
newfs: warning: fsck_ffs will need 112MB; min(ulimit -dH,physmem) is 111MB
/dev/rwd1d: 4096.0MB in 8388608 sectors of 512 bytes
21 cylinder groups of 202.47MB, 12958 blocks, 25984 inodes each
/dev/rwd1f: 2048.0MB in 4194304 sectors of 512 bytes
11 cylinder groups of 202.47MB, 12958 blocks, 25984 inodes each
/dev/rwd1g: 1024.0MB in 2097152 sectors of 512 bytes
6 cylinder groups of 202.47MB, 12958 blocks, 25984 inodes each
/dev/rwd1h: 10240.0MB in 20971520 sectors of 512 bytes
51 cylinder groups of 202.47MB, 12958 blocks, 25984 inodes each
/dev/rwd1j: 2048.0MB in 4194304 sectors of 512 bytes
11 cylinder groups of 202.47MB, 12958 blocks, 25984 inodes each
/dev/rwd1i: 2048.0MB in 4194304 sectors of 512 bytes
11 cylinder groups of 202.47MB, 12958 blocks, 25984 inodes each
/dev/rwd1e: 4319.8MB in 8846848 sectors of 512 bytes
22 cylinder groups of 202.47MB, 12958 blocks, 25984 inodes each
Which disk do you wish to initialize? (or 'done') [done]
/dev/wd1a (3a4cf22a9ab83757.a) on /mnt type ffs (rw, asynchronous, local)
/dev/wd1k (3a4cf22a9ab83757.k) on /mnt/home type ffs (rw, asynchronous, local, n
odev, nosuid)
/dev/wd1d (3a4cf22a9ab83757.d) on /mnt/tmp type ffs (rw, asynchronous, local, no
dev, nosuid)
/dev/wd1f (3a4cf22a9ab83757.f) on /mnt/usr type ffs (rw, asynchronous, local, no
dev)
/dev/wd1g (3a4cf22a9ab83757.g) on /mnt/usr/X11R6 type ffs (rw, asynchronous, loc
al, nodev)
/dev/wd1h (3a4cf22a9ab83757.h) on /mnt/usr/local type ffs (rw, asynchronous, loc
al, nodev)
/dev/wd1j (3a4cf22a9ab83757.j) on /mnt/usr/obj type ffs (rw, asynchronous, local
, nodev, nosuid)
/dev/wd1i (3a4cf22a9ab83757.i) on /mnt/usr/src type ffs (rw, asynchronous, local
, nodev, nosuid)
/dev/wd1e (3a4cf22a9ab83757.e) on /mnt/var type ffs (rw, asynchronous, local, no
dev, nosuid)
dev)
/dev/wd1g (3a4cf22a9ab83757.g) on /mnt/usr/X11R6 type ffs (rw, asynchronous, loc
al, nodev)
/dev/wd1h (3a4cf22a9ab83757.h) on /mnt/usr/local type ffs (rw, asynchronous, loc
al, nodev)
/dev/wd1j (3a4cf22a9ab83757.j) on /mnt/usr/obj type ffs (rw, asynchronous, local
, nodev, nosuid)
/dev/wd1i (3a4cf22a9ab83757.i) on /mnt/usr/src type ffs (rw, asynchronous, local
, nodev, nosuid)
/dev/wd1e (3a4cf22a9ab83757.e) on /mnt/var type ffs (rw, asynchronous, local, no
dev, nosuid)
```

Il nous faut configurer les _sets_. Les choix par défaut sont très bien.

> http

> none

> ftp.fr.openbsd.org

> pub/OpenBSD/6.0/amd64

```
Let's install the sets!
Location of sets? (cd0 disk http or 'done') [http]
HTTP proxy URL? (e.g. 'http://proxy:8080', or 'none') [none]
HTTP Server? (hostname, list#, 'done' or '?') [ftp.fr.openbsd.org]
Server directory? [pub/OpenBSD/6.0/amd64]
```

Ici, nous désactivons certains _sets_. Mais surtout, nous activons le _set_ **bsd.mp**. Celui-ci est nécessaire pour l'utilisation d'un processeur multi-coeurs.

> -g* -x*

> bsd.mp

> done

> done

```
Select sets by entering a set name, a file name pattern or 'all'. De-select
sets by prepending a '-' to the set name, file name pattern or 'all'. Selected
sets are labelled '[X]'.
    [X] bsd           [X] base60.tgz    [X] game60.tgz    [X] xfont60.tgz
    [X] bsd.rd        [X] comp60.tgz    [X] xbase60.tgz   [X] xserv60.tgz
    [ ] bsd.mp        [X] man60.tgz     [X] xshare60.tgz
Set name(s)? (or 'abort' or 'done') [done] -g* -x*
    [X] bsd           [X] base60.tgz    [ ] game60.tgz    [ ] xfont60.tgz
    [X] bsd.rd        [X] comp60.tgz    [ ] xbase60.tgz   [ ] xserv60.tgz
    [ ] bsd.mp        [X] man60.tgz     [ ] xshare60.tgz
Set name(s)? (or 'abort' or 'done') [done] bsd.mp
    [X] bsd           [X] base60.tgz    [ ] game60.tgz    [ ] xfont60.tgz
    [X] bsd.rd        [X] comp60.tgz    [ ] xbase60.tgz   [ ] xserv60.tgz
    [X] bsd.mp        [X] man60.tgz     [ ] xshare60.tgz
Set name(s)? (or 'abort' or 'done') [done]
Get/Verify SHA256.sig   100% |**************************|  2152       00:00
Signature Verified
Get/Verify bsd          100% |**************************| 10258 KB    00:10
Get/Verify bsd.rd       100% |**************************|  7720 KB    00:08
Get/Verify bsd.mp       100% |**************************| 10307 KB    00:11
Get/Verify base60.tgz   100% |**************************| 54422 KB    00:57
Get/Verify comp60.tgz   100% |**************************| 49910 KB    00:53
Get/Verify man60.tgz    100% |**************************|  8617 KB    00:09
Installing bsd          100% |**************************| 10258 KB    00:01
Installing bsd.rd       100% |**************************|  7720 KB    00:00
Installing bsd.mp       100% |**************************| 10307 KB    00:01
Installing base60.tgz   100% |**************************| 54422 KB    02:09
Extracting etc.tgz      100% |**************************|   186 KB    00:00
Installing comp60.tgz   100% |**************************| 49910 KB    01:50
Installing man60.tgz    100% |**************************|  8617 KB    00:24
Location of sets? (cd0 disk http or 'done') [done]
Saving configuration files...done.
Making all device nodes...done.
```

Bravo ! Votre installation OpenBSD est un succès !
Ce n'est cependant pas terminé.

```
CONGRATULATIONS! Your OpenBSD install has been successfully completed!
To boot the new system, enter 'reboot' at the command prompt.
When you login to your new system the first time, please read your mail
using the 'mail' command.

#
```

Nous devons dorénavant configurer notre carte réseau.

> cd /mnt/etc

> ls /mnt/etc/hostname*

> cat /mnt/etc/hostname.em0

> echo inet 5.39.xx.xx 255.255.255.0 5.39.xx.255 > /mnt/etc/hostname.em0

> cat /mnt/etc/hostname.em0

> echo 5.39.xx.254 > /mnt/etc/mygate

> cat /mnt/etc/mygate

> echo lookup file bind > /mnt/etc/resolv.conf

> echo nameserver 213.186.33.99 >> /mnt/etc/resolv.conf

puis activer la gestion multi-coeurs.

> ls -l /mnt/bsd*

```
# ls -l /mnt/bsd*
-rw-r--r--  1 root  wheel  10504752 Mar 26 06:42 /mnt/bsd
-rw-r--r--  1 root  wheel  10554411 Mar 26 06:42 /mnt/bsd.mp
-rw-r--r--  1 root  wheel   7905392 Mar 26 06:42 /mnt/bsd.rd
```

> mv /mnt/bsd /mnt/bsd.sp

> cp /mnt/bsd.mp /mnt/bsd

Puis terminons par un **halt**.

> halt

```
# cd /mnt/etc
# ls /mnt/etc/hostname*
hostname.em0
# cat /mnt/etc/hostname.em0
dhcp
# echo inet 5.39.xx.xx 255.255.255.0 5.39.83.255 > /mnt/etc/hostname.em0
# cat /mnt/etc/hostname.em0
inet 5.39.xx.xx 255.255.255.0 5.39.xx.255
# echo 5.39.83.254 > /mnt/etc/mygate
# cat /mnt/etc/mygate
5.39.xx.254
# echo lookup file bind > /mnt/etc/resolv.conf
# echo nameserver 213.186.33.99 >> /mnt/etc/resolv.conf
# mv /mnt/bsd /mnt/bsd.sp
# cp /mnt/bsd.mp /mnt/bsd
# halt
syncing disks... done

The operating system has halted.
Please press any key to reboot.
```

A présent, il nous faut nous rendre une fois de plus sur notre interface Kimsufi et spécifier un démarrage sur le disque-dur puis effectuer à nouveau un redémarrage à froid de notre serveur.

Une fois celui-ci redémarré, connectons-nous une fois de plus à celui-ci en SSH à l'aide de notre fidèle compagnon PuTTY mais cette fois-ci avec l'utilisateur que nous avons spécifié plus haut.

```
login as: username
username@5.39.xx.xx's password:
OpenBSD 6.0 (GENERIC) #2148: Tue Jul 26 12:55:20 MDT 2016

Welcome to OpenBSD: The proactively secure Unix-like operating system.

Please use the sendbug(1) utility to report bugs in the system.
Before reporting a bug, please try to reproduce it with the latest
version of the code.  With bug reports, please try to ensure that
enough information to reproduce the problem is enclosed, and if a
known fix for it exists, include that as well.

$
```

## Ressource(s)
* [blog.meseira.net](https://blog.meseira.net/post/2016/12/21/install-openbsd-kimsufi/)
* [ozgur.kazancci.com](https://ozgur.kazancci.com/2017/01/03/install-openbsd-6-0-on-kimsufisoyoustart-dedicated-server-without-ipmikvm/)
* [nipil.org](https://nipil.org/2014/11/10/openbsd-5.6-sur-kimsufi-2g.html)

# Installer et configurer Nano

Tout d'abords, installons Nano.

> pkg_add nano

Nous cherchons ensuite où est caché Nano.

> find / -name "nano"

```
# find / -name "nano"
/usr/local/bin/nano
/usr/local/share/doc/nano
/usr/local/share/examples/nano
/usr/local/share/nano
```

Puis copions un exemple de fichier de configure dans le répertoire home de l'utilisateur souhaité.

> cp /usr/local/share/examples/nano/nanorc.sample ~/.nanorc

Et enfin nous éditions ce fameux fichier de configuration.

> nano ~/.nanorc

Dans ce fichier, nous pouvons par exemple activer les choses suivantes.

Activer l'auto-indentation avec `set autoindent`.

```
## Use auto-indentation.
set autoindent
```

Activer la souris en mode console dans Nano (si disponible par l'OS) avec `set mouse`.

```
## Enable mouse support, if available for your system.  When enabled,
## mouse clicks can be used to place the cursor, set the mark (with a
## double click), and execute shortcuts.  The mouse will work in the X
## Window System, and on the console when gpm is running.
set mouse
```

Puis on active la coloration syntaxique avec `include "/usr/local/share/nano/*.nanorc"`. Cette option est géniale car elle permet d'inclure toutes les colorations en une seule fois.

```
## to set the background color to black or white.
##
## All regexes should be extended regular expressions.
##
## If you wish, you may put your syntax definitions in separate files.
## You can make use of such files as follows:
##
## include "/path/to/syntax_file.nanorc"
##
## Unless otherwise noted, the name of the syntax file (without the
## ".nanorc" extension) should be the same as the "short description"
## name inside that file.  These names are kept fairly short to make
## them easier to remember and faster to type using nano's -Y option.
##
## To include all existing syntax definitions, you can do:
include "/usr/local/share/nano/*.nanorc"
```

Et on enregistre avec la combinaison de touche `CTRL + X` et on répond `Y`.

Pour afficher le nombre de ligne dans Nano, on utilise la commande suivante `nano -c`.

> nano -c ~/.nanorc

# Configurer DOAS

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

# Configurer SSH

> nano /etc/ssh/sshd_config

Changer le port de SSH `Port 222`.

Interdire la connexion SSH en root `PermitRootLogin no`.

Autoriser 6 tentatives de connexions maximum `MaxAuthTries 6`.

Limiter à 10 le nombre de session `MaxSessions 10`.

Puis recharger la configuration.

> rcctl reload sshd

Et bien entendu, nous devons nous déconnecter et reconnecter (sur le port 222 à présent).

# Configurer PacketFilter

> nano /etc/pf.conf

```
#       $OpenBSD: pf.conf,v 1.54 2014/08/23 05:49:42 deraadt Exp $
#
# See pf.conf(5) and /etc/examples/pf.conf

## MACRO ##
ext_if = "em0"
port_ssh = "22"
port_ftp = "21"
set skip on lo
set limit table-entries 400000

## TABLES ##
table <ssh_abuse> persist
table <ftp_abuse> persist

## Protection contre l'usurpation de l'adresse IP  ##
antispoof for $ext_if

block return    # block stateless traffic
pass            # establish keep-state

# By default, do not permit remote connections to X11
block return in on ! lo0 proto tcp to port 6000:6010

## SSH - Anti BruteForce ##
block in log quick proto tcp from <ssh_abuse> to any port $port_ssh
pass in on $ext_if proto tcp to any port $port_ssh flags S/SA keep state (max-src-conn 5, max-src-conn-rate 5/60, $

## FTP - Anti BruteForce ##
block in log quick proto tcp from <ftp_abuse> to any port $port_ftp
pass in on $ext_if proto tcp to any port $port_ftp flags S/SA keep state (max-src-conn 5, max-src-conn-rate 5/60, $
```

Afficher les flux en temps réel
> tcpdump -n -e -ttt -i pflog0

Afficher les logs
> tcpdump -n -e -ttt -r /var/log/pflog

Afficher les IP de la blackliste
> pfctl -t ssh_abuse -T show

Pour supprimer une adresse IP de la blackliste
> pfctl -t ssh_abuse -T delete xxx.xxx.xxx.xxx

Vider les règles du pare feu
> pfctl -F all

Vérifier la configuration sans l'injecter
> pfctl -nf /etc/pf.conf

Recharger à chaud la configuration
> pfctl -f /etc/pf.conf

# Installer le serveur de messagerie Prosody (XMPP)

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

> prosodyctl deluser user@dodoritfort.xyz

Documentation : http://prosody.im/doc/creating_accounts

## 5. Configurer le mode SSL

Commençons par générer le certificat.

> prosodyctl cert generate votredomaine.fr

```
# prosodyctl cert generate dodoritfort.xyz
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

# Installer et configurer un serveur HTTP + SSL et PHP 7

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
# certbot certonly --webroot --rsa-key-size 4096 -w /var/www/htdocs/nextcloud -d cloud.votredomaine.fr

IMPORTANT NOTES:
 - Congratulations! Your certificate and chain have been saved at
   /etc/letsencrypt/live/cloud.votredomaine.fr/fullchain.pem. Your
   cert will expire on 2017-06-24. To obtain a new or tweaked version
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

# 8. Installer NextCloud

Pour fonctionner, NextCloud a besoin de certaines dépendances PHP. Nous allons donc procéder à leur installation.

> pkg_add -i php-zip-7.0.8p0 php-gd-7.0.8p0 php-curl-7.0.8p0

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
        root "/htdocs/cloud/"
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

# Installer les headers X manquants

Il faut effectuer ces manipulations en root.

Nous téléchargeons les fichiers nécessaires.

> wget http://ftp.belnet.be/pub/OpenBSD/6.0/amd64/xbase60.tgz

```
# wget http://ftp.belnet.be/pub/OpenBSD/6.0/amd64/xbase60.tgz
--2017-03-26 19:50:08--  http://ftp.belnet.be/pub/OpenBSD/6.0/amd64/xbase60.tgz
Resolving ftp.belnet.be (ftp.belnet.be)... 193.190.67.98, 2001:6a8:3c80:2::21
Connecting to ftp.belnet.be (ftp.belnet.be)|193.190.67.98|:80... connected.
HTTP request sent, awaiting response... 200 OK
Length: 23522236 (22M) [application/x-gzip]
Saving to: 'xbase60.tgz'

xbase60.tgz                  100%[=============================================>]  22.43M  9.31MB/s    in 2.4s

2017-03-26 19:50:10 (9.31 MB/s) - 'xbase60.tgz' saved [23522236/23522236]
```

> wget http://ftp.belnet.be/pub/OpenBSD/6.0/amd64/xshare60.tgz

```
# wget http://ftp.belnet.be/pub/OpenBSD/6.0/amd64/xshare60.tgz
--2017-03-26 19:50:13--  http://ftp.belnet.be/pub/OpenBSD/6.0/amd64/xshare60.tgz
Resolving ftp.belnet.be (ftp.belnet.be)... 193.190.67.98, 2001:6a8:3c80:2::21
Connecting to ftp.belnet.be (ftp.belnet.be)|193.190.67.98|:80... connected.
HTTP request sent, awaiting response... 200 OK
Length: 4507096 (4.3M) [application/x-gzip]
Saving to: 'xshare60.tgz'

xshare60.tgz                 100%[=============================================>]   4.30M  7.86MB/s    in 0.5s

2017-03-26 19:50:13 (7.86 MB/s) - 'xshare60.tgz' saved [4507096/4507096]
```

Puis on extrait...

> tar -C / -xzphf xbase60.tgz

> tar -C / -xzphf xshare60.tgz

Et enfin, on supprime les fichiers téléchargés et inutiles à présent.

> rm xbase60.tgz xshare60.tgz
