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

## De figuur in bijlage stelt een intranet voor bestaand uit een aantal Linux computers. Alle computers hebben een IP-adres van de vorm 172.x.y.z . De getallen x en y lees je af rechts van de naam van de computer. Het getal z lees je af links van de naam van de computer. De computers staan gegroepeerd in een tabel met als header de naam van het domein waarin ze zich bevinden. De recht­hoeken die domeinen groeperen stellen dan weer een zone voor. Stippel­lijnen duiden op een domein/sub­domein relatie. De pijlen laten toe om de primaire name­server van elke forward DNS zone te achter­halen. De reverse DNS van de volgende adresruimten wordt gedelegeerd aan: ... . Geen enkele zone heeft een secundaire nameserver. Stel het configuratiebestand en de zonebestanden op van alle servers die een rol spelen bij de reverse DNS resolving van IP-adressen behorend tot de adresruimten  ... en ... . Je hoeft hierbij enkel de configuratie informatie te vermelden die noodzakelijk is voor reverse DNS. Gebruik relatieve DNS namen waar mogelijk. Het gebruik van forwarders is hier niet toegelaten !
Figuur??