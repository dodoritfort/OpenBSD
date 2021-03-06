# Configurer le DNS Unbound avec cache

> ftp -o /var/unbound/etc/root.hints ftp://FTP.INTERNIC.NET/domain/named.cache

> nano /etc/monthly.local

```
# Télécharge la liste des DNS Root pour Unbound
ftp -o /var/unbound/etc/root.hints ftp://FTP.INTERNIC.NET/domain/named.cache
```

> unbound-anchor -u _unbound -a "/var/unbound/db/root.key" 

Vérifier le nombre de coeurs du CPU utilisé.

> sysctl hw.ncpu

> sysctl hw.ncpufound

```
# sysctl hw.ncpu
hw.ncpu=4
# sysctl hw.ncpufound
hw.ncpufound=4
```

Nous pourrons donc spécifier dans le fichier de configuration que notre processeur est composé de 4 coeurs.

> nano /var/unbound/etc/unbound.conf

```
# $OpenBSD: unbound.conf,v 1.7 2016/03/30 01:41:25 sthen Exp $

server:
        # Nombre de coeurs sur le CPU
        # number of threads to create. 1 disables threading. This should equal the number
        # of CPU cores in the machine. Our example machine has 4 CPU cores.
        num-threads: 4

        # Cache size
        key-cache-size: 512m

        # Increase the memory size of the cache. Use roughly twice as much rrset cache
        # memory as you use msg cache memory. Due to malloc overhead, the total memory
        # usage is likely to rise to double (or 2.5x) the total cache memory. The test
        # box has 4gig of ram so 256meg for rrset allows a lot of room for cacheed objects.
        rrset-cache-size: 256m
        msg-cache-size: 128m

        outgoing-range: 206

        # port to answer queries from
        port: 53

        # specify the interfaces to answer queries from by ip-address.  The default
        # is to listen to localhost (127.0.0.1 and ::1).  specify 0.0.0.0 and ::0 to
        # bind to all available interfaces.  specify every interface[@port] on a new
        # 'interface:' labeled line.  The listen interfaces are not changed on
        # reload, only on restart.
        interface: 127.0.0.1

        #interface: 127.0.0.1@5353      # listen on alternative port
        interface: ::1

        # Enable IPv4, "yes" or "no".
        do-ip4: yes

        # Enable IPv6, "yes" or "no".
        do-ip6: yes

        # Enable UDP, "yes" or "no".
        do-udp: yes

        # Enable TCP, "yes" or "no". If TCP is not needed, Unbound is actually
        # quicker to resolve as the functions related to TCP checks are not done.i
        # NOTE: you may need tcp enabled to get the DNSSEC results from *.edu domains
        # due to their size.
        do-tcp: yes

        # override the default "any" address to send queries; if multiple
        # addresses are available, they are used randomly to counter spoofing
        #outgoing-interface: 192.0.2.1
        #outgoing-interface: 2001:db8::53

        access-control: 0.0.0.0/0 refuse
        access-control: 127.0.0.0/8 allow
        access-control: ::0/0 refuse
        access-control: ::1 allow

        # Fichier à récupérer sur ftp://FTP.INTERNIC.NET/domain/named.cache
        # Read  the  root  hints from this file. Default is nothing, using built in
        # hints for the IN class. The file has the format of  zone files,  with  root
        # nameserver  names  and  addresses  only. The default may become outdated,
        # when servers change,  therefore  it is good practice to use a root-hints
        # file.  get one from ftp://FTP.INTERNIC.NET/domain/named.cache
        root-hints: "/var/unbound/etc/root.hints"

        harden-below-nxdomain: yes

        # Require DNSSEC data for trust-anchored zones, if such data is absent, the
        # zone becomes  bogus.  Harden against receiving dnssec-stripped data. If you
        # turn it off, failing to validate dnskey data for a trustanchor will trigger
        # insecure mode for that zone (like without a trustanchor).  Default on,
        # which insists on dnssec data for trust-anchored zones.
        harden-dnssec-stripped: yes

        harden-referral-path: yes

        # Will trust glue only if it is within the servers authority.
        # Harden against out of zone rrsets, to avoid spoofing attempts.
        # Hardening queries multiple name servers for the same data to make
        # spoofing significantly harder and does not mandate dnssec.
        harden-glue: yes

        # Enable to not answer id.server and hostname.bind queries.
        hide-identity: yes

        # Enable to not answer version.server and version.bind queries.
        hide-version: yes

        # Use 0x20-encoded random bits in the query to foil spoof attempts.
        # http://tools.ietf.org/html/draft-vixie-dnsext-dns0x20-00
        # While upper and lower case letters are allowed in domain names, no significance
        # is attached to the case. That is, two names with the same spelling but
        # different case are to be treated as if identical. This means calomel.org is the
        # same as CaLoMeL.Org which is the same as CALOMEL.ORG.
        use-caps-for-id: yes

        # Enforce privacy of these addresses. Strips them away from answers.  It may
        # cause DNSSEC validation to additionally mark it as bogus.  Protects against
        # 'DNS Rebinding' (uses browser as network proxy).  Only 'private-domain' and
        # 'local-data' names are allowed to have these private addresses. No default.
        private-address: 192.168.0.0/16
        private-address: 172.16.0.0/12
        private-address: 10.0.0.0/8

        # Allow queries to nsd on 127.0.0.1
        # IMPORTANT FOR TESTING: If you are testing and setup NSD or BIND  on
        # localhost you will want to allow the resolver to send queries to localhost.
        # Make sure to set do-not-query-localhost: yes . If yes, the above default
        # do-not-query-address entries are present.  if no, localhost can be queried
        # (for testing and debugging).
        do-not-query-localhost: no

        # Should additional section of secure message also be kept clean of unsecure
        # data. Useful to shield the users of this validator from potential bogus
        # data in the additional section. All unsigned data in the additional section
        # is removed from secure messages.
        val-clean-additional: yes

        # Uncomment to enable qname minimisation.
        # https://tools.ietf.org/html/draft-ietf-dnsop-qname-minimisation-08
        #
        qname-minimisation: yes

        # Uncomment to enable DNSSEC validation.
        #
        auto-trust-anchor-file: "/var/unbound/db/root.key"

        # Garde les résultats en cache
        # perform prefetching of close to expired message cache entries.  If a client
        # requests the dns lookup and the TTL of the cached hostname is going to
        # expire in less than 10% of its TTL, unbound will (1st) return the ip of the
        # host to the client and (2nd) pre-fetch the dns request from the remote dns
        # server. This method has been shown to increase the amount of cached hits by
        # local clients by 10% on average.
        prefetch: yes

        # the time to live (TTL) value lower bound, in seconds. Default 0.
        # If more than an hour could easily give trouble due to stale data.
        cache-min-ttl: 3600

        # the time to live (TTL) value cap for RRsets and messages in the
        # cache. Items are not cached for longer. In seconds.
        cache-max-ttl: 86400

        # Serve zones authoritatively from Unbound to resolver clients.
        # Not for external service.
        #
        #local-zone: "local." static
        #local-data: "mycomputer.local. IN A 192.0.2.51"
        #local-zone: "2.0.192.in-addr.arpa." static
        #local-data-ptr: "192.0.2.51 mycomputer.local"

        # UDP EDNS reassembly buffer advertised to peers. Default 4096.
        # May need lowering on broken networks with fragmentation/MTU issues,
        # particularly if validating DNSSEC.
        #
        #edns-buffer-size: 1480

        # Use TCP for "forward-zone" requests. Useful if you are making
        # DNS requests over an SSH port forwarding.
        #
        #tcp-upstream: yes

        # DNS64 options, synthesizes AAAA records for hosts that don't have
        # them. For use with NAT64 (PF "af-to").
        #
        #module-config: "dns64 validator iterator"
        #dns64-prefix: 64:ff9b::/96     # well-known prefix (default)
        #dns64-synthall: no

        # the number of slabs to use for cache and must be a power of 2 times the
        # number of num-threads set above. more slabs reduce lock contention, but
        # fragment memory usage.
        msg-cache-slabs: 8
        rrset-cache-slabs: 8
        infra-cache-slabs: 8
        key-cache-slabs: 8

        # buffer size for UDP port 53 incoming (SO_RCVBUF socket option). This sets
        # the kernel buffer larger so that no messages are lost in spikes in the traffic.
#       so-rcvbuf: 1m

        # Gestion des journaux
        unwanted-reply-threshold: 10000
        logfile: /var/unbound/etc/unbound.log
        val-log-level: 2
        verbosity: 1

remote-control:
        control-enable: yes
        control-use-cert: no
        control-interface: /var/run/unbound.sock

# Use an upstream forwarder (recursive resolver) for specific zones.
# Example addresses given below are public resolvers valid as of 2014/03.
#
#forward-zone:
#       name: "."                               # use for ALL queries
#       forward-addr: 74.82.42.42               # he.net
#       forward-addr: 2001:470:20::2            # he.net v6
#       forward-addr: 8.8.8.8                   # google.com
#       forward-addr: 2001:4860:4860::8888      # google.com v6
#       forward-addr: 208.67.222.222            # opendns.com
#       forward-first: yes                      # try direct if forwarder fails
#
```

> nano /etc/weekly.local

```
# Permet de faire la vérification DNSSEC
unbound-anchor -a "/var/unbound/db/root.key"
```

> sh /etc/weekly.local

> rcctl enable unbound

> rcctl start unbound

> nano /etc/resolv.conf

```
nameserver 127.0.0.1
```

Documentation : [http://man.openbsd.org/unbound.conf](http://man.openbsd.org/unbound.conf)

Documentation : [https://www.unbound.net/documentation/unbound.conf.html](https://www.unbound.net/documentation/unbound.conf.html)

Documentation : [http://obsd4a.net/wiki/doku.php?id=network:config:unbound_dnssec](http://obsd4a.net/wiki/doku.php?id=network:config:unbound_dnssec)

Documentation : [https://calomel.org/unbound_dns.html](https://calomel.org/unbound_dns.html)

Documentation : [https://homeserver-diy.net/wiki/index.php?title=Installer_et_configurer_son_serveur_DNS_connect%C3%A9_aux_serveurs_root_avec_Unbound](https://homeserver-diy.net/wiki/index.php?title=Installer_et_configurer_son_serveur_DNS_connect%C3%A9_aux_serveurs_root_avec_Unbound)