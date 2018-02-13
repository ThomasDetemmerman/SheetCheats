# named.conf
**te vinden in:** `/etc/named.conf`

## options
```bash
options {
  directory "/var/named";  #dir met zonefiles dewelke dns records bevatten.
  forwarders { [ips]; }     #lijst van dns'en die mogelijks het antwoord weten als jij het niet weet. wordt geraadpleegd alvorens de root wordt geraadpleegd.
  allow-transer { [ips]; };   #lijst van secundaire (slave) servers die een zone transfer mogen uitvoeren.
};
```

## zone bij een primaire DNS
```bash
zone "iii.hogent.be" {
  type master;
  file "iii.hogent.be";
  allow-update { [ips]; };  #optioneel, lijst van ip's die updates mogen uitvoeren aan het bestand iii.hogent.be
  allow-transer { [ips]; };  #optioneel, overschrijft de allow-transfer gedefnieerd in options. voor deze specifieke zone.
};
```

## zone bij een secundaire DNS
```bash
zone "iii.hogent.be" {
  type slave;
  file "iii.hogent.be";
  masters { [ip master]; }  #spreekt voor zich
};
```

## zone voor reverse DNS
```bash
zone "16.168.192.in-adr.arpa"{    #omgekeerde CIDR notatie van 192.168.16/24
  type [master|slave];
  file "192.168.192"
  masters { [ip master]; }  #enkel voor slave
};
```

## zone naar root
```bash
zone "." {
  type hint; #wordt enkel gebruikt in combi met zonenaam "."
  file "named.ca"; #bevat een lijst met rootservers
}
```
*named.ca moet je in de praktijk niet aanpassen*

# DNS record files

## zone.domain.toplevel

**bijvoorbeeld:** iii.ugent.be

```bash
[client name].[subdomain] IN A  [ip] #aangezien zones over meerdere subdomeinen verspreid mogen zijn mogen we ook kleinkinderen definieren.
[client name]             IN A  [ip]
```

## delegeren van subdomeinen

De volgende lijnen code moeten toegevoegd worden aan de **parent**
```bash
[subdomain]             IN NS [dnsserver].[subdomain]  #het [subdomain] valt onder de verantwoordelijkheid van DNS   [dnsserver].[subdomain]
[dnsserver].[subdomain] IN A [ip] #en deze heeft volgend ip
```

## config van een zone
```bash
;/var/named/[subsubdomain].[subdomain].ugent.be
@   IN SOA [dnsservername].[subsubdomain].[subdomain].ugent.be. ( #merk de punt op achter be!!
    [int]; #serial
    [int];  refresh
    [int]; retry
    [int]; expire
    [int]; minimum
    )
            IN NS [dnsservername] #voorafgegaan door een tap of spatie(s)
[client]    IN A [ip]
[client]    IN A [ip]
[client]    IN A [ip]

)
```

## reverse DNS zone
```bash
;/var/named/192.168.16
17 IN PTR [client].[subsubdomain].[subdomain].ugent.be. #De 17 slaat op het laatste cijfer van het ip dus zijn volledig ip zal zijn: 192.168.16.17
```