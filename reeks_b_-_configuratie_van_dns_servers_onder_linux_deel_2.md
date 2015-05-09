# Reeks B - Configuratie van DNS servers onder Linux Deel 2
De figuur in bijlage stelt een intranet bestaand uit een aantal Linux ...(cfr.vraag B2)... achterhalen. Geen enkele zone heeft een secundaire nameserver. Je hoeft geen reverse DNS te configureren.

## Bespreek in detail het formaat van een zonebestand en zijn records. Je mag dit doen op basis van één van de oplossingen in a), doch je moet ook alternatieve records en formaten beschrijven, die je niet noodzakelijk hebt gebruikt. (§3.3.1 & §3.3.4)

```bash
$TTL 10
@	IN	SOA	vivaldi.concerti.XVIII.it. root. ( 1 1 1 1 1 )

	IN	NS 	vivaldi.concerti.XVIII.it.

vivaldi.concerti.XVIII.it.	IN	A 	192.168.16.155


; delegaties

ro	IN	NS	elgar.XX.uk
ru	IN	NS	arne.opera.XVIII.uk

arne.opera.XVIII.uk	IN	A	192.168.16.154
zelenka.sacral.XVIII.cz	IN	A	192.168.16.133
```

## Stel het configuratiebestand en alle zonebestanden op van volgende DNS servers: ... . Gebruik relatieve DNS namen waar mogelijk. Gebruik noch forwarders, noch de $ORIGIN opdracht !
