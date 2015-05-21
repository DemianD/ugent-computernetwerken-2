# Reeks B - Configuratie van DNS servers onder Linux Deel 3
De figuur in bijlage stelt een intranet bestaand uit een aantal Linux ...(cfr.vraag B2)... achterhalen. Geen enkele zone heeft een secundaire nameserver. Je hoeft geen reverse DNS te configureren.

## Bespreek in detail het formaat van een configuratiebestand. Je mag dit doen op basis van één van de oplossingen in a), doch je moet ook alternatieve opdrachten en sleutelwoorden beschrijven, die je niet noodzakelijk hebt gebruikt. (§3.3.3)

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
    
    zone "iii.hogent.be" {
        type slave;
        file "iii.hogent.be";
        masters { 192.168.16.16; };
    };
    
    controls {
    	inet * allow { localhost; 192.168.16/24; } keys { rndckey; };
    };
    
    include "/etc/rndc.key";
    include "/etc/named.rfc1912.zones";

* Named gebruikt named.conf (/etc/named.conf)
* Bevat basisinstellingen voor de serverdaemon te configureren
* Vermeldt ook de namen van de zonebestanden
* Meest belangrijke keywords zijn zone en options (soort JSON structuur)
* options
    * directory padnaam 
        * geeft dir waar zonebestanden zich bevinden
    * forwarders { ip-adressen; }; 
        * Als geen DNS info gevonden, vraag aan deze IP's
        * Als ook deze geen terugvinden, vraag aan root server
    * allow-transfer { ip-adressen; }; 
        * IP's van computers die zone transfers kunnen uitvoeren (noodzakelijk voor secundaire nameservers en voor ls opdracht in nslookup)
        * Indien deze lijn ontbreekt kan elke computer een zone transfereren.
* zone
    * master
        * named is primaire ns voor deze zone
        * **file:** lijn toont bestand (gebruikt dir voor padnaam)
        * **allow-update**: toestellen die dynamische updates kunnen uitvoeren.
    * slave
        * named is secundaire NS voor deze zone
        * masters: bevat IP's van de nameservers voor dit domein
        * file: bevat lijn voor bestand.
    * stub
        * gedraagt zich zoals secundaire nameserver, maar dupliceert enkel NS records.
    * forward
        * stuurt alle aanvragen door naar andere nameservers (vermeld in forwarders lijn)
    * hint
        * gebruikt zonenaam ".",  gebruikte bestand bevat lijst van rootservers.
        * vb: `.    3600000 IN NS A.ROOT-SERVERS.NET.`


## Stel het configuratiebestand en alle zonebestanden op van volgende DNS servers: ... . Gebruik relatieve DNS namen waar mogelijk. Gebruik noch forwarders, noch de $ORIGIN opdracht !

