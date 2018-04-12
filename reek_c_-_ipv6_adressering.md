# Reek C - IPv6 Adressering

## Bespreek de structuur en numerieke voorstelling van IPv6 adressen.

* Een IPv6 bericht bestaat uit 128 bits (16 bytes)
  * Biedt een groot adresbereik aan, zodat protocollen als NAT overbodig worden.
* Basisrepresentatie: P:Q:R:S:T:U:V:W
* Notatie:
  * Voorloopnullen zijn overbodig:
    * `FFE1:1:... == FFE1:0001:...`
  * Opeenvolgende `[:]0000[:]` mogen vervangen worden door `::`
    * `FFE1::1EFF == FFE1:0000:0000:0000:0000:0000:0000:0000:1EFF`
  * Willekeurig 2 byte segment mag voorgesteld worden via dotted-decimal notatie:
    * `FE80::5EFE:192.168.41.30 == FE80::5EFE:C0A8:291E`
* Elk adres bestaat uit netwerkprefix en een interface id
  * Enkel prefixlengte syntax kan gebruikt worden: `2001:410:1::/48`

## Geef en bespreek de verschillende types IPv6 adressen. Geef onder andere van elk van deze types de structuur, hun interpretatie, relevante voorbeelden en eventuele subtypes.

### Globale Unicast adressen

* Formaatprefix (eerste 3 bits) 001
* Globaal uniek
* IPv4 = netwerkprefix en interfaceId
* IPv6 kan nog verder opgesplitst worden in hierarchische niveaus
  * Top-Level aggregation Identifier
    * 13 bit veld
    * hoogste niveau (toegewezen aan tier 1 isp's)
    * Ten hoogste 8192 routes met prefixlengte /16 in default-free routes
  * Next-Level Aggregation Identifier
    * 24 bit veld
    * op provider niveau toegewezen
    * Vormt samen met de TLA de publieke topologie van het internet
  * Site-Level Aggregation Identifier
    * 16 bit veld
    * Klant kan zo topologie afstemmen op structuur van zijn privaat netwerk
    * Door de grootte van dit veld kan oude IPv4 structuur herbruikt wordem om SLA te vormen
  * Interface ID
    * 64 bit veld
    * Random bepaald
    * Kan ook eenduidig afgeleid worden uit het unieke MAC-adres (6 bytes) van de netwerkkaart
      * Omzetting op basis van IEEE EUI-64 formaat
      * Eerste 3 bytes van MAC + FF:FE + Laatste 3 bytes van MAC
      * Eventueel wordt laatste bit gecomplementeerd zodat deze op 0 (vereist bij unicast MAC-adres)

### Lokale Unicast adressen

* Dienen voor intern gebruik binnen een organisatie
* Worden niet doorgestuurd door routers buiten de organisatie
* 2 soorten:
  * Sitelokale adressen
    * Syntax: Formaatprefix FEC0:0:0/48
    * Vrij te kiezen SLA veld (maakt subnetting mogelijk)
    * Gebruikt voor verwerken verkeer, zonder dat versturen naar het internet mogelijk is
    * Te vergelijken met IPv4 private adresblokken 10/8, 172.16/12, 192.168/16
  * Linklokale adressen
    * Syntax: Formaatprefix FE80:0:0:0/64
    * Gebruikt om knooppunten op een enkele netwerkverbinding te nummeren
    * Laten geen subnetting toe
    * Te vergelijken met IPv4 169.254/16 APIPA adressen
    * Worden automatisch toegewezen aan elke interface

### Multicast adressen

* Relatief ongewijzigd t.o.v. IPv4
* Netwerkinterface wordt geconfigureerd met een lijst MAC-Multicast adressen
  * Deze moeten doorgestuurd worden naar een hogere protocollaag
* Syntax: Formaatprefix: FFvs::
  * Vlag veld (v)
    * Bepaalt het type adres, is 4 bits lang en enkel de laagste bit is tot nu gedefinieerd
    * v = 0: Adres is een permanent adres, overige 7 bits leggen speciale betekenis vast
    * v = 1: adres is transient (tijdelijk), kan adhoc gebruikt worden en adressen van de vorm FF1s::w.x.y.z worden automatisch gemapt op ethernet, multicast adressen van de vorm 33-33-w-x-y-z
  * Scope veld (s)
    * Geeft het bereik van het multicast adres aan
      * Globaal, site-lokaal, subnet, organisatie
* Naast linklokaal unicast adres wordt ook een solicited-node multicast adres toegewezen (aan de interface)
  * Syntax: FF02::1:255x.y.z
  * x.y.z wordt bepaald door de laatste 3 bytes van het unicast IP adres
  * Zorgt er voor dat adresresolutie slechts een enkele klant verstoort en niet alle knooppunten van het netwerk

### Anycast adressen

* Kunnen gedeeld worden door meerdere knooppunten
  * Slechts ontvangen door 1 knooppunt
* Handig voor aanbieden van diensten
  * Aan elke dienst een apart anycast adres toekennen
* Client software dient exacte IP van server niet te kennen, maar kan gewoon het anycast adres aanspreken om bij de juiste server terecht te komen
* Anycast adressen worden toegewezen uit de ruimte van unicast adressen en kunnen niet onderscheiden worden.
  * Interfaces moeten dus specifiek geconfigureerd worden om bepaald anycast adres te aanvaarden
  * Routers moeten op de hoogte zijn van de locatie van elk anycast adres
* De interfaces van een router worden steeds geconfigureerd met het subnet-router anycast adres
  * Vormen door het netwerkadres aan te vullen met een interface ID dat uit allemaal nullen bestaat
  * Zo is het eenvoudig mogelijk om op een subnet een willekeurige route te bereiken door het netwerk te adresseren.
