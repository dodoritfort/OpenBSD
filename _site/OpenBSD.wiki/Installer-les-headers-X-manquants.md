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