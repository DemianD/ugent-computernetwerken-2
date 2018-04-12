# Reek C - Overgang naar IPv6 en autoconfiguratie

## Bespreek de belangrijkste motieven die uiteindelijk een overgang naar IPv6 zullen veroorzaken. (§5.1)

* Beperkte adresruimte
  * IPv4 is 32 bits, en kent maar 4 miljoen adressen
  * Door gebruik te maken van een hierarchisch adressensysteem (CIDR) zal een deelvan deze adressenruimte ook onbruikbaar zijn.
* Grote routingtabellen
  * CIDR laat hierarchische aggregatie van routes toe
    * Te laat ingevoerd waardoor tier 1 ISP's verantwoordelijk zijn voor meerdere niet aggregeerbare blokken en hun default-free routers gigantische routingtabellen kennen.
* Pakketstructuur
  * IPv4 gebruikt opties van variabele grootte als onderdeel van de header
    * Hierdoor moeten alle routers de opties verwerken.
* Beveiliging
  * IPv4 heeft geen functionaliteit voor beveiliging
  * Er dient gebruikt gemaakt te worden van hogere protocollagen.
    * Hierdoor is heel wat informatie, zoals de header onbeschermd.
* Mobiliteit
  * IPv4 kent geen mogelijkheden voor mobiliteit
  * De configuratie van IPv4 is steeds statisch.

<p style="page-break-after:always;"></p>
## Hoe zal deze overgang worden gerealizeerd ? Van welke technieken zal men gebruik maken ? Je moet hierbij onder andere tunneling in algemene termen beschrijven, echter zonder dieper in te gaan op de praktische uitwerking ervan. (§5.7 zonder subsecties)
### Aanpassingen
* Op subnetwerkniveau enkel verwijzing naar hogere protocollaag aanpassen (ethernet frames)
* Hogere protocollagen moeten niet aangepast worden, tenzij voor grotere adreslengte (checksum binnen tcp header)
* De gerelateerde protocollen dienen ook aangepast te worden:
    * ARP en IGMP verdwijnen
    * ICMP zal aangepast worden naar ICMPv6
* Ook hogere protocollen dienen aangepast te worden (DHCP en DNS)

### Realisatie

Deze moet geleidelijk aan gebeuren met een aantal voorwaarden:

* IPv4 knooppunten moeten op een willekeurig moment geupgrade kunnen worden.
* Alleen-IPv6 knooppunten moeten onafhankelijk van andere knooppunten toegevoegd kunnen worden.
* Bestaande alleen-IPv4 knooppunten moeten hun IPv4 adres kunnen blijven gebruiken (Dit zijn IPv6 knooppunten gemapt als `::FFFF:w.x.y.z`)
* Alle IPv6 knooppunten moeten interoperabel blijven met IPv4 systemen.

Om de overgang eenvoudig te realiseren wordt gebruikt gemaakt van volgende technieken:

* Dual-IP of IPv6/IPv4 systemen
  * Deze systemen ondersteunen beide protocollen.
  * Gerealiseerd met Dual-Stack systemen, waarbij meestal ook de transportlaag gesplitst is.
* IPv6 over IPv4 tunneling
  * Hierbij worden IPv6 datagrammen ingekapseld in IPv4 pakketten.
  * Protocolidentificatie 41
  * De ontvanger zal dan de IPv4 header verwijderen om de IPv6 inhoud te verwerken.
  * Zowel zender als ontvanger dienen dus dual-stack systemen te zijn.
* Protocol of Header translation
  * IPv6/IPv4 routers zetten IPv4 headers om in een IPv6 header
  * Dit met behulp van de IPv4 gemapte adressen.

<p style="page-break-after:always;"></p>
## Geef de alternatieve mogelijkheden voor autoconfiguratie in IPv6. Bespreek hierbij de opeenvolgende stappen. (§5.6)
### Identificatie Begrippen
* Tentatief
    * Adres is vastgelegd maar niet geverifieerd
    * Controle gebeurt door Duplicate Address Detection
    * Tijdens deze fase kan de client alleen multicast adressen ontvangen.
* Valid
    * Eenmaal een uniek adres vastgelegd en bevestigd is, kan de client ook unicast data verzenden/ontvangen.
    * Aangezien de levensduur van een adres beperkt is, bevindt de client zich steeds in een Preffered of Deprecated toestand.
* Invalid
    * Komt voor als levensduur verstreken is
    * Client kan geen multicast en geen unicast verkeer verwerken.

### Stateless autoconfiguratie

* Client zal autonoom zijn configuratie bepalen zonder beroep te doen op een server
* Interface ID wordt bepaald op basis van de IEEE EUI-64 standaard
* De netwerkprefix kan worden bekomen door router discovery
* Dit is enkel mogelijk voor niet-routers.

Werking:

* Bepalen tentatief linklokaal unicast adres
  * Gebeurt op basis van MAC-adres
  * Zorgt voor de mogelijkheid voor communicatie op het lokale subnet.
* Initialisatie interface
  * Tentatief linklocaal unicast adres gebruikt
  * Multicast MAC-adres overeenkomstig met het solicited-node multicast adres wordt geregistreerd (eerste 3 bytes in MAC adres vervangen door 33-33-ff)
* Routes detecteren
  * Gebeurt door 3 Router Sollicitation berichten te sturen
  * Bij ontvangen Router Advertisement berichten wordt de configuratie voor Hop Limit, Reachable Time, Retrans Timer en MTU er uit afgeleid.
  * Indien niets ontvangen wordt overgeschakeld op stateful autoconfiguratie.
* Bepalen nieuw adres
  * Op basis van de ontvangen Prefix Information wordt een nieuw tentatief unicast adres berkeend (opnieuw met Duplicate Address Detection)
  * Indien de prefixlengte overeenkomt met een publiek subnetwerk, wordt dit gebasseerd op een random MAC adres.
* Stateful configuratie
  * Indien de Managed Address Configuration valg in het router Advertisemenet bericht aan staat, wordt een stateful autoconfiguratie uitgevoerd om bijkomende adressen te bekomen.

<p style="page-break-after:always;"></p>
### Stateful autoconfiguratie
* Dit configuratiemechanisme maakt gebruik van een centrale server die toestandsinformatie bijhoudt van alle knooppunten.
* Biedt zo een volledige controle over het adresseringsschema (ook interface-id's)
* Een dergelijke configuratie kan bekomen worden via het DHCPv6 protocol.
* Enkele wijzigingen van DHCPv6 t.o.v. DHCP:
    * Niet compatibel met het BOOTP protocol
    * Interactie (servers, relay-agents, ...) gebeurt niet meer via broadcasting, maar via multicasting met een aantal permanente multicast adressen.
    * Kan op stateless autoconfiguratie steunen om stateful autoconfiguratie te vereenvoudigen.
    * Bepaalde opties worden niet langer ondersteind (bv optie 3)
    * Kan aan dezelfde interface meerdere IPv6 adressen toewijzen.
    * Toevoeging van het DHCP Reconfigure berichttype om op het initiatief van de server configuratiewijzigingen naar clients te sturen.
    * Adres deprecation kan gebruikt worden om netwerken dynamisch te hernummeren.
