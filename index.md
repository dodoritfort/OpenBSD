## Welcome to GitHub Pages

You can use the [editor on GitHub](https://github.com/dodoritfort/OpenBSD/edit/master/index.md) to maintain and preview the content for your website in Markdown files.

Whenever you commit to this repository, GitHub Pages will run [Jekyll](https://jekyllrb.com/) to rebuild the pages in your site, from the content in your Markdown files.

### Markdown

Markdown is a lightweight and easy-to-use syntax for styling your writing. It includes conventions for

```markdown
Syntax highlighted code block

# Header 1
## Header 2
### Header 3

- Bulleted
- List

1. Numbered
2. List

**Bold** and _Italic_ and `Code` text

[Link](url) and ![Image](src)
```

For more details see [GitHub Flavored Markdown](https://guides.github.com/features/mastering-markdown/).

### Jekyll Themes

Your Pages site will use the layout and styles from the Jekyll theme you have selected in your [repository settings](https://github.com/dodoritfort/OpenBSD/settings). The name of this theme is saved in the Jekyll `_config.yml` configuration file.

### Support or Contact

Having trouble with Pages? Check out our [documentation](https://help.github.com/categories/github-pages-basics/) or [contact support](https://github.com/contact) and we’ll help you sort it out.








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

```
root@ns30xxxxx:~# dmesg | grep -i ^cpu
CPU: Physical Processor ID: 0
CPU: Processor Core ID: 0
CPU0: Thermal monitoring enabled (TM2)
cpuidle: using governor ladder
cpuidle: using governor menu
```

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

```
root@ns30xxxxx:~# cat /etc/resolv.conf
nameserver 127.0.0.1
nameserver 213.186.33.99
search ovh.net
```

__________

```
login as: root
root@5.39.xx.xx's password:

The programs included with the Debian GNU/Linux system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Debian GNU/Linux comes with ABSOLUTELY NO WARRANTY, to the extent
permitted by applicable law.
```

```
root@rescue:~# wget -P /tmp/ http://ftp.fr.openbsd.org/pub/OpenBSD/6.0/amd64/min                                                                                                                                              iroot60.fs
--2017-03-16 17:21:30--  http://ftp.fr.openbsd.org/pub/OpenBSD/6.0/amd64/miniroo                                                                                                                                                             t60.fs
Resolving ftp.fr.openbsd.org (ftp.fr.openbsd.org)... 145.238.209.46
Connecting to ftp.fr.openbsd.org (ftp.fr.openbsd.org)|145.238.209.46|:80... conn                                                                                                                                                             ected.
HTTP request sent, awaiting response... 200 OK
Length: 4063232 (3.9M) [application/octet-stream]
Saving to: ‘/tmp/miniroot60.fs’

/tmp/miniroot60.fs  100%[=====================>]   3.88M  10.1MB/s   in 0.4s

2017-03-16 17:21:30 (10.1 MB/s) - ‘/tmp/miniroot60.fs’ saved [4063232/4063232]
```

```
root@rescue:~# wget -P /tmp/ http://ftp.fr.openbsd.org/pub/OpenBSD/6.0/amd64/SHA                                                                                                                                                            256
--2017-03-16 17:21:53--  http://ftp.fr.openbsd.org/pub/OpenBSD/6.0/amd64/SHA256
Resolving ftp.fr.openbsd.org (ftp.fr.openbsd.org)... 145.238.209.46
Connecting to ftp.fr.openbsd.org (ftp.fr.openbsd.org)|145.238.209.46|:80... conn
ected.
HTTP request sent, awaiting response... 200 OK
Length: 1989 (1.9K) [application/octet-stream]
Saving to: ‘/tmp/SHA256’

/tmp/SHA256         100%[=====================>]   1.94K  --.-KB/s   in 0.03s

2017-03-16 17:21:53 (59.5 KB/s) - ‘/tmp/SHA256’ saved [1989/1989]
```

```
root@rescue:~# sha256sum /tmp/miniroot60.fs; grep miniroot60.fs /tmp/SHA256 | cu                                                                                                                                                             t -d ' ' -f 4
a6ff63e72ed9f45a211b0a57fb566e6f674242988e369209cd6b2a205d8bcec1  /tmp/miniroot6                                                                                                                                                             0.fs
a6ff63e72ed9f45a211b0a57fb566e6f674242988e369209cd6b2a205d8bcec1
```

```
root@rescue:~# cd /tmp
```

```
root@rescue:/tmp# qemu-system-x86_64 -curses -drive file=miniroot60.fs -drive fi                                                                                                                                                             le=/dev/sda -net nic -net user
```

__________

```
Welcome to the OpenBSD/amd64 6.0 installation program.
(I)nstall, (U)pgrade, (A)utoinstall or (S)hell? i
At any prompt except password prompts you can escape to a shell by
typing '!'. Default answers are shown in []'s and are selected by
pressing RETURN.  You can exit this program at any time by pressing
Control-C, but this can leave your system in an inconsistent state.
```

```
Choose your keyboard layout ('?' or 'L' for list) [default] L
Available layouts: be br cf de dk es fr hu is it jp la lt lv nl no pl pt ru sf s
g si sv tr ua uk us
Choose your keyboard layout ('?' or 'L' for list) [default] us
kbd: keyboard mapping set to us
```

```
System hostname? (short form, e.g. 'foo') hostname
```

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

```
Password for root account? (will not echo)
Password for root account? (again)
```

```
Start sshd(8) by default? [yes]
Do you expect to run the X Window System? [yes] no
Change the default console to com0? [no]
```

```
Setup a user? (enter a lower-case loginname, or 'no') [no] username
Full name for user username? [username]
Password for user username? (will not echo)
Password for user username? (again)
```

```
WARNING: root is targeted by password guessing attacks, pubkeys are safer.
Allow root ssh login? (yes, no, prohibit-password) [no]
```

```
What timezone are you in? ('?' for list) [Europe/Paris]
```

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

```
Let's install the sets!
Location of sets? (cd0 disk http or 'done') [http]
HTTP proxy URL? (e.g. 'http://proxy:8080', or 'none') [none]
HTTP Server? (hostname, list#, 'done' or '?') [ftp.fr.openbsd.org]
Server directory? [pub/OpenBSD/6.0/amd64]
```

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

```
CONGRATULATIONS! Your OpenBSD install has been successfully completed!
To boot the new system, enter 'reboot' at the command prompt.
When you login to your new system the first time, please read your mail
using the 'mail' command.

#
```

```
# cd /mnt/etc
# ls hostname*
hostname.em0
# cat hostname.em0
dhcp
# echo inet 5.39.xx.xx 255.255.255.0 5.39.83.255 > hostname.em0
# cat hostname.em0
inet 5.39.xx.xx 255.255.255.0 5.39.83.255
# echo 5.39.83.254 > mygate
# cat mygate
5.39.83.254
# halt
syncing disks... done

The operating system has halted.
Please press any key to reboot.
```

__________

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
