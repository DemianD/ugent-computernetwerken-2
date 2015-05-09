# Reeks B - Configuratie van DNS servers onder Linux Deel 3
De figuur in bijlage stelt een intranet bestaand uit een aantal Linux ...(cfr.vraag B2)... achterhalen. Geen enkele zone heeft een secundaire nameserver. Je hoeft geen reverse DNS te configureren.

## Bespreek in detail het formaat van een configuratiebestand. Je mag dit doen op basis van één van de oplossingen in a), doch je moet ook alternatieve opdrachten en sleutelwoorden beschrijven, die je niet noodzakelijk hebt gebruikt. (§3.3.3)

```
options {
	directory "/var/named";
	dump-file "/var/named/data/cache_dump.db";
		statistics-file "/var/named/data/named_stats.txt";
		memstatistics-file "data/named_mem_stats.txt";
};

logging
{
		channel default_debug {
				syslog daemon;
				severity dynamic;
		};
};

zone "oratoria.XVII.it" IN {
	type master;
	file "oratoria.XVII.it.zone";
	allow-update { none; };
};

zone "XIX.it" IN {
	type master;
	file "XIX.it.zone";
	allow-update { none; };
};

zone "XX.us" IN {
	type master;
	file "XX.us.zone";
	allow-update { none; };
};

controls {
	inet * allow { localhost; 192.168.16/24; } keys { rndckey; };
};

include "/etc/rndc.key";
include "/etc/named.rfc1912.zones";
```

## Stel het configuratiebestand en alle zonebestanden op van volgende DNS servers: ... . Gebruik relatieve DNS namen waar mogelijk. Gebruik noch forwarders, noch de $ORIGIN opdracht !

