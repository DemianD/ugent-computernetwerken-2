# Reeks B - Configuratie van reverse DNS onder Linux

## Wat wordt beoogd met reverse DNS ? Hoe wordt dit gerealiseerd ? (§3.3.2)

### Wat

* Technologie die toelaat om domeinnaam te vinden voor gegeven IP
* Toegepast als beveiligingscontrole

### Hoe

* DNS PTR-type Records zorgen verantwoordelijk voor de omzetting.
* Elk IP heeft in-addr.arpa: `z.y.x.w.in-addr.arpa`, dit komt uit bij de machine die we zoeken.
* Om Reverse DNS toe te laten, moeten we primaire nameservers configureren in het zonebestand. Zie onderstaand voorbeeld.

```bash
;
;  Database file 7.10.in-addr.arpa.zone for 7.10.in-addr.arpa zone.
;  Zone version:  2
;

@                       IN  SOA corelli.sonatas.xvii.it. xavier.geerinck.ugent.be. (
                        	1            ; serial number
                        	10800        ; refresh
                        	3600         ; retry
                        	3600000      ; expire
                        	86400      ) ; default TTL

;
;  Zone NS records
;

@                       NS	corelli.sonatas.xvii.it.
1                       NS	xenakis.xx.gr.
2                       NS	delelande.orchestral.xvii.fr.

;
;  Zone records
;

11                   PTR	corelli.sonatas.xvii.it.
12                   PTR	merulo.xvi.it.
27                   PTR	nono.xx.it.
```

**named.conf**

```bash
zone "16.168.192.in-addr.arpa" {
	type master;
	file "192.168.16";
}
```

* Alle namen zijn relatief ten opzichte van zone
* 16.168.192.in-addr.arpa managed dus .z (16.168.192.z)
